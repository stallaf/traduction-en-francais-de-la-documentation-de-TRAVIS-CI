# Dépendances et répertoires du cache

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#repertoires-de-cache">Répertoires de cache</a></li>
            <li><a href="#elements-a-ne-pas-mettre-en-cache">Éléments à ne pas mettre en cache</a></li>
            <li><a href="#recuperation-et-stockage-des-caches">Récupération et stockage des caches</a></li>
            <li><a href="#reference-de-configuration-de-construction">Référence de configuration de construction</a></li>
            <li><a href="#configuration">Configuration</a></li>
            <li><a href="#caches-et-matrices-de-construction">Caches et matrices de construction</a></li>
            <li><a href="#caches-et-autorisation-de-lecture">Caches et autorisations de lecture</a></li>
            <li><a href="#comment-fonctionne-la-mise-en-cache">Comment fonctionne la mise en cache</a></li>
        </ul>
        </ul>
    </div>
</div>
</p>

Travis CI peut mettre en cache du contenu qui ne change pas souvent pour accélérer votre processus de construction. **Pour utiliser la fonction de mise en cache**, dans vos paramètres de référentiel, définissez _Build pushed branches_ sur ON. 

* Travis CI récupère le cache pour chaque construction, y compris les branches et les _pull requests_ ;
* Si une branche n'a pas son propre cache, Travis CI récupère le cache de la branche par défaut du référentiel ;
* Il existe un cache par branche et par version du langage/ version du compilateur/ version du JDK/ emplacement Gemfile/ etc ;
* Seules les modifications apportées aux répertoires en cache à partir de poussées normales sont stockées. 

<div class="notice cadre">
Veuillez noter que le contenu du cache est disponible pour toute construction sur le référentiel, y compris les <i>Pull requests</i>, alors assurez-vous de ne pas mettre d'informations sensibles dans le cache. 
</div>

Lors de la création du cache, les liens symboliques ne sont pas suivis. Envisagez plutôt de mettre en cache les fichiers et les répertoires normaux.

<h2 id="repertoires-de-cache">Répertoires de cache.
<a href="#repertoires-de-cache" class="ancre-titre after"></a></h2>

Les caches permettent à Travis CI de stocker des répertoires (Bundler, dépendances) entre les builds, ce qui est utile pour stocker les dépendances qui prennent plus de temps à compiler ou à télécharger.

Notez que si un projet tiers, tel que Bundler, modifie l'emplacement où il stocke les dépendances, vous devrez peut-être spécifier le répertoire manuellement au lieu d'utiliser ce raccourci de mise en cache particulier. N'hésitez pas à nous contacter pour toute question, problème ou commentaire.

<h3 id="construire-des-phases">Construire des phases.
<a href="#construire-des-phases" class="ancre-titre after"></a></h3>

Travis CI télécharge le cache après la phase `script` de la construction, mais avant la suite `after_success` ou `after_failure`. 

<div class="notice cadre">
Si le cache ne peut pas être téléchargé, la tâche n'est pas marquée comme ayant échouée.
</div>

<h3 id="bundler">Bundler.
<a href="#bundler" class="ancre-titre after"></a></h3>

Sur les projets Ruby et Objective-C, l'installation de dépendances via [Bundler](http://bundler.io/) peut constituer une grande partie de la durée de construction. La mise en cache du bundle entre les constructions réduit considérablement le temps qu'une build prend à s'exécuter.

**Activer la mise en cache de Bundler**

Pour permettre la mise en cache de Bundler dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
cache: bundler
```

Chaque fois que vous mettez à jour votre paquet, Travis CI mettra également à jour le cache.

**Déterminez le chemin du paquet**

Travis CI fait de son mieux pour déterminer le chemin que Bundler utilise pour le stockage des dépendances.

Si vous avez des [arguments Bundler personnalisés](./langage-ruby.md#custom), et que ceux-ci incluent l'option `--path`, Travis CI utilisera ce chemin. Si `--path` est manquant mais `--deployment` est présent, il utilisera le `vendor/bundle`.

Sinon, il ajoutera automatiquement l'option `--path`. Dans ce cas, il utilisera la valeur de la variable d'environnement `BUNDLE_PATH` ou, en cas de manque, `vendor/bundle`.

**Mettre en cache et remplacer l'étape d'installation**

Le fait de passer outre l'étape `install` peut entraîner la perte du répertoire par la directive `cache: bundler`. Dans ce cas, observez où Bundler installe les gemmes et mettez ce répertoire en cache à l'aide de [cache.directories](#repertoires-arbitraires) .

**Nettoyage du paquet**

Lorsque vous utilisez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache: bundler
```

La commande `bundle clean` est exécutée avant le téléchargement du cache.

Dans les cas où cela n'est pas souhaitable, vous pouvez utiliser Spécifier les [répertoires arbitraires](#repertoires-arbitraires) pour le contourner. Voir [ce problème GitHub](https://github.com/travis-ci/travis-ci/issues/2518) pour plus d'informations.

<h3 id="cache-rvm">Version Cache RVM Ruby pour les projets non Ruby.
<a href="#cache-rvm" class="ancre-titre after"></a></h3>

Il existe des projets utilisant des machines non basées sur Ruby mais ayant des exécutions de rubis. Par exemple, une application NodeJS qui possède une suite de test fonctionnels Ruby.

Pour ces cas, l'installation d'une version de Ruby avec `rvm install 2.3.1` peut prendre plus de 3 minutes. Pour ces cas, vous pouvez mettre en cache l'installation de Ruby. 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
 cache:
    directories:
      - /home/travis/.rvm/
```

<h3 id="cocoapods">Cocoapods.
<a href="#cocoapods" class="ancre-titre after"></a></h3>

Pour les projets Objective-C, l'installation des dépendances via [CocoaPods](http://cocoapods.org/) peut prendre une bonne partie de votre build. La mise en cache des Pods compilés entre les builds permet de réduire ce temps.

**Activer les Cocoapods de mise en cache**

Vous pouvez activer la mise en cache Cocoapods pour votre référentiel en l'ajoutant à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: objective-c
cache: cocoapods
```

Si vous souhaitez activer la mise en cache Bundler et la mise en cache de Cocoapods, vous pouvez les énumérer tous les deux :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: objective-c
cache:
    bundler: true
    cocoapods: true
```

Notez que la mise en cache Cocoapods n'aura aucun effet si vous fournissez déjà le répertoire Pods dans votre référentiel Git.

**Déterminez le chemin de Podfile**

Par défaut, Travis CI supposera que votre Podfile est à la racine du référentiel. Si ce n'est pas le cas, vous pouvez spécifier où il se trouve, comme ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: objective-c
podfile: path/to/Podfile
```

<h3 id="cache-npm">Cache npm.
<a href="#cache-npm" class="ancre-titre after"></a></h3>

<div class="notice cadre">
Veuillez noter qu'à partir de juillet 2019, npm est mis en cache par défaut sur Travis CI.
</div>

Pour désactiver la mise en cache npm, utilisez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache:
    npm: false
```

Pour mettre explicitement en cache npm, utilisez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: node_js
node_js: '6' # or another
cache: npm
```

Cela met en cache `$HOME/.npm` ou `node_modules`, selon la structure du référentiel. Pour plus d'informations, [consultez la documentation Node.js](./langage-javascript-avec-nodejs.md#cache-avec-npm).

<h3 id="cache-yarn">Cache yarn.
<a href="#cache-yarn" class="ancre-titre after"></a></h3>

Pour la mise en cache avec `yarn`, utilisez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: node_js
node_js: '6' # or another
cache: yarn
```

Cela met en cache `$HOME/.cache/yarn`.*

<h3 id="cache-pip">Cache pip.
<a href="#cache-pip" class="ancre-titre after"></a></h3>

Pour la mise en cache des fichiers `pip`, utilisez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: python
cache: pip
```

Met en cache `$HOME/.cache/pip`.

<h3 id="cache-ccache">Cache ccache.
<a href="#cache-ccache" class="ancre-titre after"></a></h3>

Si vous utilisez `ccache`, utilisez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: c # or other C/C++ variants
cache: ccache
```

Pour mettre en cache `$HOME/.ccache ` et ajouter automatiquement `/usr/lib/ccache` à votre `$PATH`.

<h3 id="cache-paquetage-r">Cache de paquetage R.
<a href="#cache-paquetage-r" class="ancre-titre after"></a></h3>

Pour les packages R de mise en cache, utilisez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: R
cache: packages
```

Cela met en cache `$HOME/R/Library` et définit la variable d'environnement `R_LIB_USER=$HOME/R/Library`.

 <h3 id="cache-rust-cargo">Cache Rust Cargo.
<a href="#cache-rust-cargo" class="ancre-titre after"></a></h3>

Pour la mise en cache du paquetage Cargo, utilisez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: rust
cache: cargo
```

Ceci met en cache `$HOME/.cargo` et `$TRAVIS_BUILD_DIR/target`.

 <h3 id="repertoires-arbitraires">Répertoires arbitraires.
<a href="#repertoires-arbitraires" class="ancre-titre after"></a></h3>

Vous pouvez mettre en cache des répertoires arbitraires, tels que les répertoires Gradle, Maven, Composer et npm, entre les builds en les énumérant dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache:
    directories:
      - .autoconf
      - $HOME/.m2
```

Comme vous pouvez le voir, vous pouvez utiliser des variables d'environnement dans le cadre du chemin du répertoire. Après une éventuelle expension de variables, des chemins qui :

* Ne commencent pas par `/` sont relatifs à `$TRAVIS_BUILD_DIR` ;
* Commencent par `/` sont absolus.

Veuillez noter que l'utilisateur Travis doit avoir des autorisations d'écriture sur ce répertoire.

 <h2 id="elements-a-ne-pas-mettre-en-cache">Elements à ne pas mettre en cache.
<a href="#elements-a-ne-pas-mettre-en-cache" class="ancre-titre after"></a></h2>

Le cache a pour objectif de faciliter et d'accélérer l'installation des dépendances spécifiques à un langage. Tout ce qui concerne les outils tels que Bundler, pip, Composer, npm, Gradle, Maven, etc. doit donc être stocké dans le cache.

Les fichiers volumineux qui s'installent rapidement mais dont le téléchargement est lent ne tirent aucun avantage du cache, car leur téléchargement à partir du cache prend autant de temps qu'à partir de la source d'origine :

* SDK Android ; 
* Paquetages Debian ;
* Paquetages JDK ;
* Binaires compilés ; 
* Images docker.

Les images Docker ne sont pas mises en cache, car nous fournissons une toute nouvelle machine virtuelle pour chaque version.

 <h2 id="recuperation-et-stockage-des-caches">Récupération et stockage des caches.
<a href="#recuperation-et-stockage-des-caches" class="ancre-titre after"></a></h2>

* Travis CI récupère le cache pour chaque construction, y compris les branches et les _pull requests_ ;
* Il existe un cache par branche et par version de langage /version du compilateur /version JDK /emplacement Gemfile, etc. Pour plus de détails, consultez la section [caches et matrices de construction](#caches-et-matrices-de-construction") ;
* Si une branche n'a pas son propre cache, Travis CI récupère le cache de branche par défaut ;
* Seules les modifications apportées aux répertoires en cache à partir de poussées normales sont stockées.

 <h2 id="generer-pr-et-mise-en-cache">Générer des <i>pull requests</i> et mis en en cache.
<a href="#generer-pr-et-mise-en-cache" class="ancre-titre after"></a></h2>

Les builds de _pull request_ vérifient les emplacements de cache suivants dans l'ordre, en utilisant le premier disponible :

* Le cache de _pull request_ ;
* Le cache de branche cible de la _pull request_ ; 
* Le cache de branche par défaut du référentiel.

Si aucun des emplacements précédents ne contient un cache valide, la construction continue sans cache.

Une fois la première construction de _pull request_, il en crée un nouveau cache. 

Quelques points importants à noter concernant la mise en cache des _pull requests_ :

* Si un référentiel a les branches poussées par Build désactivées, ni la branche cible ni la branche principale ne peuvent être mises en cache ;
* Si le cache de la branche principale est ancien, par exemple dans un flux de travail où la plupart des tâches sont effectuées sur des branches, le cache sera d'autant moins utile ;
* Si une _pull request_ utilise un cache mais que vous ne le souhaitez pas, vous devez effacer à la fois son cache mais aussi le cache de la branche cible.

<h3 id="phase-before-cache">Phase <i>before-cache</i>.
<a href="#phase-before-cache" class="ancre-titre after"></a></h3>

Lorsque vous utilisez des caches, il peut être utile d'exécuter une commande juste avant de télécharger la nouvelle archive de cache.

Par exemple, l'utilitaire de gestion des dépendances peut écrire des fichiers journaux dans le répertoire que vous mettez en cache et vous ne voulez pas qu'ils affectent le cache. Utilisez la phase `before_cache` pour supprimer les fichiers journaux :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache:
    directories:
      - $HOME/.cache/pip
before_cache:
      - rm -f $HOME/.cache/pip/log/debug.log
```

Un échec à cette étape ne signifie pas que la tâche a échoué.

<h3 id="effacer-le-cache">Effacer le cache</i>.
<a href="#effacer-le-cache" class="ancre-titre after"></a></h3>

Parfois, vous pouvez corrompre votre cache en stockant des données erronées dans l'un des répertoires mis en cache, ou votre cache peut devenir invalide lorsque les environnements d'exécution linguistiques changent.

Utilisez l'une des façons suivantes d'accéder à votre cache et de le supprimer si nécessaire : 

* La page Paramètres de votre référentiel sur [https://travis-ci.com](https://travis-ci.com/). 

![Image de l'interface utilisateur du cache ](https://docs.travis-ci.com/images/caches-item.png)

* [Le client de la ligne de commande](https://github.com/travis-ci/travis#readme)

![Cache Travis - Selon](https://docs.travis-ci.com/images/cli-cache.png)

* [L'API](https://api.travis-ci.com/#/repos/:owner_name/:name/caches)

<h3 id="expiration-du-cache">Expiration du cache</i>.
<a href="#expiration-du-cache" class="ancre-titre after"></a></h3>

Les archives de cache sont actuellement configurées pour expirer après 45 jours pour les référentiels sur https://app.travis-ci.com. Cela signifie qu'une archive de cache spécifique sera supprimée si elle n'a pas été modifiée après son délai d'expiration.

<h2 id="reference-de-configuration-de-construction">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour la mise en [cache](https://config.travis-ci.com/ref/job/cache) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="configuration">Configuration.
<a href="#configuration" class="ancre-titre after"></a></h2>

<h3 id="activer-plusieurs-fonctionnalites-de-mise-en-cache">Activer plusieurs fonctionnalités de mise en cache.
<a href="#activer-plusieurs-fonctionnalites-de-mise-en-cache" class="ancre-titre after"></a></h3>

Lorsque vous souhaitez activer plusieurs fonctionnalités de mise en cache et que le langage les prend en charge, vous pouvez les répertorier en tant que tableau :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: objective-c
cache:
- bundler
- cocoapods
```

Cela ne fonctionne pas lors de la mise en cache des[répertoires](#repertoires-arbitraires) arbitraires, ou lorsqu' aucune des directives n'est soutenue par la langue.

Si vous souhaitez combiner cela avec d'autres modes de mise en cache, utilisez une carte de hachage. Voici un exemple de référentiel Ruby mettant en cache des modules Node.js :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
cache:
    bundler: true
    directories:
  - node_modules # NPM packages
```

Voici un autre exemple : un référentiel Rust mettant en cache des paquets Cargo et Ruby Gems :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: rust
cache:
    cargo: true
    directories:
      - vendor/bundle
install:
    - bundle install --deployment # to cache vendor/bundle
```

<h3 id="desactiver-explicitement-la-mise-en-cache">Désactiver explicitement la mise en cache.
<a href="#desactiver-explicitement-la-mise-en-cache" class="ancre-titre after"></a></h3>

Vous pouvez explicitement désactiver toute la mise en cache en définissant l'option `cache` sur `false` dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache: false
```

Il est également possible de désactiver un seul mode de mise en cache :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: objective-c
cache:
    bundler: false
    cocoapods: true
```

<h3 id="definition-du-delai-expiration">Définition du délai d'expiration.
<a href="#definition-du-delai-expiration" class="ancre-titre after"></a></h3>

La mise en cache a un délai d'expiration défini par défaut à 3 minutes. Ce délai d'expiration est là pour éviter tout problème pouvant entraîner un blocage de la construction. De tels problèmes peuvent être causés par une difficulté réseau entre les serveurs de travail et S3, ou même par un cache trop volumineux pour être compressé et téléchargé en temps voulu. Il existe toutefois des situations dans lesquelles vous pouvez souhaiter définir un délai d'expiration plus long, en particulier si vous devez mettre en cache une grande quantité de données. Pour modifier le délai d'expiration, vous pouvez utiliser la propriété `timeout` avec le délai souhaité en secondes :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache:
    timeout: 1000
```

<h2 id="caches-et-matrices-de-construction">Caches et matrices de construction.
<a href="#caches-et-matrices-de-construction" class="ancre-titre after"></a></h2>

Lorsque vous avez plusieurs tâches dans une matrice de construction, certaines caractéristiques de chaque tâche sont utilisées pour identifier le cache que chacune des tâches devrait utiliser.

Ces facteurs sont : 

1. Nom du système d'exploitation (actuellement, `linux` ou `windows`) ; 
* Distribution du système d'exploitation (pour Linux, `noble`, `jammy`, `focal`, `bionic`, `xenial`, `trusty` ou `precise`)  ;
* Noms et valeurs des variables d'environnement visibles définies dans `.travis.yml` ou dans le panneau Paramètres ; 
* Version d'exécution de la langue (pour la langue spécifiée dans la clé `language`) le cas échéant ;
* Pour les tâches de Bundler-aware, le nom du `Gemfile` utilisé.

Si ces caractéristiques sont communes à plusieurs tâches dans une matrice de construction, elles partageront la même URL sur le réseau. Cela pourrait corrompre le cache, ou le cache pourrait contenir des fichiers qui ne sont pas utilisables dans toutes les tâches qui l'utilisent. Dans ce cas, nous vous conseillons d'ajouter un nom de variable d'environnement publique à chaque tâche afin de créer une entrée de cache unique :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
CACHE_NAME=JOB1
```

Notez que lorsque vous envisagez des variables d'environnement, les valeurs doivent correspondre exactement, y compris les espaces. Par exemple avec :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
env:
  - FOO=1 BAR=2
  - FOO=1  BAR=2
  - BAR=2 FOO=1
```

Chacune des trois tâches utilisera son propre cache.

<h2 id="caches-et-autorisation-de-lecture">Caches et autorisations de lecture.
<a href="#caches-et-autorisation-de-lecture" class="ancre-titre after"></a></h2>

Lors de la mise en cache des [fichiers et des répertoires personnalisés](#rrepertoires-arbitraires), assurez-vous que les emplacements que vous spécifiez peuvent être lus et écrits par l'utilisateur.

S'ils ne le sont pas, les utilitaires de mise en cache signalent les erreurs lorsqu'il invoque `tar` pour créer l'archive de cache.

Par exemple :

```console
FAILED: tar -Pzcf /Users/travis/.casher/push.tgz /path/to/unreadable/directory
tar: /path/to/unreadable/directory: Cannot stat: No such file or directory
```

<h2 id="comment-fonctionne-la-mise-en-cache">Comment fonctionne la mise en cache.
<a href="#comment-fonctionne-la-mise-en-cache" class="ancre-titre after"></a></h2>

Travis CI enregistre une archive de tous les répertoires répertoriés dans la configuration et la télécharge sur un fournisseur de stockage, en utilisant une URL sécurisée et protégée, assurant la sécurité et la confidentialité des archives téléchargées.

Notez que cela rend notre cache non-local, il est toujours lié à la bande passante du réseau et aux résolutions DNS. Cela a un impact sur ce que vous pouvez et devriez stocker dans le cache. Si vous stockez des archives plus grandes que quelques centaines de mégaoctets dans le cache, il est peu probable que vous verrez une amélioration à grande vitesse.

Avant la construction, nous vérifions si une archive mise en cache existe. Si c'est le cas, nous la téléchargeons et la décompressons aux emplacements spécifiés.

Après la construction, nous vérifions les modifications apportées au répertoire, créons une nouvelle archive contenant ces modifications et la téléchargeons vers le stockage distant.

Le téléchargement fait actuellement partie du cycle de construction, mais nous cherchons à améliorer ce processus afin qu'il se déroule en dehors, ce qui permettra d'obtenir un retour plus rapide sur la build.
<hr>
