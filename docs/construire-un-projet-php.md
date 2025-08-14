# Construire un projet PHP

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#test-php">Test avec différentes versions de PHP</a></li>
            <li><a href="#script-par-defaut">Script de construction par défaut</a></li>
            <li><a href="#gestion-des-dependances-php">Gestion des dépendances</a></li>
            <li><a href="#installation-php">Installation de PHP</a></li>
            <li><a href="#configuration-php">Configuration personnalisée de PHP</a></li>
            <li><a href="#extensions-php">Activer les extensions PHP préinstallées</a></li>
            <li><a href="#desactiver-extensions-php">Désactiver les extensions PHP préinstallées</a></li>
            <li><a href="#extensions-supplementaires-php">Installer des extensions PHP supplémentaires</a></li>
            <li><a href="#reference-de-configuration-de-construction-php">Référence de configuration de construction</a></li>
            <li><a href="#exemples-php">Exemples</a></li>
            <li><a href="#tableau-versions-php">Versions de PHP</a></li>
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
    <li><a href="environnement-noble.md">Noble</a>.</li>
  </ul>
  </p>
  </div>

<p>
Ce guide traite de la configuration des projets PHP sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.
</p>

<p>
<table class="tableau">
    <thead>
        <tr><th>PHP</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="#gestion-des-dependances-php">Défaut<code>install</code></td>
            <td>N/A</td>
        </tr>
        <tr>
            <td><a href="#script-de-build-par-defaut">Défaut<code>script</code></td>
            <td><code>phpunit</code></td>
        </tr>
        <tr>
            <td>Cle de matrice</a></td>
            <td><code>env</code>,<code>php</code><td>
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
language: php
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>

<p>
<h2 id="test-php">Test avec différentes versions de PHP.
<a href="#test-php" class="ancre-titre after"></a></h2>

Travis CI fournit plusieurs versions PHP, qui incluent tous XDebug et Phpunit. Travis CI utilise [`phpenv`](https://github.com/CHH/phpenv) pour gérer les différentes versions PHP installées sur les machines virtuelles.
</p>

Un exemple de fichier `.travis.yml` qui teste diverses versions PHP :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: php
php:
  - '5.4'
  - '5.6'
  - '7.0'
  - hhvm # on Trusty only
  - nightly
```

Les versions `X.Y` sont des alias pour les versions récentes `X.Y.Z` préinstallées sur les images de construction. Pour les versions exactes utilisées dans votre build, consultez les «informations du système de construction» dans le journal de construction.

Ce ne sont peut-être pas les versions les plus récentes. Si vous devez assurer l'utilisation des versions les plus récentes, fournissez le troisième numéro ; par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: php
php:
  - 7.1.9
```

<h3 id="versions-php">Versions PHP prises en charge.
<a href="#versions-php" class="ancre-titre after"></a></h3>

La liste des versions PHP disponibles pour l'installation à la demande peut être trouvée dans le [tableau ci-dessous](#tableau-versions-php).

<h3 id="versions-php-avec-trusty">Testez les versions HHVM avec Trusty.
<a href="#versions-php-avec-trusty" class="ancre-titre after"></a></h3>

Travis CI peut tester vos applications PHP avec HHVM sur Ubuntu Trusty :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
php:
  - hhvm-3.18
  - hhvm-nightly
```

Veuillez noter que si vous souhaitez exécuter PHPUnit sur HHVM, vous devez installer explicitement la version 5.7 dans votre `.travis.yml` en raison d'un problème de compatibilité entre HHVM et PHP7 :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
  - curl -sSfL -o ~/.phpenv/versions/hhvm/bin/phpunit https://phar.phpunit.de/phpunit-5.7.phar
```

<h3 id="compilation-nocturne">Compilation nocturne.
<a href="#compilation-nocturne" class="ancre-titre after"></a></h3>

Travis CI peut tester vos applications PHP avec une construction [PHP](https://github.com/php/php-src/) nocturne, qui comprend le PHPUnit et Composer, mais n'inclut pas les extensions PHP tierces :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: php
php:
  - nightly
```

<h2 id="script-par-defaut">Script de construction par défaut.
<a href="#script-par-defaut" class="ancre-titre after"></a></h2>

Le script de construction par défaut est PHPUnit.  Il est fourni avec PHP, mais vous pouvez également installer une version spécifique à un emplacement personnalisé. Si vous l'installez séparément, veillez à invoquer la bonne version en utilisant le chemin d'accès complet.

Travis CI recherche `phpunit` dans le [même ordre que Composer le fait](https://getcomposer.org/doc/articles/vendor-binaries.md#can-vendor-binaries-be-installed-somewhere-other-than-vendor-bin-) et utilise le premier trouvé. 

1. `$COMPOSER_BIN_DIR/phpunit` ;
2. `phpunit` trouvé dans le répertoire spécifié par `bin-dir` dans `composer.json`  ;
3. `vendor/bin/phpunit` ;
4. `phpunit`, qui se trouve dans `$PATH` (généralement préinstallé avec le runtime PHP).

Si votre projet utilise autre chose que PHPUnit, vous pouvez [remplacer le script de construction par défaut](https://docs.travis-ci.com/user/customizing-the-build/).

<h3 id="atoum">Travaillez avec atoum.
<a href="#atoum" class="ancre-titre after"></a></h3>

Au lieu de PHPUnit, vous pouvez également utiliser [atoum](https://github.com/atoum/atoum) pour tester vos projets. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script: composer require atoum/atoum
script: vendor/bin/atoum
```

<h2 id="gestion-des-dependances-php">Gestion des dépendances.
<a href="#gestion-des-dependances-php" class="ancre-titre after"></a></h2>

Avant que Travis CI puisse exécuter votre suite de tests, il peut être nécessaire d'installer vos dépendances de projet. Cela peut être fait à l'aide d'un script PHP, d'un script shell ou de tout ce dont vous avez besoin. Définissez une ou plusieurs commandes que vous souhaitez que Travis CI utilise avec l'option _install_ dans votre `.travis.yml`, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install: php vendor/vendors.php
```

ou, si vous devez exécuter plusieurs commandes séquentiellement :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
  - ./bin/ci/install_dependencies.sh
  - php vendor/vendors.php
```

<h3 id="plusieurs-versions">Tester avec plusieurs versions de dépendances.
<a href="#plusieurs-versions" class="ancre-titre after"></a></h3>

Si vous devez tester plusieurs versions de Symfony par exemple, vous pouvez demander à Travis CI d'effectuer plusieurs exécutions avec différents ensembles ou valeurs de variables d'environnement. Utilisez la clé `env` dans votre fichier `.travis.yml`, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
env:
  - SYMFONY_VERSION="2.0.*" DB=mysql
  - SYMFONY_VERSION="dev-master" DB=mysql
```

puis utilisez des valeurs de variables ENV dans n'importe quel script ultérieur comme les scripts d'installation de vos dépendances, les cas de test ou les valeurs de paramètres de script de test.

Voici un exemple utilisant la variable ENV ci-dessus pour modifier les dépendances lors de l'utilisation du gestionnaire de paquetage Composer pour exécuter les tests par rapport aux 2 versions différentes de Symfony telles que définies ci-dessus.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
   - composer require symfony/framework-bundle:${SYMFONY_VERSION}
```

Ici, nous utilisons la valeur de variable DB pour choisir le fichier de configuration phpunit : 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
 script: phpunit --configuration $DB.phpunit.xml
```

La même technique est souvent utilisée pour tester des projets dans plusieurs bases de données, etc.

Pour voir des exemples du monde réel, voir : 

* [FOSRest](https://github.com/FriendsOfSymfony/FOSRest/blob/master/.travis.yml) ;
* [LiipHyphenatorBundle](https://github.com/liip/LiipHyphenatorBundle/blob/master/.travis.yml) ;
* [doctrine2](https://github.com/doctrine/doctrine2/blob/master/.travis.yml).

<h3 id="installer-paquetages-composer">Installer les paquetages Composer.
<a href="#installer-paquetages-composer" class="ancre-titre after"></a></h3>

Notez que nous mettons à jour Composer chaque fois que nous mettons à jour l'environnement de construction PHP, qui est tous les 30 à 60 jours. Parce que Composer a un avertissement de mise à jour basé dans le temps, vous pouvez voir des messages comme celui-ci, qui peuvent être ignorés en toute sécurité :

```console
AVERTISSEMENT: Cette construction de développement de Composer a plus de 30 jours. Il est recommandé de le mettre à jour en exécutant "/home/travis/.phpenv/versions/5.6/bin/composer self-update" pour obtenir la dernière version.
```

Vous pouvez également installer des paquetages [Composer](http://packagist.org/) dans l'environnement Travis CI PHP. La commande Composer est préinstallée, utilisez ce qui suit :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
composer install
```

Pour vous assurer que tout fonctionne, utilisez des URLs http(s) sur [Packagist](http://packagist.org/) et non les URLs git.

<h2 id="installation-php">Installation de PHP.
<a href="#installation-php" class="ancre-titre after"></a></h2>

Vous trouverez [ici](https://github.com/travis-ci/travis-cookbooks/blob/precise-stable/ci_environment/phpbuild/templates/default/default_configure_options.erb) les options de configuration par défaut utilisées pour créer les différentes versions PHP utilisées sur Travis CI , elle vous donnera un aperçu de l'installation PHP de Travis CI.

Veuillez noter les différences suivantes entre les différentes versions PHP disponibles sur Travis CI : 

* L'extension OpenSSL est désactivée sur PHP 5.3.3 en raison de [problèmes de compilation avec OpenSSL 1.0](https://travis-ci.com/blog/upcoming_ubuntu_11_10_migration/) ;
* Différents SAPIs : 
    * 5.3.3 est livré avec PHP-CGI uniquement ;
    * 5.3.x (5.3.29) est livré avec PHP-FPM uniquement (voir ce problème) ;
    * 5.4.x, 5.5.x et 5.6.x sont livrés avec PHP-CGI et PHP-FPM.

<h2 id="configuration-php">Configuration personnalisée de PHP.
<a href="#configuration-php" class="ancre-titre after"></a></h2>

La façon la plus simple de personnaliser votre configuration PHP est d'utiliser `phpenv config-add` pour ajouter un fichier de configuration personnalisé avec vos directives de configuration :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script: phpenv config-add myconfig.ini
```

<div class="notice cadre">
Assurez-vous que votre fichier de configuration ne commence pas par un point (.) ou un trait d'union (-) car cela empêchera PHP de charger vos paramètres personnalisés.
</div>

Et `myconfig.ini` :

<div class="titre-code">
    <div class="item gauche">&nbsp;myconfig.ini</div><div class="item droit">Ini</div>
</div>
```ini
extension = "mongo.so"
date.timezone = "Europe/Paris"
default_socket_timeout = 120
# some other configuration directives...
```

Vous pouvez également utiliser cette commande unique dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script: echo 'date.timezone = "Europe/Paris"' >> ~/.phpenv/versions/$(phpenv version-name)/etc/conf.d/travis.ini
```

<h2 id="extensions-php">Activer les extensions PHP préinstallées.
<a href="#extensions-php" class="ancre-titre after"></a></h2>

Vous devez les activer en ajoutant une ligne `extension="<extension>.so"` à un fichier de configuration PHP (pour la version actuelle de PHP). La manière la plus simple de procéder consiste à utiliser `phpenv` pour ajouter un fichier de configuration personnalisé qui active et configure l'extension :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install: phpenv config-add myconfig.ini
```

<div class="notice cadre">
Assurez-vous que votre fichier de configuration ne commence pas par un point (.) ou un trait d'union (-) car cela empêchera PHP de charger vos paramètres personnalisés.
</div>

Et `myconfig.ini` :

<div class="titre-code">
    <div class="item gauche">&nbsp;myconfig.ini</div><div class="item droit">Ini</div>
</div>
```ini
extension="mongo.so"
# some other mongo specific configuration directives
# or general custom PHP settings...
```

Vous pouvez également utiliser cette commande d'une seule ligne :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install: echo "extension = <extension>.so" >> ~/.phpenv/versions/$(phpenv version-name)/etc/php.ini
```

<h2 id="desactiver-extensions-php">Désactiver les extensions PHP préinstallées.
<a href="#desactiver-extensions-php" class="ancre-titre after"></a></h2>

Pour désactiver xdebug, ajoutez-le à votre configuration:

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
  - phpenv config-rm xdebug.ini
```

<h2 id="extensions-supplementaires-php">Installer des extensions PHP supplémentaires.
<a href="#extensions-supplementaires-php" class="ancre-titre after"></a></h2>

Il est possible d'installer des extensions PHP personnalisées dans l'environnement Travis CI à l'aide de [PECL](http://pecl.php.net/), mais ils doivent être construits avec la version PHP testée.

Par exemple, pour installer `memcache` :

```console
pecl install <extension>
```

PECL activera automatiquement l'extension à la fin de l'installation. Si vous souhaitez configurer votre extension, utilisez la commande `phpenv config-add` pour ajouter un fichier de configuration ini personnalisé dans votre before_script.

Il est également possible de faire l'installation «manuellement», mais vous devrez activer manuellement l'extension après l'installation avec `phpenv config-add` et un fichier ini personnalisé ou avec cette commande d'une  ligne :

```console
echo "extension=<extension>.so" >> ~/.phpenv/versions/$(phpenv version-name)/etc/php.ini
```

Voir aussi le [script complet à l'aide de midgard2](https://github.com/bergie/midgardmvc_core/blob/master/tests/travis_midgard2.sh).

Si vous avez besoin d'une version spécifique de l'extension préinstallée, vous devez forcer l'installation de version spécifique avec l'indicateur `-f`. Par exemple :

```console
pecl install -f mongo-1.2.12
```

<h3 id="pecl-install">Remarque sur pecl install.
<a href="#pecl-install" class="ancre-titre after"></a></h3>

Notez que `pecl install` peut échouer si la version demandée du package est déjà installée.

<h3 id="apache-et-php">Apache + php.
<a href="#apache-et-php" class="ancre-titre after"></a></h3>

Actuellement, Travis CI ne prend pas en charge `mod_php` pour Apache, mais vous pouvez configurer `php-fpm` pour vos tests d'intégration :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
  - sudo apt-get update
  - sudo apt-get install apache2 libapache2-mod-fastcgi
  # enable php-fpm
  - sudo cp ~/.phpenv/versions/$(phpenv version-name)/etc/php-fpm.conf.default ~/.phpenv/versions/$(phpenv version-name)/etc/php-fpm.conf
  - sudo a2enmod rewrite actions fastcgi alias
  - echo "cgi.fix_pathinfo = 1" >> ~/.phpenv/versions/$(phpenv version-name)/etc/php.ini
  - sudo sed -i -e "s,www-data,travis,g" /etc/apache2/envvars
  - sudo chown -R travis:travis /var/lib/apache2/fastcgi
  - ~/.phpenv/versions/$(phpenv version-name)/sbin/php-fpm
  # configure apache virtual hosts
  - sudo cp -f build/travis-ci-apache /etc/apache2/sites-available/000-default.conf
  - sudo sed -e "s?%TRAVIS_BUILD_DIR%?$(pwd)?g" --in-place /etc/apache2/sites-available/000-default.conf
  - sudo service apache2 restart
```

<div class="notice cadre">
Notez que <code>sudo</code> n'est pas disponible pour les builds qui fonctionnent sur des <a href="https://docs.travis-ci.com/user/workers/container-based-infrastructure">conteneurs</a>.
</div>

Vous devrez avoir un fichier `build/travis-ci-apache` qui configurera votre hôte virtuel comme d'habitude, la partie importante de php-fpm est la suivante:

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Configuration Apache</div>
</div>
```configuration
<VirtualHost *:80>
  # [...]

  DocumentRoot %TRAVIS_BUILD_DIR%

  <Directory "%TRAVIS_BUILD_DIR%/">
    Options FollowSymLinks MultiViews ExecCGI
    AllowOverride All
    Require all granted
  </Directory>

  # Wire up Apache to use Travis CI's php-fpm.
  <IfModule mod_fastcgi.c>
    AddHandler php5-fcgi .php
    Action php5-fcgi /php5-fcgi
    Alias /php5-fcgi /usr/lib/cgi-bin/php5-fcgi
    FastCgiExternalServer /usr/lib/cgi-bin/php5-fcgi -host 127.0.0.1:9000 -pass-header Authorization

    <Directory /usr/lib/cgi-bin>
        Require all granted
    </Directory>
  </IfModule>

  # [...]
</VirtualHost>
```

<h2 id="reference-de-configuration-de-construction-php">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-php" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [PHP](https://config.travis-ci.com/ref/language/php) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="exemples-php">Exemples.
<a href="#exemples-php" class="ancre-titre after"></a></h2>

* [Drupal](https://github.com/sonnym/travis-ci-drupal-module-example).

<h2 id="tableau-versions-php">Versions de PHP.
<a href="#tableau-versions-php" class="ancre-titre after"></a></h2>

[Ces archives sont disponibles pour l'installation à la demande](https://docs.travis-ci.com/user/languages/php/#php-versions).
<div class="invisible">