# Utiliser les espaces de travail

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#espaces-de-travail">Espaces de travail</a></li>
            <li><a href="#configuration">Configuration</a></li>
            <li><a href="#exemple-espaces-de-travail">Exemples d'espaces de travail</a></li>
            <li><a href="#espaces-de-travail-et-concurrence">Espaces de travail et concurrence</a></li>
            <li><a href="#noms-predefinis-espaces-de-travail">Noms prédéfinis d'espaces de travail</a></li>
            <li><a href="#differences-entre-espaces-de-travail-et-caches">Différences entre les espaces de travail et les caches</a></li>
        </ul>
        </ul>
    </div>
</div>
</p>

<h2 id="espaces-de-travail">Espaces de travail.
<a href="#espaces-de-travail" class="ancre-titre after"></a></h2>

Les espaces de travail permettent des tâches _au sein_ d'une version pour partager des fichiers. Ils sont utiles lorsque vous souhaitez utiliser des artefacts de construction à partir d'une tâche précédente ; par exemple, vous créez un cache qui peut être utilisé dans plusieurs tâches plus tard.

Vous pouvez donc voir clairement quand un espace de travail est créé et utilisé. Nous vous recommandons d'utiliser des espaces de travail avec des étapes de construction, comme indiqué dans les exemples suivants. 

Notez qu'il est préférable de créer un espace de travail à une certaine étape, puis de l'utiliser dans les étapes suivantes.

<h2 id="configuration">Configuration.
<a href="#configuration" class="ancre-titre after"></a></h2>

Une configuration minimale de l'espace de travail ressemble à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    include:
        - stage: warm_cache
            script:
              - echo "foo" > foo.txt
            workspaces:
                create:
                    name: ws1
                    paths:
                      - foo.txt
        - stage: use_cache
            workspaces:
                use: ws1
            script:
              - cat foo.txt || true
```

Chaque espace de travail a un nom unique dans une construction. Il est spécifié par la clé `name` lorsqu'il est créé avec `workspaces.create`, et par la valeur lorsqu'il est utilisé avec `workspaces.use`.

Dans l'exemple ci-dessus, l'espace de travail nommé `ws1` est créé par le seul travail dans l'étape `warm_cache`. L'espace de travail est ensuite consommé dans l'étape `use_cache`.

<h2 id="exemple-espaces-de-travail">Exemples d'espace de travail.
<a href="#exemple-espaces-de-travail" class="ancre-titre after"></a></h2>

La section suivante montre différents exemples d'espace de travail.

<h3 id="exemple-multiples-espaces-de-travail">Exemple de multiples espaces de travail.
<a href="#exemple-multiples-espaces-de-travail" class="ancre-titre after"></a></h3>

Vous pouvez utiliser plusieurs espaces de travail dans une construction.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script:
  - ./build $TRAVIS_OS_NAME
jobs:
  include:
    - stage: Build binaries
      os: linux
      workspaces:
        create:
          name: linux-binaries
    - os: osx
      workspaces:
        create:
          name: osx-binaries
    - stage: Deploy
      language: minimal
      workspaces:
        use:
          - linux-binaries
          - osx-binaries
      git:
        clone: false
      script:
        - ./create_archive.sh
      deploy:
        provider: script
        script: ./upload.sh
```

Dans cet exemple : 

1. Nous exécutons deux tâches au stade «Build binaries», chacune produisant des binaires de téléchargement. 
2. Dans l'étape «Deploy» ultérieure, nous récupérons les espaces de travail produits par les tâches dans l'étape précédente et les déployons à l'aide d'un script personnalisé.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
  include:
    - stage: Build and Test
      name: "Job A"
      script:
        - echo "Building Job A"
        - echo "Running tests for Job A"
        - mkdir -p workspace-a
        - echo "This is data from Job A" > workspace-a/data-A.txt
      workspaces:
        create:
          name: workspace-a
          paths:
            - workspace-a
    - name: "Job B"
      script:
        - echo "Building Job B"
        - echo "Running tests for Job B"
        - mkdir -p workspace-b
        - echo "This is data from Job B" > workspace-b/data-B.txt
      workspaces:
        create:
          name: workspace-b
          paths:
            - workspace-b
    - name: "Job C"
      script:
        - echo "Building Job C"
        - echo "Running tests for Job C"
        - mkdir -p workspace-c
        - echo "This is data from Job C" > workspace-c/data-C.txt
      workspaces:
        create:
          name: workspace-c
          paths:
            - workspace-c
    - stage: Deploy
      name: "Deploy"
      script:
        - echo "Deploying using artifacts from Job A Job B and Job C"
        - cat workspace-a/data-A.txt
        - cat workspace-b/data-B.txt
        - cat workspace-c/data-C.txt
      workspaces:
        use:
          - workspace-a
          - workspace-b
          - workspace-c
```

Dans l'exemple ci-dessus : 

1. L'étape de construction  «Build and Test» se compose de 3 tâches, chacune produisant une sortie dans un espace de travail séparé ; 
2. La création de «Deploy» est constituée d'une tâche, laquelle : 
     - Marque l'utilisation des 3 espaces de travail précédemment créés ;
     - extrait les données de chaque espace de travail précédemment créé et les renvoie vers la sortie standard afin de démontrer la capacité à accéder à l'espace de travail.

<h2 id="espaces-de-travail-et-concurrence">Espaces de travail et concurrence.
<a href="#espaces-de-travail-et-concurrence" class="ancre-titre after"></a></h2>

Notez que les espaces de travail fonctionnent mieux s'il n'y a pas de condition de concurrence lors de leur téléchargement. Étant donné qu'il n'y a aucune garantie quant à l'ordre d'exécution des tâches au sein d'une étape de build, il est recommandé d'attribuer des noms d'espace de travail différents aux tâches au sein d'une étape de construction.

<h2 id="noms-predefinis-espaces-de-travail">Noms prédéfinis d'espace de travail.
<a href="#noms-predefinis-espaces-de-travail" class="ancre-titre after"></a></h2>

Pour des raisons techniques, les noms d'espace de travail seront échappés pour shell. En d'autres termes, étant donné :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
workspaces:
    create:
        name: $TRAVIS_OS_NAME
        paths: …
```

il devra être consommé par :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
workspaces:
    use: $TRAVIS_OS_NAME
```

et pas par :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
workspaces:
    use: linux
```

Même si l'usager fonctionne sur une machine virtuelle Linux.

<h2 id="differences-entre-espaces-de-travail-et-caches">Différence entre les espaces de travail et les caches.
<a href="#differences-entre-espaces-de-travail-et-caches" class="ancre-titre after"></a></h2>

Il convient de réitérer que les espaces de travail sont destinés à partager des fichiers dans la même construction.  Pour les fichiers que vous souhaitez partager entre les différentes builds, utilisez des [caches](./dependances-et-repertoires-du-cache.md).

<h3 id="espaces-de-travail-et-caches-sont-independants">Les espaces de travail et les caches indépendants.
<a href="#espaces-de-travail-et-caches-sont-independants" class="ancre-titre after"></a></h2>

Il est possible d'inclure un seul fichier dans le cache et les espaces de travail. 

<div class="notice cadre">
Si vous redémarrez certaines constructions au sein d'une tâche et que votre espace de travail ne fonctionne pas comme prévu, essayez de redémarrer l'ensemble de la build.
</div>
<hr class="invisible">