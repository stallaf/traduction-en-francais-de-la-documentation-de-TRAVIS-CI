# Construire un projet Ruby

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#versions-ruby">Spécifier les versions et implémentations Ruby</a></li>
            <li><a href="#script-par-defaut-ruby">Script de construction par défaut</a></li>
            <li><a href="#reference-de-configuration-de-construction-ruby">Référence de configuration de construction</a></li>
            <li><a href="#gestion-des-dependances-ruby">Gestion des dépendances</a></li>
            <li><a href="#jdks">JRuby : test avec plusieurs JDKs</a></li>
            <li><a href="#rubygems">Mise à niveau de RubyGems</a></li>
        </ul>
    </div>
</div>
</p>

<div class="deux-colonnes">
  <div class="notice cadre">
  Pour les versions de langage et d'autres informations spécifiques à l'environnement de compilation, consultez nos pages de référence :
  <p>
  <ul>
    <li><a href="environnement-precise.md">Precise</a> ;</li>
    <li><a href="environnement-trusty.md">Trusty</a> ;</li>
    <li><a href="environnement-xenial.md">Xenial</a> ;</li>
    <li><a href="environnement-bionic.md">Bionic</a> ;</li>
    <li><a href="environnement-focal.md">Focal</a> ;</li>
    <li><a href="environnement-jammy.md">Jammy</a> ;</li>
    <li><a href="environnement-noble.md">Noble</a> ;</li>
    <li><a href="environnement-macos.md">macOs</a> ;</li>
    <li><a href="environnement-freebsd.md">FreeBSD</a>.</li>
  </ul>
  </p>
  </div>

  <p>
  <table class="tableau">
      <thead>
          <tr><th>RUBY</th><th>DÉFAUT</th></tr>
      </thead>
      <tbody>
          <tr>
              <td><a href="#gestion-des-dependances-ruby">Défaut</a><code>install</code></td>
              <td><code>bundle install --jobs=3 --retry=3</code></td>
          </tr>
          <tr>
              <td><a href="#script-par-defaut-ruby">Défaut<code>script</code></td>
              <td><code>rake</code></td>
          </tr>
          <tr>
              <td>Cle de matrice</a></td>
              <td><code>env</code>,<code>rvm</code>,<code>gemfile</code>,<code>jdk</code><td>
          </tr>
          <tr>
              <td>Support</td>
              <td><a href="mailtosupport@travis-ci.com">Travis CI</a></td>
          </tr>
      </tbody>
      <tfoot>
          <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
          <tr><th colspan="2" text-align="left">Exemple minimal</th></tr>
          <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
          <tr><th colspan="2">
                    <div class="titre-code">
                        <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
                    </div>
```yml
language: ruby
```
          </th></tr>
      </tfoot>
  </table>
  </p>
  </div>

Ce guide traite de la configuration des projets Ruby sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.

<h2 id="versions-ruby">Spécifier les versions et implémentations Ruby.
<a href="#versions-ruby" class="ancre-titre after"></a></h2>

<div class="notice cadre">
Yjit, un Jit rubis léger et minimaliste construit à l'intérieur de Cruby est disponible avec des versions Ruby prises en charge.
</div>

L'environnement Ruby sur Travis CI utilise [RVM](https://rvm.io/) pour fournir de nombreuses implémentations Ruby, versions et même des niveaux de correctifs.

Pour les spécifier, utilisez la clé `rvm``: dans votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
rvm:
  - 2.5
  - 2.6
  - jruby
  - truffleruby
```

<div class="notice cadre">
Notez que la clé <code>rvm:</code> n'est disponible que dans des environnements Ruby Build, pas dans d'autres images contenant une implémentation Ruby.
</div>

Au fur et à mesure que nous mettons à niveau RVM et Rubies, des alias comme `2.2` ou `jruby` pointent vers différentes versions exactes et niveaux de correctifs.

<h3 id="cle-ruby-versions">Utiliser la clé .ruby-version.
<a href="#cle-ruby-versions" class="ancre-titre after"></a></h3>

Si la version Ruby n'est pas spécifiée par la clé `rvm`, Travis CI utilise la version spécifiée dans le fichier `.ruby-version` à la racine du référentiel si celui-ci existe.

<h3 id="rubinius">Rubinius.
<a href="#rubinius" class="ancre-titre after"></a></h3>

Si vous utilisez des environnements Trusty, vous pouvez également utiliser [Rubinius](http://rubini.us/). Pour tester avec Rubinius, ajoutez `rbx-X` ou `rbx-X.Y.Z` à votre fichier `.travis.yml`, où `X.Y.Z` spécifie une version de Rubinius répertoriée sur [http://rubies.travis-ci.org/rubinius ](http://rubies.travis-ci.org/rubinius).

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
dist: trusty
rvm:
  - rbx-3
```

<h3 id="truffleruby">TruffleRuby.
<a href="#truffleruby" class="ancre-titre after"></a></h3>

Pour tester avec [TruffleRuby](https://github.com/oracle/truffleruby), ajoutez simplement `truffleruby` ou `truffleruby-VERSION` à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
rvm:
  - truffleruby # latest release
  # or
  - truffleruby-19.2.0 # specific version
```

Consultez la page des [versions de TruffleRuby](https://github.com/oracle/truffleruby/releases) pour obtenir la liste des versions disponibles. Veuillez signaler tout problème sur [GitHub](https://github.com/oracle/truffleruby/issues).

<h3 id="jruby">JRuby : extensions C non prises en charge.
<a href="#jruby" class="ancre-titre after"></a></h3>

Veuillez noter que **les extensions C ne sont pas prises en charge dans JRuby** sur Travis CI. La raison est d'attirer l'attention des développeurs sur le fait que leur projet peut comporter des dépendances qui ne devraient pas être utilisées sur JRuby en production. L'utilisation d'extensions C sur JRuby est techniquement possible, mais n'est pas recommandée en termes de performances et de stabilité, et nous pensons que les services d'intégration continue tels que Travis CI devraient le souligner.

Donc, si vous souhaitez exécuter CI avec JRuby, veuillez vérifier que votre Gemfile prend en compte JRuby. La plupart des extensions C populaires disposent également d'implémentations Java (gem json, nokogiri, eventmachine, gem bson) ou d'alternatives Java (comme les pilotes basés sur JDBC pour MySQL, PostgreSQL, etc.).

<h2 id="script-par-defaut-ruby">Script de construction par défaut.
<a href="#script-par-defaut-ruby" class="ancre-titre after"></a></h2>

Dans les pprojets Ruby, le script de construction par défaut est `rake`. Ajoutez `rake` au groupe `;test` de votre Gemfile.

<h2 id="reference-de-configuration-de-construction-ruby">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-ruby" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Ruby](https://config.travis-ci.com/ref/language/ruby) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="gestion-des-dependances-ruby">Gestion des dépendances.
<a href="#gestion-des-dependances-ruby" class="ancre-titre after"></a></h2>

<h3 id="bundler">Bundler.
<a href="#bundler" class="ancre-titre after"></a></h3>

Travis CI utilise [Bundler](http://bundler.io/) pour installer les dépendances de votre projet Ruby en cas de Gemfile dans le répertoire racine du projet, ou s'il y a un Gemfile spécifié dans la matrice de construction :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```yml
bundle install --jobs=3 --retry=3
```

Si un Gemfile.lock existe dans le répertoire racine de votre projet, nous ajoutons l'indicateur `--deployment`.

Si vous souhaitez utiliser un moyen différent de gérer les dépendances de votre projet Ruby, vous pouvez remplacer la commande `install`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install: gem install rails
```

Par défaut, les _gems_ sont installées dans _vendor/bundle_ dans le répertoire racine de votre projet.

**Bundler 2.0**

Le 3 janvier 2019, l'équipe Bundler a sorti [Bundler 2.0](https://bundler.io/blog/2019/01/03/announcing-bundler-2.html) qui nécessite Ruby 2.3+. Une version [2.0.1](https://bundler.io/blog/2019/01/04/an-update-on-the-bundler-2-release.html) ultérieure a abaissé la version RubyGems requise à 2.5.0, qui est disponible par défaut sur Ruby 2.3+.

Par conséquent, _il n'est pas nécessaire de mettre à jour RubyGems_ pour Bundler 2.

Travis CI utilise Bundler 1 par défaut. Si votre `Gemfile.lock` a une section `BUNDLED WITH 1.x` (ou aucune section de ce type), le comportement par défaut devrait être suffisant et ne nécessite aucune modification.

Si vous trouvez que vos versions échouent en raison d'erreurs «Bundler non installées» ou si vous souhaitez utiliser Bundler 2.0, essayez l'une des solutions suivantes : 

* Si vous utilisez Ruby 2.3 ou plus et que vous souhaitez passer à Bundler 2.0, utilisez ce qui suit dans votre `.travis.yml` : 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
  before_install:
    - gem install bundler
```

* Si vous utilisez une version de Ruby inférieure à 2.6 et que vous souhaitez utiliser Bundler 2.x, assurez-vous de passer à une version plus récente de RubyGems. Sur la version par défaut de RubyGems livrée avec des versions plus anciennes de Ruby, la version Bundler dans `Gemfile.lock` doit correspondre exactement à la version utilisée, ou elle sera erronée. C'est fixé dans les versions plus récentes de Rubygems. 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
  before_install:
  - yes | gem update --system --force
  - gem install bundler
```

Il est nécessaire d'utiliser la commande `gem update --system` avec l'option `yes`, car il existe un problème distinct lié aux anciennes versions de RubyGems fournies avec un binstub défectueux, qui demande une confirmation interactive de la part de l'utilisateur.

* Si vous utilisez Ruby 2.3.x mais souhaitez rester explicitement sur Bundler 1.x (par exemple, pour des raisons de dépendance telles que Rails 4.2.x), écrivez : 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
  before_install:
    - gem uninstall -v '>= 2' -i $(rvm gemdir)@global -ax bundler || true
    - gem install bundler -v '< 2'
```

La commande `gem uninstall` ci-dessus supprime tout Bundler 2.x installé dans Gemset «global» de RVM lors de l'installation Ruby par RVM, qui serait sélectionné comme commande de `bundle` par défaut. Nous ignorons l'échec de cette commande, car l'échec signifie probablement qu'il n'y avait pas de version de Bundler correspondante à désinstaller.

Votre configuration de construction peut nécessiter une combinaison de ces solutions de contournement.

**Cache Bundler**

L'installation de Bundler peut prendre un certain temps, ralentissant votre construction. Vous pouvez dire à Travis CI de [mettre en cache le bundle installé](./mise-en-cache-des-dependances.md).

Lors de votre première construction, nous réveillons le cache. Lors de la deuxième, nous récupérons le cache, ce qui permet à l'installation du bundle de ne prendre que quelques secondes.

**Accélérez la compilation en excluant les dépendances non essentielles**

Beaucoup de projets incluent des bibliothèques comme `ruby-debug`, `unicorn` ou `newrelic_rpm` dans leur ensemble de _gems_ par défaut.

Cela ralentit beaucoup le processus d'installation, et généralement, ces bibliothèques ne sont pas nécessaires lors de l'exécution de vos tests. Cela comprend également des bibliothèques qui compilent le code natif, ralentissant encore plus l'installation et les temps de test globaux.

De plus, les bibliothèques qui intègrent implicitement `ruby_core_source` ou `linecache19` sont vouées à échouer lorsque Travis CI met à niveau les versions et les niveaux de correctif de Ruby.

Il en va de même pour les _gems_ dont vous avez seulement besoin en production, comme Unicorn, la bibliothèque New Relic, etc.

Vous pouvez accélérer votre processus d'installation en déplaçant ces bibliothèques dans une section distincte de votre Gemfile, par exemple, `production:`.

```console
group :production do
  gem 'unicorn'
  gem 'newrelic_rpm'
end
```

Ajustez vos arguments Bundler pour exclure explicitement ce groupe :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
bundler_args: --without production
```

Profitez d'une construction plus rapide, qui est également moins sujette aux problèmes de compilation.

**Arguments de Bundler personnalisés et emplacements Gemfile**

L'emplacement de Gemfile par défaut est le `Gemfile` à l'origine de votre projet.

Pour spécifier un nom ou un emplacement Gemfile personnalisé:

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
gemfile: gemfiles/Gemfile.ci
```

Si vous spécifiez l'emplacement de votre Gemfile de cette manière, la construction échouera si le fichier n'est pas trouvé.

Vous pouvez transmettre des [arguments supplémentaires](http://bundler.io/v1.3/man/bundle-install.1.html) à `bundle install:` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
bundler_args: --binstubs
```

<h3 id="plusieurs-versions">Tester avec plusieurs versions de dépendances.
<a href="#plusieurs-versions" class="ancre-titre after"></a></h3>

De nombreux projets doivent être testés avec plusieurs versions de RACK, Eventmachine, Haml, Sinatra, Ruby on Rails, etc.

Pour tester avec plusieurs versions de dépendances : 

1. Créez un répertoire dans la racine du référentiel de votre projet où vous garderez des gemfiles, tels que `./gemfiles` ;
2. Ajoutez-y un ou plusieurs gemfiles ;
3. Réglez la clé `gemfile` dans votre `.travis.yml`.

Paperclip de Thoughtbot est {testé sur plusieurs versions d'ActiveRecord 
Gemfile](https://github.com/thoughtbot/paperclip/blob/master/.travis.yml) :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
gemfile:
  - gemfiles/rails2.gemfile
  - gemfiles/rails3.gemfile
  - gemfiles/rails3_1.gemfile
```

Une alternative à cela consiste à utiliser des variables d'environnement et à faire en sorte que votre exécuteur de test les utilise. Par exemple, [Sinatra est testé avec plusieurs versions de Tilt et de Rack](https://github.com/sinatra/sinatra/blob/master/.travis.yml ):

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
env:
  - "rack=1.3.4"
  - "rack=master"
  - "tilt=1.3.3"
  - "tilt=master"
```

ChefSpec est [testé avec plusieurs versions de Chef](https://github.com/chefspec/chefspec/blob/master/.travis.yml ):

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
env:
  - CHEF_VERSION=14.3.37
  - CHEF_VERSION=13.10.0
  - CHEF_VERSION=12.22.5
```

La même technique est souvent utilisée pour tester plusieurs bases de données, moteurs de modèles, fournisseurs de services hébergés, etc.

<h3 id="environnement">Variable d'environnement $BUNDLE_GEMFILE.
<a href="#environnement" class="ancre-titre after"></a></h3>

Lorsque `gemfile` est défini _et_ qu'un fichier Gemfile existe dans le référentiel, nous définissons la variable d'environnement `$BUNDLE_GEMFILE`, que `bundle install` utilise pour résoudre les dépendances.

Si vous avez besoin de travailler avec plusieurs gemfiles dans une seule tâche, remplacez `$BUNDLE_GEMFILE` en passant le drapeau `--gemfile=`:

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
bundle install --gemfile=my_gemfile
```

<h2 id="jdks">JRuby : test avec plusieurs JDKs.
<a href="#jdks" class="ancre-titre after"></a></h2>

Tester des projets avec plusieurs JDKs, en utilisant la clé `jdk`` dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jdk:
  - oraclejdk7
  - openjdk7
  - oraclejdk8
```

Chaque JDK avec lequel vous testez créera des permutations avec toutes les autres configurations, donc pour éviter d'exécuter des tests pour, par exemple, CRuby 1.9.3 plusieurs fois, vous devez ajouter des exclusions de matrice (décrite dans notre [guide de configuration de build](./personnalisation-de-la-construction.md)) :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
rvm:
  - 1.9.2
  - jruby-18mode
  - jruby-19mode
  - jruby-head
jdk:
  - openjdk6
  - openjdk7
  - oraclejdk7
jobs:
  exclude:
    - rvm: 1.9.2
      jdk: openjdk6
    - rvm: 1.9.2
      jdk: openjdk7
    - rvm: 1.9.2
      jdk: oraclejdk7
```

Par un exemple, [voir le support Travis](https://github.com/travis-ci/travis-support/blob/master/.travis.yml).

<h3 id="java10">Utilisez Java 10 et plus.
<a href="#java10" class="ancre-titre after"></a></h3>

Pour tester avec Open JDK et OracleJDK 10 et plus, voir la [documentation Java](./construire-un-projet-java.md/#java10-et-plus).

<h2 id="rubygems">Mise à niveau de RubyGems.
<a href="#rubygems" class="ancre-titre after"></a></h2>

La version RubyGems installée sur l'environnement Ruby de Travis CI dépend de ce qui est installé par la nouvelle combinaison Bundler/Rubygems, et est maintenu aussi à jour que possible.

Si vous avez besoin de la dernière version de RubyGems, vous pouvez ajouter ce qui suit à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - gem update --system
  - gem --version
```

Pour rétrograder vers une version spécifique de RubyGems :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - gem update --system 2.1.11
  - gem --version
```

Veuillez noter que cela aura une incidence sur la durée totale du test, car des téléchargements et des installations supplémentaires sur le réseau seront nécessaires.
<div class="invisible">

