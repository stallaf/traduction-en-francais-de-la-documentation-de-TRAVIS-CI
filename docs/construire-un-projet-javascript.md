# Construire un projet JavaScript et Node.js

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#specifier-versions-nodejs">Spécifier les versions Node.js</a></li>
            <li><a href="#versions-avec-nvmrc">Spécifier les versions Node.js à l'aide de .nvmrc</a></li>
            <li><a href="#script-de-build-par-defaut">Script de construction par défaut</a></li>
            <li><a href="#utiliser-gulp">Utiliser Gulp</a></li>
            <li><a href="#gestion-des-dependances-javascript">Gestion des dépendances</a></li>
            <li><a href="#ember">Applications Ember</a></li>
            <li><a href="#meteor">Applications Meteor</a></li>
            <li><a href="#paquetages-meteor"> Paquetages Meteor</a></li>
            <li><a href="#exigences-du-compilateur">Exigences du compilateur Node.js v4 (ou io.js v3)</a></li>
            <li><a href="#reference-de-compilation-de-construction-javascript">Référence de configuration de construction<a></li>
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
    <li><a href="environnement-macos.md">macOS</a> ;</li>
   <li><a href="environnement-windows.md">Windows</a> ;</li>
    <li><a href="environnement-freebsd.md">FreeBSD</a> ;</li>
  </ul>
  </p>
  </div>

Ce guide traite de la configuration des projets JavaScript et Node.js sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.
<p>
<table class="tableau">
    <thead>
        <tr><th>JAVASCRIPT et NODE.JS</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="#gestion-des-dependances-javascript">Défaut<code>install</code></td>
            <td><code>npm install</code>ou<code>npm ci</code></td>
        </tr>
        <tr>
            <td><a href="#script-de-buld-par-defaut">Défaut<code>script</code></td>
            <td><code>npm test</code></td>
        </tr>
        <tr>
            <td>Cle de matrice</a></td>
            <td><code>env</code>,<code>node_js</code><td>
        </tr>
        <tr>
            <td>Support</td>
            <td><a href="mailtosupport@travis-ci.com">Travis CI</a></td>
        </tr>
    </tbody>
    <tfoot>
        <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
        <tr><th colspan="2" text-align="left" style="font-weight:normal";>(Si le fichier <code>package-lock.json</code>ou <code>npm-shrinkwrap.json</code> existe et que votre version de npm le prend en charge, Travis CI utilisera <code>npm ci</code> à la place de <code>npm install</code>.)</th></tr>
        <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
        <tr><th colspan="2" text-align="left">Exemple minimal</th></tr>
        <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
        <tr><th colspan="2">
            <div class="titre-code">
                <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
            </div>
```yml
language: node_js
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>

<h2 id="specifier-versions-nodejs">Spécifier les versions Node.js.
<a href="#specifier-versions-nodejs" class="ancre-titre after"></a></h2>

Le moyen le plus simple de spécifier les versions Node.js consiste à utiliser une ou plusieurs des dernières versions dans votre fichier `.travis.yml` :

* `node` Dernière version stable Node.js ; 
* `lts/*` Dernière version LTS Node.js ;
* 24 Dernière version 24.x ;
* 23 Dernière version 23.x ;
* 22 Dernière version 22.x ;
* 21 Dernière version 21.x ;
* 20 Dernière version 20.x.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: node_js
node_js:
  - 7
```

Des informations plus spécifiques sur les versions de Node.js sont disponibles dans les pages de référence de l'environnement: 

* [Precise](./environnement-precise.md#images-javasript-et-nodejs) ;
* [Trusty](./environnement-trusty.md#images-javasript-et-nodejs).

Si vous avez besoin d'un contrôle plus spécifique des versions Node.js dans votre build, utilisez n'importe quelle version instalable par `nvm``. Si votre `.travis.yml` contient une version de Node.js que `nvm` ne peut pas installer, comme `0,4`, la tâche échoue immédiatement.

Pour une liste précise des versions préinstallées sur la machine virtuelle, veuillez consulter «Build Informations System» dans le journal de construction.

<h2 id="versions-avec-nvmrc">Spécifier les versions Node.js à l'aide de .nvmrc.
<a href="#versions-avec-nvmrc" class="ancre-titre after"></a></h2>

Facultativement, votre référentiel peut contenir un fichier `.nvmrc` dans la racine du référentiel pour spécifier la version unique de Node.js et exécuter vos tests.

Le fichier `.nvmrc` n'est lu que lorsque la clé `node_js` dans vos fichiers `.travis.yml` ne spécifie pas de version NodeJS. Lorsque le fichier `.nvmrc` est lu, `$TRAVIS_NODE_VERSION` est défini sur la version nodejs. Voir la [documentation nvm](https://github.com/creationix/nvm#usage) pour plus d'informations sur `.nvmrc`.

<h2 id="script-de-build-par-defaut">Script de construction par défaut.
<a href="#script-de-build-par-defaut" class="ancre-titre after"></a></h2>

Le script de création par défaut pour les projets utilisant NodeJS est :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
npm test
```

Dans le cas où aucun fichier `package.json` n'est présent dans le dossier racine, le script de construction par défaut est :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
make test
```

<h3 id="support-yarn">Support Yarn.
<a href="#support-yarn" class="ancre-titre after"></a></h3>

Si `yarn.lock` existe, la commande de test par défaut sera `yarn test` au lieu de `npm test`.

<h3 id="autres-tests">Utilisez d'autres suites de test.
<a href="#autres-tests" class="ancre-titre after"></a></h3>

Vous pouvez dire à npm comment exécuter votre suite de tests en ajoutant une ligne dans `package.json`. Par exemple, pour tester à l'aide de Vows :

<div class="titre-code">
    <div class="item gauche">&nbsp;pakage.json</div><div class="item droit">JSON</div>
</div>
```json
"scripts": {
  "test": "vows --spec"
},
```

<h2 id="utiliser-gulp">Utiliser Gulp.
<a href="#utiliser-gulp" class="ancre-titre after"></a></h2>

Si vous utilisez déjà Gulp pour gérer vos tests, installez-le et exécutez le `gulpfile.js` par défaut en ajoutant les lignes suivantes à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
  - npm install -g gulp-cli
script: gulp
```

<h2 id="gestion-des-dependances-javascript">Gestion des dépendances.
<a href="#gestion-des-dependances-javascript" class="ancre-titre after"></a></h2>

Travis CI utilise [npm](https://npmjs.org/) ou [yarn](https://yarnpkg.com/) pour installer les dépendances de votre projet. 

<div class="notice cadre">
Notez qu'il n'y a pas de paquetages npm installés par défaut dans l'environnement Travis CI.
</div>

<h3 id="utiliser-npm">Utiliser npm.
<a href="#utiliser-npm" class="ancre-titre after"></a></h3>

**Utiliser une version npm spécifique**

Ajoutez ce qui suit à la phase [`avant_install`](./cycle-de-vie-de-job.md) de `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - npm i -g npm@version-number
```

<h3 id="utiliser-npm-ci">Prise en charge de npm CI.
<a href="#utiliser-npm-ci" class="ancre-titre after"></a></h3>

Si `package-lock.json` ou `npm-shrinkwrap.json` existe et que votre version npm la prend en charge, Travis CI utilisera `npm ci` au lieu de `npm install`.

Cette commande supprimera votre dossier `node_modules` et installera toutes les dépendances comme spécifié dans votre fichier de verrouillage.

**Cache avec npm**

`npm` est maintenant mis en cache par défaut, au cas où vous souhaitez le désactiver, veuillez ajouter ce qui suit à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache:
  npm: false
```

Pour mettre en cache explicitement vos dépendances :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache: npm
```

1. Cela met en cache `$HOME/.npm` précisément lorsque `npm ci` est la commande `script` par défaut. (Voir ci-dessus.)
2. Dans tous les autres cas, cela mettra en cache `node_modules.` Notez que `npm install` continuera à s'exécuter à chaque compilation et mettra à jour/installera tous les nouveaux paquets ajoutés à votre fichier `package.json`.

Même lorsque `script` est remplacé, ce raccourci est efficace.

<h3 id="utiliser-yarn">Utiliser Yarn.
<a href="#utiliser-yarn" class="ancre-titre after"></a></h3>

Travis CI détecte l'utilisation de [Yarn](https://yarnpkg.com/).

Si `package.json` et `yarn.lock` sont présents dans le répertoire actuel, nous exécutons la commande suivante _au lieu de_ `npm install` :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
yarn --frozen-lockfile
```

Si votre version de Yarn ne prend pas en charge `--frozen-lockfile`, nous exécutons simplement `yarn`.

Notez que `yarn` nécessite Node.js version 4 ou ultérieure. Si la tâche ne répond pas à cette exigence, `npm install` est utilisée à la place.

**Utilisez une version spécifique de Yarn**

Ajoutez ce qui suit à la [phase `before_install`](./cycle-de-vie-de-job.md) de `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - curl -o- -L https://yarnpkg.com/install.sh | bash -s -- --version version-number
  - export PATH="$HOME/.yarn/bin:$PATH"
```

**Cache avec yarn**

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache: yarn
```

Ceci ajoutera le répertoire de mise en cache par défaut de `yarn` (qui varie en fonction du système d'exploitation), comme indiqué par [`yarn cache dir`](https://yarnpkg.com/en/docs/cli/cache#toc-yarn-cache-dir).

Si votre mise en cache doit inclure d'autres directives, vous pouvez utiliser :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache:
  yarn: true
```

Pour plus d'informations, reportez-vous à la documentation de [mise en cache](./mise-en-cache-des-dependances.md).

<h3 id="shrinkwrapped">Utilisez des dépendances GIT shrinkwrapped.
<a href="#shrinkwrapped" class="ancre-titre after"></a></h3>

Notez que `npm install` peut échouer si une dépendance git shrinkwrapped pointant vers une branche voit son HEAD modifié.

<h2 id="ember">Applications Ember.
<a href="#ember" class="ancre-titre after"></a></h2>

Vous pouvez créer vos applications Ember sur Travis CI. Le framework de test par défaut est `Qunit`. L'exemple suivant montre comment créer et tester différentes versions d'Ember.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
dist: trusty
addons:
  apt:
    sources:
      - google-chrome
    packages:
      - google-chrome-stable
language: node_js
node_js:
  - "7"
env:
    - EMBER_VERSION=default
    - EMBER_VERSION=release
    - EMBER_VERSION=beta
    - EMBER_VERSION=canary
jobs:
  fast_finish: true
  allow_failures:
    - env: EMBER_VERSION=release
    - env: EMBER_VERSION=beta
    - env: EMBER_VERSION=canary
before_install:
    # setting the path for phantom.js 2.0.0
    - export PATH=/usr/local/phantomjs-2.0.0/bin:$PATH
    # starting a GUI to run tests, per https://docs.travis-ci.com/user/gui-and-headless-browsers/#using-xvfb-to-run-tests-that-require-a-gui
    - export DISPLAY=:99.0
    - sh -e /etc/init.d/xvfb start
    - "npm config set spin false"
    - "npm install -g npm@^2"
install:
    - mkdir travis-phantomjs
    - wget https://s3.amazonaws.com/travis-phantomjs/phantomjs-2.0.0-ubuntu-12.04.tar.bz2 -O $PWD/travis-phantomjs/phantomjs-2.0.0-ubuntu-12.04.tar.bz2
    - tar -xvf $PWD/travis-phantomjs/phantomjs-2.0.0-ubuntu-12.04.tar.bz2 -C $PWD/travis-phantomjs
    - export PATH=$PWD/travis-phantomjs:$PATH
    - npm install -g bower
    - npm install
    - bower install
script:
    - ember test --server
```

<h2 id="meteor">Applications Meteor.
<a href="#meteor" class="ancre-titre after"></a></h2>

Vous pouvez créer vos applications Metor sur Travis CI et tester avec [`laika`](http://arunoda.github.io/laika/ ):

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: node_js
node_js:
  - "7"
before_install:
  - "curl -L https://raw.githubusercontent.com/arunoda/travis-ci-laika/6a3a7afc21be99f1afedbd2856d060a02755de6d/configure.sh | /bin/sh"
services:
  - mongodb
env:
  - LAIKA_OPTIONS="-t 5000"
```

Plus d'informations sur les [tests avec laika](https://github.com/arunoda/travis-ci-laika)
.

<h2 id="paquetages-meteor">Paquetages Meteor.
<a href="#paquetages-meteor" class="ancre-titre after"></a></h2>

Vous pouvez également créer vos paquetages Meteor sur Travis CI en étendant la configuration Node.js.

Le script `before_install` suivant installe les dépendances requises :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: node_js
node_js:
  - "7"
before_install:
  - "curl -L https://raw.githubusercontent.com/arunoda/travis-ci-meteor-packages/dca8e51fafd60d9e5a8285b07ca34a63f22a5ed4/configure.sh | /bin/sh"
before_script:
  - "export PATH=$HOME/.meteor:$PATH"
```

Trouvez ici  le code source de [Travis-Ci-Meteor-Packages](https://github.com/arunoda/travis-ci-meteor-packages).

<h2 id="exigences-du-compilateur">Exigences du compilateur Node.js v4 (ou io.js v3).
<a href="#exigences-du-compilateur" class="ancre-titre after"></a></h2>

Pour compiler les modules natifs pour IO.js v3 ou node.js v4 ou version ultérieure, un compilateur compatible [standard C++11](https://en.wikipedia.org/wiki/C%2B%2B11) est requis. Plus précisément, GCC 4.8 (ou ultérieur), soit Clang 3.5 (ou ultérieur).

Nos images Trusty ont GCC et Clang qui répondent à cette exigence, mais l'image Precise ne le fait pas.

Pour mettre à jour ces compilateurs à une version plus récente, par exemple, ` gcc/g++` vers la version 4.8, ajoutez ce qui suit dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: node_js
node_js:
  - "4"
env:
  - CXX=g++-4.8
addons:
  apt:
    sources:
      - ubuntu-toolchain-r-test
    packages:
      - g++-4.8
```

<h2 id="reference-de-compilation-de-construction-javascript">Référence de configuration de construction.<a href="#reference-de-compilation-de-construction-javascript" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [JavaScript](https://config.travis-ci.com/ref/language/node_js) dans notre [référence de configuration Travis CI Build](https://config.travis-ci.com/).
<hr class="invisible">

