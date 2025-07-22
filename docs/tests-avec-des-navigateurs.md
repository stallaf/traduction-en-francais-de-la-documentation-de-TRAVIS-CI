# Tests d'interfaces graphiques et de navigateurs

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#travailler-avec-sauce-labs">Travailler avec Sauce Labs</a></li>
            <li><a href="#executer-des-tests-gui">Exécuter des tests GUI avec xvfb</a></li>
            <li><a href="#mode-sans-tete-addon-chrome">Mode sans tête avec l'<i>addon</i> Chrome </a></li>
            <li><a href="#mode-sans-tete-addon-firefox">Mode sans tête avec l'addon Firefox</a></li>
            <li><a href="#utiliser-phantomjs">Utiliser PhantomJS</a></li>
            <li><a href="#exemples">Exemples</a></li>
            <li><a href="#depannage">Dépannage</a></li>
        </ul>
    </div>
</div>
</p>

Ce guide couvre les tests d'interfaces graphiques et de navigateurs sans tête en utilisant les outils fournis par [l'environnement IC](./precise.md) de Travis. La plupart du contenu est neutre du point de vue technologique et ne couvre pas tous les détails des outils de test spécifiques (comme Poltergeist ou Capybara). Nous vous recommandons de commencer par les guides [Onboarding](./embarquement.md) et [Build Configuration](./personnalisation-de-la-construction.md) avant de lire celui-ci.

<h2 id="travailler-avec-sauce-labs">Travaillez avec Sauce Labs.
<a href="#travailler-avec-sauce-labs" class="ancre-titre after"></a></h2>

[Sauce Labs](https://saucelabs.com/) fournit le _clound_ Sélénium avec un accès à plus de 170 combinaisons de périphériques/OS/navigateurs différentes. Si vous avez des tests de navigateur qui utilisent Sélénium, l'utilisation de Sauce Labs pour exécuter les tests est très facile. Tout d'abord, vous devez vous inscrire à leur service (c'est gratuit pour les projets open-source).

Une fois que vous vous êtes inscrit, configurez un tunnel à l'aide de Sauce Connect afin que Sauce Labs puisse se connecter à votre serveur web. Notre [_addon_ Sauce Connect](./sauce-connect.md) rend cela facile, il suffit d'ajouter ceci à votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
addons:
    sauce_connect:
        username: "Your Sauce Labs username"
        access_key: "Your Sauce Labs access key"
```

Vous pouvez [crypter votre clé d'accès](./cryptage.md), si vous le souhaitez.

Maintenant, Sauce Labs a un moyen d'atteindre votre serveur web, mais vous devez toujours le démarrer. Voir [démarrage d'un serveur web](#demarrer-un-serveur-web) ci-dessous pour plus d'informations sur la façon de le faire.

Enfin, vous devez configurer vos tests Selenium pour qu'ils s'exécutent sur Sauce Labs au lieu de s'exécuter localement. Cela se fait à l'aide d'un [Remote WebDriver](https://code.google.com/p/selenium/wiki/RemoteWebDriver). Le code exact dépend de l'outil/plateforme que vous utilisez, mais pour Python, il ressemblerait à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;exemple.py</div><div class="item droit">Python</div>
</div>
```yaml
username = os.environ["SAUCE_USERNAME"]
access_key = os.environ["SAUCE_ACCESS_KEY"]
capabilities["tunnel-identifier"] = os.environ["TRAVIS_JOB_NUMBER"]
hub_url = "%s:%s@localhost:4445" % (username, access_key)
driver = webdriver.Remote(desired_capabilities=capabilities, command_executor="http://%s/wd/hub" % hub_url)
```

Le module complémentaire Sauce Connect exporte les variables d'environnement `SAUCE_USERNAME` et `SAUCE_ACCESS_KEY`, et relaie les connexions à l'URL du pôle vers Sauce Labs.

C'est tout ce dont vous avez besoin pour faire fonctionner vos tests Selenium sur Sauce Labs. Cependant, il se peut que vous souhaitiez utiliser Sauce Labs uniquement pour les builds Travis CI et non pour les builds locaux. Pour ce faire, vous pouvez utiliser les variables d'environnement `CI` ou `TRAVIS` pour changer conditionnellement le pilote que vous utilisez (voir [notre liste de variables d'environnement disponibles](./precise.md#variables-environnement)  pour plus de moyens de détecter si vous travaillez sur Travis CI).

Pour rendre les résultats des tests sur Sauce Labs un peu plus faciles à naviguer, vous pouvez souhaiter fournir plus de métadonnées à envoyer avec le build. Vous pouvez le faire en passant plus de capacités souhaitées :

<div class="titre-code">
    <div class="item gauche">&nbsp;exemple.py</div><div class="item droit">Python</div>
</div>
```yaml
capabilities["build"] = os.environ["TRAVIS_BUILD_NUMBER"]
capabilities["tags"] = [os.environ["TRAVIS_PYTHON_VERSION"], "CI"]
```

Pour travis-web, notre propre site web, nous utilisons Sauce Labs pour exécuter des tests de navigateur sur plusieurs d'entre eux. Nous utilisons des variables d'environnement dans notre [.travis.yml](https://github.com/travis-ci/travis-web/blob/15dc5ff92184db7044f0ce3aa451e57aea58ee19/.travis.yml#L14-15) pour diviser la build en plusieurs travaux, puis passer le navigateur souhaité dans Sauce Labs en utilisant les [capacités souhaitées](https://github.com/travis-ci/travis-web/blob/15dc5ff92184db7044f0ce3aa451e57aea58ee19/script/saucelabs.rb#L9-13). Du côté de Travis CI, il finit par ressembler à [ceci](https://travis-ci.org/travis-ci/travis-web/builds/12857641).

<h2 id="executer-des-tests-gui">Exécuter des tests GUI avec xvfb .
<a href="#executer-des-tests-gui" class="ancre-titre after"></a></h2>

Pour exécuter des tests nécessitant une interface utilisateur graphique sur Travis CI, utilisez `xvfb` (X Virtual Framebuffer) pour imiter un affichage. Si vous avez besoin d'un navigateur, vous pouvez utiliser Firefox (avec la version préinstallée, soit [l'addon](./firefox.md)) ou Google Chrome (avec [l'addon](./chrome.md), sur Linux Trusty).

<h3 id="service-script">Utiliser les services : sur votre script .
<a href="#service-script" class="ancre-titre after"></a></h3>

<div class="notice cadre">
Cela ne fonctionne que sur Ubuntu 16.04 (Xenial) et plus tard sur les versions, c'est-à-dire avec <code>dist: xenial</code> ou <code>dist: bionic</code>.
</div>

Les éléments suivants démarreront xvfb et définiront les bonnes valeurs pour la variable d'environnement `DISPLAY` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
dist: xenial
services:
    - xvfb
```

<h3 id="emballage-xvfb-run">Comment utiliser l'emballage xvfb-run .
<a href="#emballage-xvfb-run" class="ancre-titre after"></a></h3>

`xvfb-run` est un emballage pour invoquer `xvfb` afin que `xvfb` puisse être utilisé avec moins de soucis :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
script: xvfb-run make test
```

Pour définir la résolution de l'écran :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
script: xvfb-run --server-args="-screen 0 1024x768x24" make test
```

<h3 id="utilisation-directe-de-xvfb">Utilisation directe de xvfb.
<a href="#utilisation-directe-de-xvfb" class="ancre-titre after"></a></h3>
 
<div class="notice cadre">
Ceci est recommandé sur Ubuntu 14.04 (Trusty), c'est-à-dire avec <code>dist: trusty</code>. Pour <code>dist: xenial</code>, utilisez le mot-clé <code>services</code> décrit <a href="#service-script">ci-dessus</a>.
</div>

Pour utiliser `xvfb` lui-même, démarrez-le dans la section `before_script` de votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
before_script:
    - "export DISPLAY=:99.0"
    - "sh -e /etc/init.d/xvfb start"
    - sleep 3 # give xvfb some time to start
```

Remarque: n'exécutez pas `xvfb` directement, car il ne gère pas plusieurs instances simultanées de cette façon.

Si vous devez définir la taille de l'écran et la profondeur des pixels, vous devez démarrer `xvfb` avec l'utilitaire `start-stop-daemon` et non avec le script init dans l'exemple précédent.

Par exemple, pour définir la résolution d'écran sur `1280x1024x16` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
before_install:
    - "/sbin/start-stop-daemon --start --quiet --pidfile /tmp/custom_xvfb_99.pid --make-pidfile --background --exec /usr/bin/Xvfb -- :99 -ac -screen 0 1280x1024x16"
```

Voir la [page de manuel xvfb](http://www.xfree86.org/4.0.1/Xvfb.1.html) pour plus d'informations.

<h3 id="demarrer-un-serveur-web">Démarrer un serveur web.
<a href="#demarrer-un-serveur-web" class="ancre-titre after"></a></h3>

Si votre projet nécessite l'exécution d'une application web pour être testé, vous devez en démarrer une avant d'exécuter les tests. Il est courant d'utiliser des serveurs web basés sur Ruby, Node.js et JVM qui servent les pages HTML utilisées pour exécuter les suites de tests. Étant donné que chaque environnement de build fournit au moins une version de Ruby, Node.js et OpenJDK, vous pouvez compter sur l'une de ces trois options.

Ajoutez un `before_script` pour démarrer un serveur, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
before_script:
    - "export DISPLAY=:99.0"
    - "sh -e /etc/init.d/xvfb start"
    - sleep 3 # give xvfb some time to start
    - rackup  # start a Web server
    - sleep 3 # give Web server some time to bind to sockets, etc
```

Si vous avez besoin que le serveur web écoute sur le port 80, n'oubliez pas d'utiliser `sudo` (Linux n'autorise pas les processus non privilégiés à se lier au port 80). Pour les ports supérieurs à 1024, l'utilisation de `sudo` n'est pas nécessaire (et n'est pas recommandée).
Notez que `sudo` n'est pas disponible pour les builds qui s'exécutent sur [les _workers_ basés sur des conteneurs](./precise.md).

<h2 id="mode-sans-tete-addon-chrome">Mode sans tête avec l'<i>addon</i> Chrome. 
<a href="#mode-sans-tete-addon-chrome" class="ancre-titre after"></a></h2>

À partir de la version 57 pour Linux Trusty, Google Chrome peut être utilisé en mode «sans tête» avec [l'addon Chrome](./chrome), qui convient aux tests basés sur le navigateur à l'aide de Sélénium et d'autres outils. 

<div class="notice cadre">
Au 02-05-2017, cela signifie <code>stable</code> ou <code>beta</code> sur les builds Linux.
</div>

Par exemple, sur Linux :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
dist: xenial
addons:
    chrome: stable
before_install:
    - # start your web application and listen on `localhost`
    - google-chrome-stable --headless --disable-gpu --remote-debugging-port=9222 http://localhost &
    ⋮
```

<h3 id="plus-de-doc">Plus de documentation.
<a href="#plus-de-doc" class="ancre-titre after"></a></h3>

* [Documentation de chrome sans tête](https://chromium.googlesource.com/chromium/src/+/lkgr/headless/README.md) ;
* [Démarrage avec Chrome sans tête](Getting Started with Headless Chrome).

<h2 id="mode-sans-tete-addon-firefox">Mode sans tête avec l'<i>addon</i>Firefox.
<a href="#mode-sans-tete-addon-firefox" class="ancre-titre after"></a></h2>

À partir de la version 56, Firefox peut être utilisé en mode «sans tête» avec le [l'addonFirefox](./firefox.md), qui convient aux tests basés sur le navigateur à l'aide de Sélénium et d'autres outils. Le mode sans tête peut être activé à l'aide de la [variable d'environnement](./variables-environnement.md) `MOZ_HEADLESS`:

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
env:
    global:
      - MOZ_HEADLESS=1
addons:
    firefox: latest
```

Vous pouvez également passer l'argument de ligne de commande `-headless` au démarrage de Firefox. Par exemple, le code suivant montre comment définir cet argument en utilisant le client Python pour Selenium :

<div class="titre-code">
        <div class="item gauche">&nbsp;exemple.py</div><div class="item droit">Python</div>
</div>
```yaml
from selenium.webdriver import Firefox
from selenium.webdriver.firefox.options import Options
options = Options()
options.add_argument('-headless')
firefox = Firefox(firefox_options=options)
```

<h3 id="plus-de-documentation">Plus de documentation.
<a href="#plus-de-documentation" class="ancre-titre after"></a></h3>

* [Utilisation du mode sans tête](https://developer.mozilla.org/en-US/Firefox/Headless_mode#Using_headless_mode) ;
* [Tests automatisés avec le mode sans tête](https://developer.mozilla.org/en-US/Firefox/Headless_mode#Automated_testing_with_headless_mode).

<h2 id="utiliser-phantomjs">Utiliser PhantomJS.
<a href="#utiliser-phantomjs" class="ancre-titre after"></a></h2>

[Phantomjs](http://phantomjs.org/) est un webkit sans tête avec l'API JavaScript. Il s'agit d'une solution optimale pour les tests rapides sans tête, le grattage du site, la capture des pages, le rendu SVG, la surveillance du réseau et de nombreux autres cas d'utilisation.

[L'environnement IC](./precise.md) fournit des PhantomJS préinstallés (disponibles dans le _PATH_ en temps que `phantomjs` ; ne comptez pas sur l'emplacement exact). Comme il est complètement sans tête, il n'y a pas besoin d'exécuter `xvfb`.

Un exemple très simple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
script: phantomjs testrunner.js
```

Si vous avez besoin d'un serveur web pour servir les tests, consultez la section précédente.

<h2 id="exemples">Exemples.
<a href="#exemples" class="ancre-titre after"></a></h2>

Ce qui suit est une série d'exemples.

<h3 id="projets-monde-reel">Projets du monde réel.
<a href="#projets-monde-reel" class="ancre-titre after"></a></h3>

* [Ember.js](https://github.com/emberjs/ember-mocha/blob/master/.travis.yml) (démarre le serveur web programmatiquement) ; 
* [Sproutcore](https://github.com/sproutcore/sproutcore/blob/master/.travis.yml) (démarre le serveur web avec <i>before_script</i>).

<h3 id="exemple-ruby">Exemple Ruby.
<a href="#exemple-ruby" class="ancre-titre after"></a></h3>

<h5 id="rspec-jasmine-cucumber">RSpec, Jasmine, Cucumber.
<a href="#rspec-jasmine-cucumber" class="ancre-titre after"></a></h5>

Voici un exemple de tâche _rake_ qui exécute les tests RSpec, Jasmine et Cucumber :

<div class="titre-code">
    <div class="item gauche">&nbsp;exemple.rb</div><div class="item droit">Ruby</div>
</div>
```ruby
task :travis do
    ["rspec spec", "rake jasmine:ci", "rake cucumber"].each do |cmd|
        puts "Starting to run #{cmd}..."
        system("export DISPLAY=:99.0 && bundle exec #{cmd}")
        raise "#{cmd} failed!" unless $?.exitstatus == 0
    end
end
```

Dans cet exemple, Jasmin et le Cucumber ont besoin du port d'affichage, car ils utilisent tous les deux de vrais navigateurs. Rspec s'exécuterait sans lui, mais cela ne fait aucun mal de le définir.

<h2 id="depannage">Dépannage.
<a href="#depannage" class="ancre-titre after"></a></h2>

<h3 id="popups">Fenêtres contextuelles Sélénium et Firefox.
<a href="#popups" class="ancre-titre after"></a></h3>

Si votre suite de tests gère une fenêtre de dialogue modale, par exemple, [une redirection vers un autre emplacement](https://support.mozilla.org/en-US/questions/792131), vous devrez peut-être ajouter un profil personnalisé afin que la fenêtre contextuelle soit supprimée.

Cela peut être corrigé en appliquant un profil Firefox personnalisé avec l'option turned off: (l'exemple est en Rubis à l'aide de Capybara).

<div class="titre-code">
    <div class="item gauche">&nbsp;exemple.rb</div><div class="item droit">Ruby</div>
</div>
```ruby
Capybara.register_driver :selenium do |app|
    custom_profile = Selenium::WebDriver::Firefox::Profile.new
    # Turn off the super annoying popup!
    custom_profile["network.http.prompt-temp-redirect"] = false
    Capybara::Selenium::Driver.new(app, :browser => :firefox, :profile => custom_profile)
end
```

<h3 id="delai-inactivite-karma-et-firefox">Délais d'inactivité pour Karma et Firefox.
<a href="#delai-inactivite-karma-et-firefox" class="ancre-titre after"></a></h3>

Lorsque vous testez avec Karma et Firefox, vous pouvez rencontrer des erreurs de build en raison des délais d'inactivité du navigateur. Lorsque cela se produit, le Karma sortira une erreur similaire à :

```console
WARN [Firefox 31.0.0 (Linux)]: Disconnected (1 times), because no message in 10000 ms.
```

Dans ce cas, vous devez augmenter le délai d'inactivité du navigateur à une valeur plus élevée dans `karma.conf.js`, par exemple :

<div class="titre-code">
    <div class="item gauche">karma.conf.js</div><div class="item droit">Js</div>
</div>
```js
browserNoActivityTimeout: 30000,
```

Pour plus d'informations, reportez-vous à la [documentation du fichier de configuration Karma](https://karma-runner.github.io/1.0/config/configuration-file.html).
<hr class="invisible">
