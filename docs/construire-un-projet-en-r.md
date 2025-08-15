# Construire un projet en R

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#communaute">Avertissement soutenu par la communauté</a></li>
            <li><a href="#configuration-de-base">Configurations de base</a></li>
            <li><a href="#options-de-configuration">Options de configuration</a></li>
            <li><a href="#reference-de-configuration-de-construction-r">Référence de configuration de construction</a></li>
            <li><a href="#exemples-r">Exemples</a></li>
            <li><a href="#convertir">Convertir depuis r-travis</a></li>
            <li><a href="#remerciements">Remerciements</a></li>
        </ul>
    </div>
</div>
</p>

Ce guide traite de la configuration des projets R sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.

<h2 id="communaute">Avertissement soutenu par la communauté.
<a href="#communaute" class="ancre-titre after"></a></h2>

Le soutien de Travis CI à R est apporté par la communauté et peut être supprimé ou modifié à tout moment. Si vous rencontrez des problèmes, veuillez les signaler dans la [section R de nos forums](https://travis-ci.community/c/languages/r) avec CC à [@jeroen](https://github.com/jeroen) et [@jimhester](https://github.com/jimhester).

<h2 id="configuration-de-base">Configurations de base.
<a href="#configuration-de-base" class="ancre-titre after"></a></h2>

La prise en charge de R dans Travis CI est conçue pour faciliter le test des paquetages R. Si le vôtre n'a pas besoin de dépendances système au-delà de celles spécifiées dans votre fichier de description, votre `.travis.yml` peut simplement ressembler à :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: r
```

L'environnement R est livré avec [LaTex](https://www.tug.org/texlive/) et [pandoc](http://johnmacfarlane.net/pandoc/) préinstallé, ce qui facilite l'utilisation de paquetages comme [RMarkdown](http://rmarkdown.rstudio.com/) ou [knitr](https://yihui.name/knitr/).

<h2 id="options-de-configuration">Options de configuration.
<a href="#options-de-configuration" class="ancre-titre after"></a></h2>

Travis CI prend en charge un certain nombre d'options de configuration pour votre paquetage R.

<h3 id="versions-r">Versions R .
<a href="#versions-r" class="ancre-titre after"></a></h3>

Travis CI prend en charge les versions de R `3.0.3` et plus sur Ubuntu Precise et Ubuntu Trusty. Les alias existent pour chaque version majeure, par exemple, `3,1` points à `3,1,3`. En outre, le nom `oldrel` est aliasé à la version majeure précédente et `release` est aliasée à la dernière version mineure. `devel` est construit à partir du [miroir git R](https://travis-ci.org/wch/r-source) du tronc SVN R (mis à jour toutes les heures).

Les versions matricielles sont prises en charge pour les versions R, cependant, les deux instances de R doivent être en minuscules.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: r
r:
  - oldrel
  - release
  - devel
```

À mesure que de nouvelles versions mineures sont publiées, les alias flotteront et pointent vers la version mineure la plus récente.

Vous pouvez accéder à la _chaîne_ «canal» ci-dessus (c'est-à-dire `release`) par opposition au numéro de version concrète (c'est-à-dire `3.2.1`) avec la variable d'environnement `TRAVIS_R_VERSION_STRING.`.

La version exacte de R utilisée pour chaque compilation est indiquée dans la section « Informations sur la session R » du journal de compilation.

<h3 id="dependances">Dépendances.
<a href="#dependances" class="ancre-titre after"></a></h3>

Par défaut, Travis CI trouvera tous les paquetages R répertoriés sous forme de dépendances dans le fichier `DESCRIPTION` de votre paquetage et les installera à partir de CRAN. Vous pouvez inclure des dépendances sur les paquetages en développement en les répertoriant dans les champs `Remotes:` de votre description. Consultez les [Remotes Vignette](https://github.com/hadley/devtools/blob/master/vignettes/dependencies.Rmd#package-remotes) pour plus d'informations sur l'utilisation des télécommandes de développement dans votre paquetage.

La plupart du temps, vous ne devriez pas avoir besoin de spécifier des dépendances supplémentaires dans votre `.travis.yml`.

<h3 id="latex-et-texlive">Paquetages de LaTex et TexLive.
<a href="#latex-et-texlive" class="ancre-titre after"></a></h3>

La distribution Texlive incluse ne contient qu'un ensemble limité de paquetages par défaut. Si vos vignettes nécessitent des paquetages TexLive supplémentaires, vous pouvez les installer à l'aide de `tlmgr install` dans l'étape `before_install`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: r
before_install:
  - tlmgr install index
```

La meilleure façon de déterminer les paquetages dont vous pourriez avoir besoin est de consulter ceux répertoriés dans le message d'erreur de LaTex et de les rechercher sur [CTAN](https://www.ctan.org/). Les paquetages ont souvent un champ `Contained in:` qui indique le groupe de paquetages que vous devez installer.

Si vous n'avez pas besoin de LaTex, dites à Travis CI de ne pas l'installer à l'aide de `latex: false`.

<h3 id="pandoc">Pandoc.
<a href="#pandoc" class="ancre-titre after"></a></h3>

La version pandoc par défaut installée est `2.2`. Les [versions alternatives de pandoc](https://github.com/jgm/pandoc/releases) peuvent être installées en définissant `pandoc_version ` sur la version souhaitée.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: r
pandoc_version: 1.19.2.1
```

Si vous n'avez pas besoin de Pandoc, dites à Travis CI de ne pas l'installer en utilisant `pandoc: false`.

<h3 id="apt">Paquetages APT.
<a href="#apt" class="ancre-titre after"></a></h3>

Utilisez [l'addon APT](https://docs.travis-ci.com/user/installing-dependencies/#installing-packages-with-the-apt-addon) pour installer les paquetages APT. L'extrait ci-dessous installe une condition préalable pour le paquetage R`xml2` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
  apt:
    packages:
      - libxml2-dev
```

Cette option est ignorée sur les versions non Linux.

Une alternative est la clé `apt_packages` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
apt_packages:
  - libxml2-dev
```

<h3 id="verification">Options de vérification des packages.
<a href="#verification" class="ancre-titre after"></a></h3>

Vous pouvez utiliser les options de niveau supérieur suivantes pour contrôler les options utilisées lors de la construction et la vérification de votre paquetage : 

* `warnings_are_errors` : cette option force `WARNINGS` de `R CMD check` à devenir des échecs de compilation (valeur par défaut : `true`). Cela est particulièrement utile lorsque vous préparez votre paquet pour le soumettre au CRAN, et est recommandé pour la plupart des paquets. Définissez `warnings_are_errors : false` si vous ne souhaitez pas que `WARNINGS` entraînent l'échec de la compilation.
* `r_build_args` : arguments supplémentaires à passer à `R CMD build`, sous la forme d'une chaîne unique. La valeur par défaut est vide.
* `r_check_args` : arguments supplémentaires à passer à `R CMD check`, sous la forme d'une chaîne unique. La valeur par défaut est `--as-cran`.

<h3 id="bioconducteur">Bioconducteur.
<a href="#bioconducteur" class="ancre-titre after"></a></h3>

Un ensemble de bioconducteur typique ne doit avoir besoin que de spécifier la version bioconductrice avec laquelle ils souhaitent tester dans leur `.travis.yml`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: r
r: bioc-devel
```

Ou si vous souhaitez tester avec la branche _release_

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: r
r: bioc-release
```

Travis CI utilisera la version R appropriée pour cette version du bioconducteur et le configurera de manière correctement pour l'installation de dépendances.

<h3 id="packrat">Packrat.
<a href="#packrat" class="ancre-titre after"></a></h3>

Si vous souhaitez que Travis CI utilise la bibliothèque de paquets Packrat spécifique à votre projet, plutôt que le comportement par défaut qui consiste à télécharger les dépendances de vos paquets depuis CRAN, vous pouvez ajouter ceci à votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
  - R -e "0" --args --bootstrap-packrat
  - R -e "packrat::restore(restart = FALSE)"
```

Vous pouvez minimiser les temps de construction en mettant en cache vos paquetages Packrat avec :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache:
  directories:
    - $TRAVIS_BUILD_DIR/packrat/src
    - $TRAVIS_BUILD_DIR/packrat/lib
  packages: true
```

<h3 id="divers">Divers.
<a href="#divers" class="ancre-titre after"></a></h3>

* `cran`: Miroir CRAN à utiliser pour récupérer les paquets. La valeur par défaut est https://cloud.r-project.org.
* `repos` : Dictionnaire des référentiels à transmettre à `options(repos)`. Si `CRAN` n'est pas spécifié dans le dictionnaire, la valeur de l'option `cran` est utilisée. Exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
repos:
  CRAN: https://cloud.r-project.org
  ropensci: http://packages.ropensci.org
```

<h3 id="variables-e">Variables d'environnement.
<a href="#variables-e" class="ancre-titre after"></a></h3>

R-Travis définit les variables d'environnement supplémentaires suivantes à partir des valeurs par défaut de Travis.

* `TRAVIS_R_VERSION=3.2.4` Définir la version choisie par `r:` ;
* `R_LIBS_USER=~/R/Library` ;
* `R_LIBS_SITE=/usr/local/lib/R/site-library:/usr/lib/R/site-library` ;
* `_R_CHECK_CRAN_INCOMING_=false` ;
* `NOT_CRAN=true` ;
* `R_PROFILE=~/.Rprofile.site` ;
* `TRAVIS_R_VERSION_STRING` défini sur la chaîne fournie à `r:`, c'est-à-dire `release`, `oldrel` ou `devel`.   Utile, par exemple, pour déployer uniquement à partir de `release` via `on: condition: "$TRAVIS_R_VERSION_STRING = release"`.

<h3 id="sup">Champs de dépendances supplémentaires.
<a href="#sup" class="ancre-titre after"></a></h3>

Pour la plupart des paquetages, vous ne devriez pas avoir besoin de spécifier des dépendances supplémentaires dans votre `.travis.yml`. Cependant, dans de rares cas, les champs suivants sont pris en charge.

Chacun des noms ci-dessous est une liste de paquetages que vous pouvez éventuellement spécifier comme une entrée de niveau supérieur dans votre `.travis.yml`; Les inscriptions de ces listes seront installées avant de construire et de tester votre paquetage. Notez que ces listes sont traitées dans l'ordre, de sorte que les entrées peuvent dépendre des dépendances dans une liste précédente. 

* `apt_packages` : Voir ci-dessus ;
* `r_binary_packages` : Liste des paquets R à installer en tant que paquets binaires sur les versions Linux, via le [PPA cran2deb4ubuntu](https://launchpad.net/~marutter/+archive/ubuntu/c2d4u) de Michael Rutter. Ces installations seront plus rapides que les installations à partir du code source, mais ne correspondront pas toujours à la version la plus récente. Spécifiez le nom comme vous le feriez lors d'une installation à partir du CRAN :
* `r_packages` : liste des paquets R à installer via `install.packages` ;
* `bioc_packages` : liste des paquets [Bioconductor](https://www.bioconductor.org/) à installer ;
* `r_github_packages` : liste des paquets à installer directement depuis GitHub, à l'aide de `devtools::install_github` du [paquet devtools](https://github.com/hadley/devtools). Les noms des paquets doivent être sous la forme `user/repo`. Si le paquet est installé dans un sous-répertoire, utilisez `user/repo/subdirectory`. Une autre solution consiste à ajouter `user/repo` ou `user/repo/folder` à la section `Remotes` du fichier `DESCRIPTION` de votre paquet.

<h3 id="personnalisation">Personnalisez les étapes de construction Travis.
<a href="#personnalisation" class="ancre-titre after"></a></h3>

Pour certains cas d'utilisation avancés, il est logique de remplacer les étapes par défaut utilisées pour la création de paquetages R. Les règles par défaut ressemblent à peu près à :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
- R -e 'remotes::install_deps(dep = T)'
script:
- R CMD build .
- R CMD check *tar.gz --as-cran
```

Si vous souhaitez voir tous les détails, [consultez le code source](https://github.com/travis-ci/travis-build/blob/master/lib/travis/build/script/r.rb).

<h2 id="reference-de-configuration-de-construction-r">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-r" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build [pour R](https://config.travis-ci.com/ref/language/r) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="exemples-r">Exemples.
<a href="#exemples-r" class="ancre-titre after"></a></h2>

<h3 id="sous-repertoire">Package dans un sous-répertoire.
<a href="#sous-repertoire" class="ancre-titre after"></a></h3>

Si votre paquetage est dans un sous-répertoire du référentiel, vous devez passer au sous-répertoire avant d'exécuter les étapes d'installation ou de script.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: r
before_install:
  - cd subdirectory
```

<h3 id="paquetage-distant">Paquetage distant.
<a href="#paquetage-distant" class="ancre-titre after"></a></h3>

Si votre paquetage dépend d'un autre référentiel, vous pouvez utiliser `r_github_packages` de cette manière :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
r_github_packages: user/repo
```

Une alternative consiste à ajouter la ligne suivante à votre fichier de description :

<div class="titre-code">
    <div class="item gauche">&nbsp;DESCIPTION</div><div class="item droit">YAML</div>
</div>
```yml
Imports: pkg-name-of-repo
Remotes: user/repo
```

N'oubliez pas que `Remotes:` spécifie la `source` d'un paquetage de développement, donc celui-ci doit toujours être répertorié dans `Imports:, Suggests: Depends:` ou `LinkingTo:`. Dans les rares cas où le nom du _dépôt_ et celui du _paquetage_ diffèrent, `Remotes:` attend le nom du _dépôt_ et Imports: attend le nom du _paquetage_ (conformément à `DESCRIPTION` du paquet importé).

<h3 id="paquetage-distant-sous-repertoire">Package distant dans un sous-répertoire.
<a href="#paquetage-distant-sous-repertoire" class="ancre-titre after"></a></h3>

Si votre paquetage dépend d'un autre référentiel qui contient le paquetage dans un sous-répertoire, vous pouvez utiliser `r_github_packages` de cette manière :

<div class="titre-code">
    <div class="item gauche">&nbsp;DESCIPTION</div><div class="item droit">YAML</div>
</div>
```yml
r_github_packages: user/repo/folder
```

Une alternative consiste à ajouter la ligne suivante à votre fichier de description :

<div class="titre-code">
    <div class="item gauche">&nbsp;DESCIPTION</div><div class="item droit">YAML</div>
</div>
```yml
Remotes: user/repo/folder
```

<h2 id="convertir">Convertir depuis r-travis.
<a href="#convertir" class="ancre-titre after"></a></h2>

Si vous avez déjà utilisé [r-travis](https://github.com/craigcitro/r-travis) pour tester votre paquetage R, vous êtes encouragé à passer à l'utilisation du support natif décrit ici. Nous avons écrit un [guide de portage](https://github.com/craigcitro/r-travis/wiki/Porting-to-native-R-support-in-Travis) pour vous aider à modifier votre `.travis.yml`.

<h2 id="remerciements">Remerciements.
<a href="#remerciements" class="ancre-titre after"></a></h2>

La prise en charge de Travis CI par R était initialement basée sur le projet [r-travis](https://github.com/craigcitro/r-travis), et nous tenons à remercier tous les [contributeurs](https://github.com/craigcitro/r-travis/graphs/contributors). Pour plus d'informations sur le passage de r-travis à la prise en charge native, consultez le [guide de portage](https://github.com/craigcitro/r-travis/wiki/Porting-to-native-R-support-in-Travis).
<hr class="invisible">
