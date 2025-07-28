# Problèmes courants de construction

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#tests-casses">Tests cassés qui fonctionnaient auparavant</a></li>
            <li><a href="#script-de-build-tue-sans-erreur">Le script de build est tué sans aucune erreur</a></li>
            <li><a href="#echecs-de-build-inattendus">Échecs de construction inattendus</a></li>
            <li><a href="#defaut-de-segmentation">Défauts de segmentation de l'interprète linguistique</a></li>
            <li><a href="#renvoi-zero"><strong>Ruby</strong> : RSPEC renvoie 0 lorsque la construction a échoué</a></li>
            <li><a href="#capybara-erreurs"><strong>Capybara</strong> : erreurs éléments non trouvés</a></li>
            <li><a href="#installation-debugger-ruby-echoue"><strong>Ruby</strong> : Debugger_ruby-core-source  L'installation de la bibliothèque échoue</a></li>
            <li><a href="#tests-figes-et-annules"><strong>Ruby</strong> : tests figés et annulés</a></li>
            <li><a href="#fastlane">FastLane</a></li>
            <li><a href="#pack-de-langues-requises"><strong>Système</strong> : pack de langues requise non installée</a></li>
            <li><a href="#linux-404"><strong>Linux</strong> : APT ne parvient pas à installer le package avec une erreur 404</a></li>
            <li><a href="#windows-problemes-connus"><strong>Windows</strong> : Problèmes de construction courants et problèmes connus</a></li>
            <li><a href="#etats-entre-builds">Travis CI ne préserve pas l'état entre les builds</a></li>
            <li><a href="#ssh-ne-fonctionne-pas">SSH ne fonctionne pas comme prévu</a></li>
            <li><a href="#sous-modules-git-non-maj">Les sous-modules Git ne sont pas mis à jour correctement</a></li>
            <li><a href="#git-ne-clone-pas-les-sous-modules">Git ne peut pas cloner mes sous-modules</a></li>
            <li><a href="#build-en-delai-depassé">Build en délai d'attente dépassé</a></li>
            <li><a href="#debogage-des-builds">Exécuter les builds en mode de débogage</a></li>
            <li><a href="#depasser-la-longueur-du-journal">Dépasser la longueur du journal</a></li>
            <li><a href="#ftp-et-smtp-ne-fonctionnent-pas">Les protocoles FTP et SMTP ne fonctionnent pas</a></li>
            <li><a href="#commit-sans-build">Pousser un commit et ne pas trouver la build</a></li>
            <li><a href="#espace-disque-manquant">La build manque d'espace disque</a></li>
            <li><a href="#artefacts-chez-sonatype">Télécharger des artefacts sur sonatype</a></li>
            <li><a href="#non-reconnaissance-du-token-github">Travis CLI ne reconnaît pas mon token GitHub valide</a></li>
            <li><a href="#jobs-inconnus-dans-un-build">Jobs inconnus ou dupliqués dans un build</a></li>
            <li><a href="#script-provoquant-des-echecs"><strong>Node</strong> : L'exécution d'un script avant l'installation d'une dépendance provoque des échecs de builds</a></li>
            <li><a href="#erreur-de-connexion-npm"><strong>Node</strong> : Erreur enetunreach lors de la connexion à NPM ou YARN</a></li>
            <li><a href="#problemes-semantiques"><strong>Problème de version sémantique de NPM</strong> : Corrige la version sémantique <code>EGITNOPERMISSION</code> de GitHub</a></li>
        </ul>
    </div>
</div>
</p>

<h2 id="tests-casses">Tests cassés qui fonctionnaient auparavant.
<a href="#tests-casses" class="ancre-titre after"></a></h2>

Une cause très commune lorsqu'un test est soudainement cassé sans qu'il y ait de changements majeurs dans le code est une modification dans les dépendances en amont.

Il peut s'agir d'un paquet Ubuntu ou de n'importe quelle dépendance de langage de votre projet, comme RubyGems, les paquets NPM, Pip, Composer, etc.

Pour savoir si c'est le cas :

* Redémarrez une build qui était verte, la dernière connue qui fonctionnait, par exemple. Si cette version échoue soudainement, il y a de fortes chances qu'une dépendance ait été mise à jour et qu'elle soit à l'origine de la panne.
* Vérifiez la liste des dépendances dans le journal de build, généralement la sortie incluant les versions et voyez s'il y a quelque chose qui a changé.

Parfois, cela peut également être causé par une dépendance indirecte qui a été mise à jour.

* Après avoir trouvé quelle dépendance a été mise à jour, verrouillez-la à la dernière version connue.
* En outre, nous mettons régulièrement à jour notre environnement de build, ce qui apporte des versions plus récentes des langages et des services en cours d'exécution.

<h2 id="script-de-build-tue-sans-erreur">Le script de build est tué sans aucune erreur.
<a href="#script-de-build-tue-sans-erreur" class="ancre-titre after"></a></h2>

Parfois, vous verrez un script de build causer une erreur, et le message dans le journal sera quelque chose comme `Killed`.

Cela est généralement dû au fait que le script ou l'un des programmes qu'il exécute épuise la mémoire disponible dans le bac à sable de build, qui est actuellement de 3 Go. De plus, il y a deux cœurs disponibles, en mode « bursted ».

En fonction de l'outil utilisé, cela peut être dû à plusieurs choses :

* La suite de tests Ruby consomme trop de mémoire.
* Les tests s'exécutant en parallèle utilisent trop de processus ou de threads (par exemple, en utilisant le gem `parallel_test`).
* g++ nécessite trop de mémoire pour compiler les fichiers, par exemple, lorsqu'ils contiennent beaucoup de modèles.

<h3 id="processus-paralleles">Processus parallèles.
<a href="#processus-paralleles" class="ancre-titre after"></a></h3>

Pour les processus parallèles s'exécutant en même temps, essayez d'en réduire le nombre. De deux à quatre processus devraient suffire, au-delà, les ressources risquent d'être épuisées.

<h3 id="processus-ruby">Processus Ruby.
<a href="#processus-ruby" class="ancre-titre after"></a></h3>

Avec les processus Ruby, vérifiez la consommation de mémoire sur votre machine locale, elle devrait présenter des causes similaires. Cela peut être dû à des fuites de mémoire ou à des paramètres personnalisés pour le ramasse-miettes, par exemple pour retarder autant que possible un balayage. Réduire ces chiffres devrait aider.

<h2 id="echecs-de-build-inattendus">Échecs de construction inattendus.
<a href="#echecs-de-build-inattendus" class="ancre-titre after"></a></h2>

Une cause possible des échecs de build inattendus peut être l'appel de `set -e` (également appelé `set errexit`), soit directement dans votre fichier `.travis.yml`, soit en sourçant un script qui le fait. Cela provoque l'arrêt de toute erreur entraînant un statut de retour non nul dans votre script et l'échec immédiat de la build.

<div class="notice note">
Notez que l'utilisation de <code>set -e</code> dans des scripts externes ne pose pas ce problème, car <code>errexit</code> n'est efficace que dans le script externe.
</div>

Voir également [étapes de builds complexes](./personnalisation-de-la-construction.md#builds-complexes).

Une autre raison pourrait être que le paramètre `Clone or import` (cloner ou importer) du dépôt est réglé sur `OFF`. Dans ce cas, aucune information du dépôt n'est partagée et il est possible que certaines builds utilisant des dépendances privées entre les dépôts échouent. Si vous souhaitez éviter cette situation lorsque tous vos référentiels arrêtent de partager les dépendances, veuillez consulter les paramètres du référentiel et définir explicitement `Clone or import` (cloner ou importer) sur `ON`. Dans ce cas, vos versions continuent de fonctionner comme d'habitude.

<h2 id="defaut-de-segmentation">Défauts de segmentation de l'interprète linguistique.
<a href="#defaut-de-segmentation" class="ancre-titre after"></a></h2>

Si votre build échoue en raison de défauts de segmentation inattendu dans l'interpréteur linguistique, cela peut être causé par des caches corrompus ou non valides de vos codes d'extension (gems, modules, etc.). Cela peut se produire avec n'importe quelle langue interprétée, comme Ruby, Python, PHP, Node.js, etc.

Résolvez le problème en :

* effaçant le cache ou ;
* supprimer de la clé de cache de votre .travis.yml (vous pouvez l'ajouter dans un _commit_ ultérieur).

<h2 id="renvoi-zero"><strong>Ruby</strong> : RSPEC renvoie 0 lorsque la construction a échoué.
<a href="#renvoi-zero" class="ancre-titre after"></a></h2>

Dans certains scénarios, lors de l'exécution de `rake rspec` ou même rspec directement, la commande renvoie 0 même si la construction a échoué. Ceci est généralement dû à certains RubyGem qui écrasent le gestionnaire `at_exit` d'un autre RubyGem, dans ce cas RSpec.

La solution de contournement est d'installer ce gestionnaire `at_exit` dans votre code, comme indiqué dans [cet](http://www.davekonopka.com/2013/rspec-exit-code.html) article.

<div class="titre-code">
    <div class="item gauche">&nbsp;.exemple.rb</div><div class="item droit">Ruby</div>
</div>
```ruby
if defined?(RUBY_ENGINE) && RUBY_ENGINE == "ruby" && RUBY_VERSION >= "1.9"
    module Kernel
        alias :__at_exit :at_exit
        def at_exit(&block)
            __at_exit do
                exit_status = $!.status if $!.is_a?(SystemExit)
                block.call
                exit exit_status if exit_status
            end
        end
    end
end
```

Si votre projet utilise [l'intégration de Code Climate](./code-climate.md) ou SimpleCov, ce problème peut également se produire avec la branche 0.8 de Simplecov. La solution consiste à revenir à la dernière version 0.7 jusqu'à ce que le problème soit résolu.

<h2 id="capybara-erreurs"><strong>Capybara</strong> :  Erreurs éléments non trouvées.
<a href="#capybara-erreurs" class="ancre-titre after"></a></h2>

Dans les scénarios qui impliquent JavaScript, vous pouvez parfois voir des erreurs qui indiquent qu'un élément manque, un bouton, un lien ou une autre ressource mise à jour ou créée par JavaScript asynchrone.

Cela peut indiquer que les délais d'expiration utilisés pour Sélénium ou l'un de ses pilotes sont réglés trop bas.

Capybara a un délai d'expiration que vous pouvez augmenter à un minimum de 15 secondes :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Js</div>
</div>
```js
Capybara.default_max_wait_time = 15
```

Poltergeist a son propre réglage pour les délais d'expiration:

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Js</div>
</div>
```js
Capybara.register_driver :poltergeist do |app|
    Capybara::Poltergeist::Driver.new(app, timeout: 15)
end
```

Si vous voyez toujours des délais d'attente après l'avoir augmenté au départ, définissez-le sur quelque chose de beaucoup plus élevé pour un essai. Si l'erreur persiste, il y a peut-être un problème plus profond sur la page, par exemple en compilant les actifs.

<h2 id="installation-debugger-ruby-echoue"><strong>Ruby</strong> : Debugger_ruby-core-source  L'installation de la bibliothèque échoue.
<a href="#installation-debugger-ruby-echoue" class="ancre-titre after"></a></h2>

Malheureusement, cette bibliothèque Ruby a déjà connu des problèmes de compatibilité, même avec des versions mineures de Ruby. Elle est généralement une dépendance de bibliothèques telles que linecache ou d'autres bibliothèques de débogage Ruby.

Nous vous recommandons de déplacer ces bibliothèques dans un groupe séparé dans votre Gemfile, puis d'installer RubyGems sur Travis CI sans ce groupe. Comme ces bibliothèques ne sont utiles que pour le développement en local, vous gagnerez même en vitesse lors du processus d'installation de votre build.

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Ruby</div>
</div>
```js
#Gemfile
group :debug do
    gem 'debugger'
    gem 'debugger-linecache'
    gem 'rblineprof'
end
#.travis.yml
bundler_args: --without development debug
```

<h2 id="tests-figes-et-annules"><strong>Ruby</strong> : tests figés et annulés
<a href="#tests-figes-et-annules" class="ancre-titre after"></a></h2>

Dans certains cas, les tests sont figés puis annulés après 10 minutes d'inactivité. L'utilisation du gem `timecop` peut entraîner des « gels » apparemment sporadiques en raison de problèmes liés à l'ordre des appels `Timecop.return`, `Timecop.freeze` et `Timecop.travel`. Par exemple, si vous utilisez RSpec, veillez à configurer `Timecop.return` pour qu'il s'exécute _après_ tous ; exemples :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Ruby</div>
</div>
```ruby
#in, e.g. spec/spec_helper.rb
RSpec.configure do |c|
    c.after :all do
        Timecop.return
    end
end
```

<h2 id="fastlane">Fastlane.
<a href="#fastlane" class="ancre-titre after"></a></h2>

Si vous utilisez Fastlane pour signer votre application (par exemple avec [Fastlane Match](https://github.com/fastlane/fastlane/tree/master/match)), vous devrez faire quelque chose de similaire à ce qui suit dans votre `FastFile` : 

<div class="titre-code">
    <div class="item gauche">&nbsp;fastfile</div><div class="item droit">Config</div>
</div>
```config
create_keychain(
    name: ENV["MATCH_KEYCHAIN_NAME"],
    password: ENV["MATCH_PASSWORD"],
    default_keychain: true,
    unlock: true,
    timeout: 3600,
    add_to_search_list: true
)
match(
    type: "adhoc",
    keychain_name: ENV["MATCH_KEYCHAIN_NAME"],
    keychain_password: ENV["MATCH_PASSWORD"],
    readonly: true
)
```

Si vous utilisez `import_certificate` directement pour importer vos certificats, il est obligatoire de passer le mot de passe de votre trousseau en tant que paramètre, par ex :

<div class="titre-code">
    <div class="item gauche">&nbsp;fastfile</div><div class="item droit">Config</div>
</div>
```config
keychain_name = "ios-build.keychain"
keychain_password = securerAndom.base64
create_keychain(
    name: keychain_name,
    password: keychain_password,
    default_keychain: true,
    unlock: true,
    timeout: 3600,
    add_to_search_list: true
)
import_certificate(
    certificate_path: "fastlane/Certificates/dist.p12",
    certificate_password: ENV["KEY_PASSWORD"],
    keychain_name: keychain_name
    keychain_password: keychain_password
)
```

Vous pouvez également avoir plus de détails sur [ce problème GitHub](https://github.com/travis-ci/travis-ci/issues/6791) à partir de [ce commentaire](https://github.com/travis-ci/travis-ci/issues/6791#issuecomment-261071904).

<h2 id="pack-de-langues-requises"><strong>Système</strong> : pack de langues requise non installée.
<a href="#pack-de-langues-requises" class="ancre-titre after"></a></h2>

Les environnements de build Travis CI n'ont actuellement que le pack de langues en_US installé. Si vous obtenez une erreur similaire à: «Error: unsupported locale setting”» (Erreur: paramètre régional non pris en charge), vous devrez peut-être installer un autre pack de langues pendant votre test.

Cela peut être fait avec l'ajout suivant à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - sudo apt-get update && sudo apt-get --reinstall install -qq language-pack-en language-pack-de
```

L'ajout ci-dessus réinstallera le pack de langues en_US ainsi que le pack de langage de_DE.

Si vous utilisez l'infrastructure basée sur les conteneurs et que vous n'avez pas accès à la commande `sudo`, installez des locaux à l'aide de [l'addon APT](./installation-de-dependances.md):

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    apt:
        packages:
          - language-pack-en
          - language-pack-de
```

<h2 id="linux-404"><strong>Linux</strong> : APT ne parvient pas à installer le package avec une erreur 404.
<a href="#linux-404" class="ancre-titre after"></a></h2>

Ceci est souvent dû à l'ancienne base de données de packages et peut être corrigé en ajoutant ce qui suit à `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - sudo apt-get update
```

<h2 id="windows-problemes-connus"><strong>Windows</strong> : Problèmes de construction courants et problèmes connus.
<a href="#windows-problemes-connus" class="ancre-titre after"></a></h2>

Pour une liste de problèmes de build courants sur Windows, des problèmes connus et des solutions de contournement, veuillez visiter le [Travis CI Community Forum](Https://travis-ci.community/t/current-known-issues-pilefead-this-beforeposting-a-new-topic/264). Le [Forum communautaire de Travis CI](https://travis-ci.community/) offre une meilleure visibilité sur les problèmes que les clients rencontrent et comment les résoudre.

<h2 id="etats-entre-builds">Travis CI ne préserve pas l'état entre les builds.
<a href="#etats-entre-builds" class="ancre-titre after"></a></h2>

Travis CI utilise des instantanés de machine virtuelle pour s'assurer qu'aucun état n'est conservé entre les builds. Si vous modifiez l'environnement CI en écrivant quelque chose dans un magasin de données, en créant des fichiers ou en installant un package via APT, il n'affecte pas les versions ultérieures.

<h2 id="ssh-ne-fonctionne-pas">SSH ne fonctionne pas comme prévu.
<a href="#ssh-ne-fonctionne-pas" class="ancre-titre after"></a></h2>

Travis CI exécute toutes les commandes sur SSH dans des machines virtuelles isolées. Les commandes qui modifient les états de la session SSH sont «collantes» et persistent tout au long de la build. Par exemple, si vous vous executez la commande `cd` dans un répertoire, toutes les commandes suivantes sont exécutées à partir de ce répertoire.

<h2 id="sous-modules-git-non-maj">Les sous-modules Git ne sont pas mis à jour correctement.
<a href="#sous-modules-git-non-maj" class="ancre-titre after"></a></h2>

Travis CI initialise et met à jour les sous-modules lorsqu'il y a un fichier `.gitmodules` à la racine du référentiel.

Pour désactiver, définissez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
git:
    submodules: false
```

Si votre projet nécessite des options spécifiques pour vos sous-modules Git, qui ne sont pas prises en charge par défaut par Travis CI, désactivez l'intégration automatique et utilisez la personalisation `before_install` pour les initialiser et les mettre à jour.

Par exemple, pour mettre à jour les sous-modules imbriqués :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - git submodule update --init --recursive
```

<h2 id="git-ne-clone-pas-les-sous-modules">Git ne peut pas cloner mes sous-modules.
<a href="#git-ne-clone-pas-les-sous-modules" class="ancre-titre after"></a></h2>

Si votre projet utilise des sous-modules Git, assurez-vous d'utiliser des URLs Git publiques. Par exemple, sur GitHub, au lieu de :

```console
git@github.com:someuser/somelibrary.git
```

utiliser :
```console
https://github.com/someuser/somelibrary.git
```

Sinon, les constructeurs de Travis CI ne pourront pas cloner votre projet car ils n'ont pas votre clé SSH privée.

<h2 id="build-en-delai-depassé">Build en délai d'attente dépassé.
<a href="#build-en-delai-depassé" class="ancre-titre after"></a></h2>

Les builds peuvent malheureusement expirer, soit pendant l'installation des dépendances, soit pendant la build elle-même, par exemple à cause d'une commande qui prend plus de temps à s'exécuter sans produire de résultat.

Nos builds ont un délai d'expiration global et un délai d'expiration basé sur la sortie. Si aucune sortie n'est reçue d'une build pendant 10 minutes, elle est supposée être bloquée pour des raisons inconnues et sera par la suite tuée.

Dans d'autres cas, l'installation des dépendances peut expirer. Bundler et RubyGems en sont un exemple pertinent. La connectivité réseau entre nos serveurs peut parfois affecter la connectivité de APT, Maven ou d'autres référentiels.

Il existe peu de façons de contourner cela.

<h3 id="dependances-hors-delai">Délais d'attente lors de l'installation des dépendances.
<a href="#dependances-hors-delai" class="ancre-titre after"></a></h3>

Si vous obtenez des délais d'attente du réseau lorsque vous essayez de télécharger des dépendances, utilisez la fonctionnalité de nouvelle tentative intégrée à votre gestionnaire de dépendances ou intégrez vos commandes d'installation dans la fonction `travis_retry`.

<h5 id="bundler">Bundler.
<a href="#bundler" class="ancre-titre after"></a></h5>

Bundler effectue trois tentatives par défaut, mais si vous devez augmenter ce nombre, utilisez la syntaxe suivante dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
bundler_args: --retry 5
```

<h5 id="travis_retry">travis_retry.
<a href="#travis_retry" class="ancre-titre after"></a></h5>

Pour les commandes qui ne dispose pas d'une fonctionnalité de tentatives multiples intégrées, utilisez la fonction `travis_retry` pour la réessayer jusqu'à trois fois si le code de retour est non nul :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install: travis_retry pip install myawesomepackage
```

La plupart de nos commandes de builds internes sont enveloppées de `travis_retry` pour réduire l'impact des délais d'attente du réseau. 

<div class="notice cadre">
Notez que <code>travis_retry</code> ne fonctionne pas dans l'étape de déploiement de la build, bien qu'elle fonctionne dans les autres étapes.
</div>

<h3 id="attente-expiree">Le délai d'attente a expiré car aucune sortie n'a été reçue.
<a href="#attente-expiree" class="ancre-titre after"></a></h3>

Lorsqu'une commande ou une étape de compilation dure régulièrement plus de 10 minutes sans produire de résultat, vous pouvez ajuster votre configuration de build pour en tenir compte.

L'environnement shell dans notre système de build offre une fonction qui aide à contourner cela, au moins pendant plus de 10 minutes.

Si vous avez une commande qui ne produit pas de sortie pendant plus de 10 minutes, vous pouvez la préfixer avec `travis_wait n`, une fonction exportée par notre environnement de build. Par exemple : 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
    install: travis_wait mvn install
```

lance un processus exécutant `mvn install`. `travis_wait` écrit ensuite une courte ligne dans le journal de compilation toutes les minutes pendant 20 minutes, prolongeant ainsi le temps dont dispose votre commande pour s'exécuter.

Si vous vous attendez à ce que la commande prenne plus de 20 minutes, préfixez la commande avec `travis_wait n` où `n` est le nombre de minutes par lesquelles le temps d'attente est prolongé.

Poursuivons l'exemple ci-dessus pour prolonger le temps d'attente à 30 minutes : 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install: travis_wait 30 mvn install
```

<div class="notice cadre">
Nous vous recommandons d'utiliser soigneusement <code>travis_wait</code>, car il peut prolonger votre temps de build lorsqu'il pourrait y avoir un problème sous-jacent plus profond. En cas de doute, envoyez-nous un e-mail d'abord pour voir si quelque chose pourrait être amélioré sur cette commande particulière en premier.
</div>

<h5 id="limites-de-travis-wait">Limites de travis_wait .
<a href="#limites-de-travis-wait" class="ancre-titre after"></a></h5>

`travis_wait` fonctionne en démarrant un processus, en l'envoyant en arrière-plan et en regardant le processus d'arrière-plan. Si la commande que vous passez à `travis_wait` ne persiste pas, `travis_wait` n'étend pas le délai d'attente.

<h2 id="debogage-des-builds">Exécuter les builds en mode de débogage.
<a href="#debogage-des-builds" class="ancre-titre after"></a></h2>

Dans les référentiels privés et les référentiels publics pour lesquels la fonctionnalité est activée, il est possible d'exécuter des builds et des travaux en mode de débogage. En utilisant cette fonctionnalité, vous pouvez interagir avec la _VM_ en direct où vos versions fonctionnent.

Pour plus d'informations, veuillez consulter la [documentation _VM_ debug](./construction-en-mode-debud.md).

<h2 id="depasser-la-longueur-du-journal">Dépasser la longueur du journal.
<a href="#depasser-la-longueur-du-journal" class="ancre-titre after"></a></h2>

Le journal pour chaque build est limité à environ 4 Mo. Lorsqu'il atteint cette taille, la build est terminée et vous verrez le message suivant à la fin de votre journal de build :

```config
La longueur du journal a dépassé la limite de 4 mégaoctets (cela signifie généralement que la suite de tests génère la même exception à plusieurs reprises).
La build a été terminée.
```

<h2 id="ftp-et-smtp-ne-fonctionnent-pas">Les protocoles FTP et SMTP ne fonctionnent pas.
<a href="#ftp-et-smtp-ne-fonctionnent-pas" class="ancre-titre after"></a></h2>

Certains protocoles tels que FTP et SMTP ne sont pas directement pris en charge en raison des exigences infrastructurelles en vigueur en matière de sécurité et d'utilisation équitable. Il est préférable d'utiliser des protocoles « sans état » alternatifs tels que HTTPS, mais le tunnelage est également connu pour fonctionner, par exemple en utilisant SFTP dans le cas spécifique du FTP, ou une connexion VPN pour une grande variété de protocoles, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    apt:
        packages:
        - openvpn
before_install:
  - sudo openvpn path/to/conf.ovpn &>>openvpn-client.log &
```

<h2 id="commit-sans-build">Pousser un commit et ne pas trouver la build.
<a href="#commit-sans-build" class="ancre-titre after"></a></h2>

Les événements de demande de build reçus par Travis CI sont répertoriés dans la page _Requests_ de votre référentiel. Vous pouvez les trouver dans le menu déroulant _More Options_, en sélectionnant _**Requests**_.

![Plus d'options menu déroulant, choisissant les requêtes](https://docs.travis-ci.com/images/common-build-problems/repository-requests-page.png)

Chaque fois que votre build a été traitée, vous verrez le message : «Build created successfully» (Build créée avec succés).

Si une build n'a pas été déclenchée pour votre commit, voici les messages de demande de builds possibles :

* **«Impossible d'autoriser la demande de construction»**, signifie généralement que l'abonnement du compte a expiré ou qu'il manquait de crédits de build. 
* **«Build sautée via le message de commit**, ce commit contient la [commande skip](./personnalisation-de-la-construction.md#sauter-une-build). 
* **«La charge utile de GitHub manque un engagement de fusion»**, veuillez confirmer que votre _PR_ est ouverte et fusionable. Vous pouvez également avoir des conflits non résolus dans une branche particulière. 
* **«Branche exclue par configuration»** ou **«Branche non incluse par configuration»**, veuillez vous assurer que votre branche n'est pas explicitement exclue ou non incluse dans votre fichier `.travis.yml`. 
* **«Build type désactivé via les paramètres du référentiel»**, veuillez vous assurer que vos builds de poussée ou de _PR_ sont toujours actives. 
* **«Build config n'a créé aucun travail.»**, veuillez vous assurer que les conditions dans votre fichier `.travis.yml` permettent de créer une tâche. 

<div class="notice cadre">
Veuillez noter que Travis CI ne reçoit pas d'événement <i>Webhook</i> lorsque plus de trois commits sont tagués. Donc, si vous effectuez une commande <code>git push --tags</code>, et que plus de trois tags présents localement ne sont pas connus sur GitHub, Travis ne sera informé d' aucun de ces événements, et les commits tagués ne seront pas construits.
</div>

<h2 id="espace-disque-manquant">La build manque d'espace disque.
<a href="#espace-disque-manquant" class="ancre-titre after"></a></h2>

L'espace disque approximatif disponible est répertorié dans la [vue d'ensemble de l'environnement de build](./environnement-de-construction.md).

La meilleure façon de savoir ce qui est disponible sur votre image spécifique est d'exécuter `df -h` dans le cadre de votre script de build. Si vous avez besoin d'un peu plus d'espace dans vos versions Ubuntu, nous vous recommandons d'utiliser `language: minimal`, qui vous redirigera vers une image de base avec moins d'outils et de langages préinstallés. Cette image dispose d'environ 24 Go d'espace libre.

<h2 id="artefacts-chez-sonatype">Télécharger des artefacts sur sonatype.
<a href="#artefacts-chez-sonatype" class="ancre-titre after"></a></h2>

Lors de la publication via le plugin `nexus-staging-maven-plugin` vers le référentiel Sonatype OSS, les adresses IP utilisées par Travis CI changent en raison de notre [couche NAT](https://travis-ci.com/blog/2018-07-23-the-tale-of-ftp-at-travis-ci). Pour contourner ce problème, veuillez utiliser `stagingProfileId` comme [expliqué dans ce document](https://travis-ci.community/t/sonatype-deployment-problems/1353/2?u=mzk).

<h2 id="non-reconnaissance-du-token-github">Travis CLI ne reconnaît pas mon jeton GitHub valide.
<a href="#non-reconnaissance-du-token-github" class="ancre-titre after"></a></h2>

Lorsque vous utilisez [l'outil Travis CLI](https://github.com/travis-ci/travis.rb#readme) pour interagir avec la plate-forme TRAVIS CI et si vous recevez une erreur `insufficient_oauth_permissions` ou similaire, veuillez vous assurer que le jeton GitHub fourni via `--github-token` a la portée de réapprovisionnement comme [expliqué dans ce document](https://developer.github.com/apps/building-oauth-apps/understanding-scopes-for-oauth-apps/).

<h2 id="jobs-inconnus-dans-un-build">Jobs inconnus ou dupliqués dans un build.
<a href="#jobs-inconnus-dans-un-build" class="ancre-titre after"></a></h2>

Lorsqu'ils spécifient des étapes, les utilisateurs ajoutent souvent sans le savoir une tâche implicite à la liste des tâches à l'aide de YAML, qui est par ailleurs syntaxiquement correct.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: c
...
jobs:
    include:
      - stage: Breakfast
      - name: Peanut Butter and Bread
        script: ./brew_hot_coffee.sh
```

La définition ci-dessus crée une étape appelée «_**Breakfast**t_» (petit-déjeuner) et deux tâches. La première est une tâche _implicite_ qui hérite de toutes les valeurs par défaut du langage de programmation spécifié. Dans l'exemple ci-dessus, les [valeurs par défaut pour C](./construire-un-projet-en-c-.md) seront utilisées, tandis que la deuxième tâche est «_Peanut Butter and Bread_ », (Beurre de cacahuète et pain) que vous avez explicitement définie.

Pour supprimer ce travail _implicite_, vous modifieriez ce qui précède pour ressembler à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: c
...
jobs:
    include:
      - stage: Breakfast
        name: Peanut Butter and Bread
        script: ./brew_hot_coffee.sh
```

Cela ne crée qu'un seul travail, _Peanut Butter and Bread_ sous la scène nommée _Breakfast_ comme vous l'avez défini. Il est important de noter que dans YAML, le symbole `-` est utilisé pour créer une liste d'éléments et l'exemple précédent crée une liste de 2 éléments, tandis que vous vouliez réellement 1. Vous pouvez en savoir plus sur la façon de définir [les étapes de construction](./etapes-de-construction.md) et la syntaxe des listes YAML dans la [documentation](https://yaml.org/spec/1.2/spec.html#id2759963) officielle.

<h2 id="script-provoquant-des-echecs"><strong>Node</strong> : L'exécution d'un script avant l'installation d'une dépendance provoque des échecs de builds.
<a href="#script-provoquant-des-echecs" class="ancre-titre after"></a></h2>

Lorsque vous ajoutez des instructions de configuration personnalisées à une compilation NodeJS, ajoutez-les dans la phase `before_script` et non avant l'installation des dépendances. La phase `before_script` est l'endroit le plus sûr pour ajouter des scripts de configuration personnalisés. Les symptômes de ce problème incluent l'échec soudain de builds précédemment réussies en raison de l'ajout d'une nouvelle dépendance.

<h2 id="erreur-de-connexion-npm"><strong>Node</strong> : Erreur enetunreach lors de la connexion à NPM ou YARN.
<a href="#erreur-de-connexion-npm" class="ancre-titre after"></a></h2>

Si vous utilisez NPM ou YARN, l'**_Erreur: connect ENETUNREACH_** s'affiche ou la build se bloque dans la phase d'installation, c'est-à-dire lors de l'exécution de `npm install` ou `yarn install` pour les versions 16 de NodeJS sur les images LXD (ppc64l, arm64 et s390x).

Cela semble être un bogue connu et les détails peuvent être examinés sur [https://github.com/npm/cli/issues/4163](https://github.com/npm/cli/issues/4163). Ajoutez ce qui suit pour résoudre le problème :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
env:
    global:
      - NODE_OPTIONS="--dns-result-order=ipv4first"
```

<h2 id="problemes-semantiques"><strong>Problème de version sémantique de NPM</strong> : Corrige la version sémantique <code>EGITNOPERMISSION</code> de GitHub.
<a href="#problemes-semantiques" class="ancre-titre after"></a></h2>

Si vous utilisez NPM et que vous déployez avec une libération sémantique et que vous obtenez l'erreur `EGITNOPERMISSION` à la fin de votre version, vous pouvez essayer d'ajouter ce qui suit à votre définition de build :

Le premier exemple est si vous utilisez `npx semantic-release` dans la phase de déploiement dans la définition `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_deploy:
  - git config --global credential.helper store
  - git config --global url."https://x-access-token:${GITHUB_TOKEN}@github.com/".insteadOf "https://github.com/"
```

Le deuxième exemple serait si vous utilisez `npx semantic-release` directement dans votre phase de script de votre définition de build `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
  - git config --global credential.helper store
  - git config --global url."https://x-access-token:${GITHUB_TOKEN}@github.com/".insteadOf "https://github.com/"
```

Pour plus d'informations, veuillez consulter [ce problème GitHub](https://github.com/semantic-release/semantic-release/issues/3590).
<hr class="invisible">