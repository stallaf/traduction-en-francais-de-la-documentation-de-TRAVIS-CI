# Matrice de build

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#extension-de-matrice">Extension de la matrice</a></li>
            <li><a href="#lister-les-taches-individuelles">Répertorier les tâches individuelles</a></li>
            <li><a href="#exclure-les-taches">Exclure les tâches</a></li>
            <li><a href="#inclure-explicitement-des-taches">Inclure explicitement des tâches</a></li>
            <li><a href="#autoriser-les-echecs-de-lignes">Autoriser l'échec de lignes</a></li>
            <li><a href="#utiliser-la-fin-rapide">Utiliser la fin rapide</a></li>
            <li><a href="#utiliser-differents-langages">Utiliser différents langages de programmation par tâche</a></li>
            <li><a href="#noms-des-taches">Noms des tâches</a></li>
            <li><a href="#taches-uniques-et-en-double">Caractère unique des tâches et tâches en double</a></li>
        </ul>
        </ul>
    </div>
</div>
</p>

Une matrice de construction est faite de plusieurs tâches multiples qui s'exécutent en parallèle.

Cela peut être utile dans de nombreux cas, mais les deux principales raisons d'utiliser une matrice de construction sont : 

* [Réduire le temps d'exécution global de la construction](./acceleration-de-la-construction.md) ;
* Exécuter des tests sur différentes versions de moteurs d'exécution ou de dépendances.

Les exemples de cette page se concentrent sur ce dernier cas d'utilisation.

Il existe deux façons de définir une matrice dans le fichier `.travis.yml` : 

* Utilisation de la fonction d'extension matricielle ;
* Listing des configurations de tâches individuelles.

Les deux fonctionnalités peuvent être combinées.

<h2 id="extension-de-matrice">Extension de la matrice.
<a href="#extension-de-matrice" class="ancre-titre after"></a></h2>

Certaines clés sont définies comme clés d'extension de matrice et prennent des tableaux de valeurs, créant une tâche supplémentaire par valeur. Si plusieurs clés d'extension de matrice sont données, cela multiplie le nombre de tâches créées.

Par exemple, la configuration suivante produit une matrice de construction qui se développe à 8 tâches individuelles (2 * 2 * 2), combinant chaque valeur à partir des trois clés d'extension de matrice `rvm`, `gemfile`, et `env`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
rvm:
- 2.5
- 2.2
gemfile:
- gemfiles/Gemfile.rails-3.2.x
- gemfiles/Gemfile.rails-3.0.x
env:
- ISOLATED=true
- ISOLATED=false
```

<h2 id="lister-les-taches-individuelles">Répertorier les tâches individuelles.
<a href="#lister-les-taches-individuelles" class="ancre-titre after"></a></h2>

De plus, les tâches peuvent être spécifiées en ajoutant des entrées à la clé `jobs.include`.

Par exemple, si toutes ces combinaisons de l'expansion de la matrice ci-dessus sont pertinentes, les tâches peuvent être spécifiées individuellement comme :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    include:
      - rvm: 2.5
        gemfile: gemfiles/Gemfile.rails-3.2.x
        env: ISOLATED=false
    - rvm: 2.2
        gemfile: gemfiles/Gemfile.rails-3.0.x
        env: ISOLATED=true
```

<div class="notice cadre" >
Les matrices de construction sont actuellement limitées à un maximum de <strong>200 tâches</strong> pour les référentiels privés et publics. Si vous êtes sur un plan open-source, n'oubliez pas que Travis CI fournit ce service gratuitement à la communauté. Veuillez donc spécifier uniquement la matrice dont vous avez réellement besoin. 
</div>

<div class="notice cadre" >
Vous pouvez également consulter la section <a href="https://config.travis-ci.com/ref/language">Langage</a> dans notre [référence de configuration de build Travis CI](https://config.travis-ci.com/).
</div>

<h2 id="exclure-les-taches">Exclure les tâches.
<a href="#exclure-les-taches" class="ancre-titre after"></a></h2>

L'expansion de la matrice de construction a parfois produit des combinaisons indésirables. Dans ce cas, il peut être pratique d'exclure certaines combinaisons à l'aide de la clé `jobs.exclude`, au lieu de lister tous les travaux individuellement.

Par exemple, cela exclurait deux travaux de la matrice de construction :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    exclude:
      - rvm: 1.9.3
        gemfile: gemfiles/Gemfile.rails-2.3.x
        env: ISOLATED=true
      - rvm: jruby
        gemfile: gemfiles/Gemfile.rails-2.3.x
        env: ISOLATED=true
```

Si les tâches que vous souhaitez exclure de la matrice de build partagent les mêmes paramètres de matrice, vous pouvez spécifier uniquement ceux-ci et omettre les parties variables.

Supposons que vous ayez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
rvm:
- 1.9.3
- 2.0.0
- 2.1.0
env:
- DB=mongodb
- DB=redis
- DB=mysql
gemfile:
- Gemfile
- gemfiles/rails4.gemfile
- gemfiles/rails31.gemfile
- gemfiles/rails32.gemfile
```

Il en résulte une matrice de construction 3 × 3 × 4. Pour exclure tous les travaux qui ont `rvm` value `2.0.0` et `gemfile` value `Gemfile`, vous pouvez écrire :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    exclude:
      - rvm: 2.0.0
        gemfile: Gemfile
```

Ce qui équivaut à:

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    exclude:
    - rvm: 2.0.0
        gemfile: Gemfile
        env: DB=mongodb
    - rvm: 2.0.0
        gemfile: Gemfile
        env: DB=redis
    - rvm: 2.0.0
        gemfile: Gemfile
        env: DB=mysql
```

<h2 id="exclure-taches-avec-valeur-env">Exclure des tâches avec la valeur env.
<a href="#exclure-taches-avec-valeur-env" class="ancre-titre after"></a></h2>

Lorsque vous excluez les tâches avec les valeurs `env`, la valeur doit correspondre _exactement_.

Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
rvm:
- 1.9.3
- 2.0.0
- 2.1.0
env:
- DB=mongodb SUITE=all
- DB=mongodb SUITE=compact
- DB=redis
- DB=mysql
jobs:
    exclude:
      - rvm: 1.9.3
            env: DB=mongodb
```

définit une matrice 3 × 4, car la valeur `env` ne correspond à aucune tâche définie dans la matrice.

Pour exclure toutes les tâches Ruby 1.9.3 avec `DB=mongodb` défini, écrivez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
rvm:
- 1.9.3
- 2.0.0
- 2.1.0
env:
- DB=mongodb SUITE=all
- DB=mongodb SUITE=compact
- DB=redis
- DB=mysql
jobs:
    exclude:
      - rvm: 1.9.3
        env: DB=mongodb SUITE=all # not 'env: DB=mongodb' or 'env: SUITE=all DB=mongodb'
      - rvm: 1.9.3
        env: DB=mongodb SUITE=compact # not 'env: SUITE=compact DB=mongodb'
```

<h2 id="inclure-explicitement-des-taches">Inclure explicitement des tâches.
<a href="#inclure-explicitement-des-taches" class="ancre-titre after"></a></h2>

Il est également possible d'inclure des entrées dans la matrice avec `jobs.include` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    include:
      - rvm: ruby-head
        gemfile: gemfiles/Gemfile.rails-3.2.x
        env: ISOLATED=false
```

Cela ajoute une tâche particulière à la matrice de construction qui a déjà été remplie.

Ceci est utile si vous souhaitez tester uniquement la dernière version d'une dépendance avec la dernière version de l'exécution.

Vous pouvez utiliser cette méthode pour créer une matrice de construction ne contenant que des combinaisons spécifiques. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: python
jobs:
    include:
    - python: "2.7"
        env: TEST_SUITE=suite_2_7
    - python: "3.8"
        env: TEST_SUITE=suite_3_8
    - python: "pypy"
        env: TEST_SUITE=suite_pypy
script: ./test.py $TEST_SUITE
```

Crée une matrice de construction avec 3 tâches, qui exécutent la suite de tests pour chaque version de Python.

<h3 id="heritage-du-tableau">Les tâches incluses explicitement héritent la première valeur du tableau.
<a href="#heritage-du-tableau" class="ancre-titre after"></a></h3>

Les tâches explicitement incluses héritent de la première valeur des clés d'expansion définies.

Dans cet exemple avec une matrice de compilation Python à 3 tâches, chaque tâche dans `jobs.include` a la valeur `python` définie sur `'3.8'`. Vous pouvez définir explicitement la version Python pour une entrée spécifique :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: python
python:
  - '3.8'
  - '3.7'
  - '2.7'
jobs:
    include:
    - python: '3.8' # this is not strictly necessary
        env: EXTRA_TESTS=true
    - python: '3.7'
        env: EXTRA_TESTS=true
script: env $EXTRA_TESTS ./test.py $TEST_SUITE
```

<h3 id="taches-incluses-avec-un-seul-element">Tâches explicitement incluses avec un seul élément dans la matrice de construction.
<a href="#taches-incluses-avec-un-seul-element" class="ancre-titre after"></a></h3>

En tant que cas particulier, si votre matrice de construction n'a qu'un seul élément _et_ que vous avez explicitement inclus des tâches, l'extension de la matrice n'est pas terminée et les tâches explicites définissent _complètement_ votre version. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: python
python:
  - '3.8'
jobs:
    include:
    - env: EXTRA_TESTS=true
# only defines one job with `python: 3.8` and `env: EXTRA_TESTS=true`
```

Si vous avez besoin d'une tâche (unique) provenant de la matrice dans ce cas, ajoutez une entrée de tâche vide avec des accolades à l'instruction de construction (car cette instruction hériterait de toutes les valeurs de la matrice) :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: python
python:
  - '3.8'
jobs:
    include:
      - {}
      - env: EXTRA_TESTS=true
# defines two jobs:
#       - python: 3.8
#       - python: 3.8
#           env: EXTRA_TESTS=true
```

<h2 id="autoriser-les-echecs-de-lignes">Autoriser l'échec de lignes.
<a href="#autoriser-les-echecs-de-lignes" class="ancre-titre after"></a></h2>

Vous pouvez définir les lignes qui sont autorisées à échouer dans la matrice de build. Les échecs autorisés sont des éléments de votre matrice de build qui sont autorisés à échouer sans entraîner l'échec de l'ensemble de la construction. Cela vous permet d'ajouter des constructions expérimentales et préparatoires afin de tester des versions ou des configurations que vous n'êtes pas encore prêt à prendre officiellement en charge.

Définir les échecs autorisés dans la matrice de construction comme paires de clé/valeur :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
  allow_failures:
    - rvm: 1.9.3
```

<h3 id="correspondance-avec-allow-failures">Faire correspondre les tâches avec <i>allow_failures</i>.
<a href="#correspondance-avec-allow-failures" class="ancre-titre after"></a></h3>

Lorsque les tâches sont mises en correspondance avec les définitions fournies dans `allow_failures`, _toutes_ les conditions de `allow_failures` doivent être remplies à la lettre, et toutes les clés de l'élément `allow_failures` doivent exister au niveau supérieur de la matrice de build (c'est-à-dire pas dans `jobs.include`).

**Exemples :**

Considérer

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby

rvm:
- 2.0.0
- 2.1.6

env:
    global:
    - SECRET_VAR1=SECRET1
    jobs:
    - SECRET_VAR2=SECRET2

jobs:
    allow_failures:
      - env: SECRET_VAR1=SECRET1 SECRET_VAR2=SECRET2
```

Ici, aucune tâche n'est autorisée à échouer car aucune tâche n'a la valeur `env` `SECRET_VAR1=SECRET1 SECRET_VAR2=SECRET2.`.

suivant,

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: php
php:
- 5.6
- 7.0
jobs:
    include:
    - php: 7.0
        env: KEY=VALUE
    allow_failures:
    - php: 7.0
        env: KEY=VALUE
```

Sans `env` de haut niveau, aucune tâche ne sera autorisée à échouer.

<h2 id="utiliser-la-fin-rapide">Utiliser la fin rapide.
<a href="#utiliser-la-fin-rapide" class="ancre-titre after"></a></h2>

Si certaines lignes dans la matrice de construction sont autorisées à échouer, la construction ne sera pas marquée comme terminée avant la fin.

Pour marquer la construction telle que terminée dès que possible, ajoutez `fast_finish: true` à la section `jobs` de votre `.travis.yml` comme ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    fast_finish: true
```

Désormais, le résultat de la build sera déterminé dès que toutes les tâches requises seront terminées, sur la base de ces résultats, tandis que les autres tâches `allow_failures` continueront à s'exécuter.

<h2 id="utiliser-differents-langages">Utiliser différents langages de programmation par tâche.
<a href="#utiliser-differents-langages" class="ancre-titre after"></a></h2>

Vous pouvez également utiliser la fonctionnalité `jobs.include`. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
dist: xenial
language: php
php:
  - '5.6'
jobs:
    include:
      - language: python
        python: 3.8
        script:
        - python -c "print('Hi from Python!')"
      - language: node_js
        node_js: 12
        script:
        - node -e "console.log('Hi from NodeJS!')"
      - language: java
        jdk: openjdk8
        script:
        - javac -help
```

Cela crée une construction avec 3 tâches comme suit : 

* Une tâche Python 3.8 ;
* Une tâche node.js 12 ;
* Une tâche Java OpenJDK 8.

<h2 id="noms-des-taches">Noms des tâches.
<a href="#noms-des-taches" class="ancre-titre after"></a></h2>

Les tâches répertoriées dans `jobs.include` peuvent être nommées à l'aide du `name` de la clé, comme suit :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    include:
    - name: Job 1
        script: echo "Running job 1"
```

Ce nom apparaîtra sur l'interface utilisateur de la matrice de construction et peut être pratique afin d'identifier rapidement les tâches dans une grande matrice.

Les tâches générées par la fonction d'extension de la matrice ne peuvent pas être nommées.

<h2 id="taches-uniques-et-en-double">Caractère unique des tâches et tâches en double
<a href="#taches-uniques-et-en-double" class="ancre-titre after"></a></h2>

Les tâches doivent être uniques et celles en double sont supprimées pendant le processus de validation de la configuration de build.

Par exemple, cette configuration entraînerait une seule tâcher en utilisant les ancres et les alias YAML :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
_shared_job: &shared_job
    script: echo "shared script config"
jobs:
    include:
    - <<: *shared_job
    - <<: *shared_job
```

Dans de rares circonstances, il peut toujours être souhaitable d'exécuter plusieures tâches avec la même configuration. Dans de tels cas, l'unicité de la tâche peut être réalisée en spécifiant toute clé supplémentaire, par exemple, un nom de tâche :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
_shared_job: &shared_job
    script: echo "shared script config"
jobs:
    include:
      - name: Job 1
        <<: *shared_job
      - name: Job 2
        <<: *shared_job
```
<hr class="invisible">
