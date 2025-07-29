# Etapes de construction
   
<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#que-sont-les-etapes-de-construction">Que sont les étapes de construction ?</a></li>
            <li><a href="#comment-fonctionnent-les-etapes-de-construction">Comment fonctionnent les étapes de construction ?</a></li>
            <li><a href="#definir-les-etapes-de-construction">Définir les étapes de construction</a></li>
            <li><a href="#references-de-configuration-de-construction">Référence de configuration de construction</a></li>
            <li><a href="#nommer-les-etapes-de-construction">Nommer les étapes de construction</a></li>
            <li><a href="#expansion-de-la-matrice-de-construction">Étapes de construction et expansion de la matrice de construction</a></li>
            <li><a href="#ordre-et-conditions-des-etapes">Spécifier l'ordre et les conditions des étapes</a></li>
            <li><a href="#etapes-de-construction-et-deploiement">Étapes de construction et déploiements</a></li>
            <li><a href="#persistance-des-donnees">Persistance des données entre les étapes et les tâches</a></li>
            <li><a href="#exemples">Exemples</a></li>
        </ul>
        </ul>
    </div>
</div>
</p>

<h2 id="que-sont-les-etapes-de-construction">Que sont les étapes de construction ?
<a href="#que-sont-les-etapes-de-construction" class="ancre-titre after"></a></h2>

Les étapes de construction permettent de regrouper des tâches et d'exécuter les tâches de chaque étape en parallèle, mais en les exécutant les unes après les autres de manière séquentielle.

Dans le cas d'utilisation le plus simple et le plus courant, vous pouvez désormais exécuter une tâche _uniquement_ si plusieurs autres tâches parallèles ont été effectuées avec succès.

Supposons que vous souhaitiez tester une bibliothèque telle qu'un gem Ruby ou un paquet npm sur différentes versions d'exécution (Ruby ou Node.js) en [parallèle](./personnalisation-de-la-construction.md#matrice-de-construction). Et vous souhaitez publier votre gem ou votre paquet **uniquement** si tous les tests ont été réussis et terminés avec succès. Les étapes de construction rendent cela possible.

Bien sûr, il existe de nombreux autres cas d'utilisation beaucoup plus élaborés que celui-ci. Vous pouvez, par exemple, également utiliser les étapes de construction pour préchauffer les caches de dépendances dans une seule tâche lors de la première étape, puis utiliser le cache dans plusieurs tâches lors de la deuxième étape. Vous pouvez également générer une image Docker et la pousser en premier, puis la tester dans plusieurs tâches en parallèle. Vous pouvez également exécuter des tests unitaires, déployer en préproduction, exécuter des tests de fumée et ensuite seulement déployer en production.

<h2 id="comment-fonctionnent-les-etapes-de-construction">Comment fonctionnent les étapes de construction ?
<a href="#comment-fonctionnent-les-etapes-de-construction" class="ancre-titre after"></a></h2>

Le concept des étapes de construction est puissant et flexible, mais simple et accessible :

Les étapes regroupent les tâches qui s'exécutent en parallèle, tandis que les différentes étapes s'exécutent de manière séquentielle.

Une étape est un ensemble de tâches qui peuvent être exécutées en parallèle. Cependant, chacune des étapes s'exécute l'une après l'autre et ne se poursuit que si toutes les tâches de l'étape précédente ont été exécutées avec succès. Si une tâche échoue dans une étape, toutes les autres tâches de cette même étape seront tout de même exécutées, mais toutes les tâches des étapes suivantes seront annulées et la compilation échouera.

Vous pouvez configurer autant de tâches par étape que vous en avez besoin, et vous pouvez avoir autant d'étapes que votre processus de livraison le nécessite.

Dans l'exemple suivant, nous exécutons deux tâches sur la première étape appelée test, puis exécutons une seule troisième tâche sur la deuxième étape appelée deploy :

![capture d'écran d'exemple](https://docs.travis-ci.com/images/stages/stages.gif)

<h2 id="definir-les-etapes-de-construction">Définir les étapes de construction.
<a href="#definir-les-etapes-de-construction" class="ancre-titre after"></a></h2>

Voici comment régler la configuration de build pour faire cela dans votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    include:
        - stage: test
            script: ./test 1
        - # stage name not required, will continue to use `test`
            script: ./test 2
        - stage: deploy
            script: ./deploy
```

Cette configuration crée la version à partir de la capture d'écran ci-dessus. C'est-à-dire qu'il crée une construction avec trois tâches, dont deux commencent en parallèle dans la première étape (nommé test), tandis que la troisième tâche de la deuxième étape (nommé deploy) ne commence qu'après l'étape de test ne soit terminée.

<h2 id="references-de-configuration-de-construction">Référence de configuration de construction.
<a href="#references-de-configuration-de-construction" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour les [étapes de construction](https://config.travis-ci.com/ref/stages) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="nommer-les-etapes-de-construction">Nommer les étapes de construction.
<a href="#nommer-les-etapes-de-construction" class="ancre-titre after"></a></h2>

Les étapes sont identifiées par leur nom, qui est composé d'un nom et d'émojis. La première lettre du nom d'une étape est automatiquement mise en majuscule pour des raisons esthétiques, vous n'avez donc pas à vous soucier des chaînes en majuscules dans votre fichier `.travis.yml`.

De plus, vous n'avez pas besoin de spécifier le nom pour chaque tâche (comme dans l'exemple ci-dessus). L'étape par défaut est `test`. Les tâches qui n'ont pas de nom d'étape sont attribuées au nom de l'étape précédente, s'il existe, ou au nom d'étape par défaut, s'il n'y a pas de nom d'étape précédente. Cela signifie que si vous définissez le nom de l'étape sur la première tâche de chaque étape, la construction fonctionnera comme prévu.

Par exemple, la configuration suivante est équivalente à celle ci-dessus, mais ajoute également une deuxième tâche de déploiement à l'étape `deploy` qui se déploie vers une cible différente. Comme vous pouvez le constater, vous ne devez spécifier le nom de l'étape qu'une seule fois :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    include:
        - script: ./test 1 # uses the default stage name "test"
        - script: ./test 2
        - stage: deploy
            script: ./deploy target-1
        - script: ./deploy target-2
```

<h3 id="nommer-des-taches">Nom des tâches au sein des étapes de construction.
<a href="#nommer-des-taches" class="ancre-titre after"></a></h3>

Vous pouvez également nommer des tâches spécifiques dans les étapes de construction. Nous recommandons des noms de tâches uniques, mais ne l'appliquez pas (bien que cela puisse changer à l'avenir). Les tâches définies dans la section `jobs.include` peuvent recevoir un attribut de nom comme suit :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    include:
        - stage: "Tests"                # naming the Tests stage
            name: "Unit Tests"            # names the first Tests stage job
            script: ./unit-tests
        - script: ./integration-tests
            name: "Integration Tests"     # names the second Tests stage job
        - stage: deploy
            name: "Deploy to GCP"
            script: ./deploy
```

<h2 id="expansion-de-la-matrice-de-construction">Étapes de construction et expansion de la matrice de construction.
<a href="#expansion-de-la-matrice-de-construction" class="ancre-titre after"></a></h2>

[L'expansion de la matrice](./personnalisation-de-la-construction.md#matrice-de-construction) signifie que certaines clés de configuration de niveau supérieur se développent dans une matrice de tâches.

Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
rvm:
    - 2.3
    - 2.4
jobs:
    include:
      - stage: deploy
        rvm: 2.4
        env:
          - FOO=foo
        script: ./deploy
```

Cela exécutera deux tâches sur Ruby 2.3 et 2.4, respectivement, en premier lieu, et les assignera au test de la phase par défaut. La troisième tâche de la phase de déploiement ne démarre qu'une fois la phase de test terminée avec succès.

<div class="notice cadre">
Chaque tâche incluse dans <code>jobs.include</code> hérite de la première valeur du tableau qui définit une dimension de matrice. Dans l'exemple ci-dessus, sans définir explicitement <code>rvm: 2.4</code>, la tâche <code>include</code> hérite de <code>rvm: 2.3</code>.
</div>

<h2 id="ordre-et-conditions-des-etapes">Spécifier l'ordre et les conditions des étapes.
<a href="#ordre-et-conditions-des-etapes" class="ancre-titre after"></a></h2>

Vous pouvez spécifier l'ordre des étapes dans la section étapes :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
stages:
    - compile
    - test
    - deploy
```

Cela est particulièrement utile pour « ajouter » une étape à l'étape de test à laquelle seront affectées les tâches résultant de l'expansion matricielle.

Dans la même section, vous pouvez également spécifier des conditions pour les étapes, comme suit :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
stages:
    - compile
    - test
    - name: deploy
        if: branch = master
```

Voir les [versions, étapes et taĉhes conditionnelles](./constructions-conditionnelles.md) pour plus de détails sur la spécification des conditions.

<h2 id="etapes-de-construction-et-deploiement">Étapes de construction et déploiements.
<a href="#etapes-de-construction-et-deploiement" class="ancre-titre after"></a></h2>

Vous pouvez combiner les étapes de construction avec les [déploiements](./aperçu-deploiement.md) :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    include:
      - script: ./test 1 # uses the default stage name "test"
      - script: ./test 2
      - stage: deploy
        script: skip     # usually you do not want to rerun any tests
        deploy: &heroku
            provider: heroku
            # ⋮
```

Travis CI ne définit ni n'écrase aucun de vos scripts et la plupart des langues ont un [script de test par défaut](./construire-un-projet-en-ruby.md#script-de-build-par-defaut) défini. Ainsi, dans de nombreux cas d'utilisation, vous voudrez peut-être écraser l'étape `script` en spécifiant le mot-clé `skip` ou `ignore`, dans d'autres cas, vous voudrez peut-être écraser d'autres étapes, telles que l'étape `install` qui s'exécute par défaut dans plusieurs langages.

<h2 id="persistance-des-donnees">Persistance des données entre les étapes et les tâches.
<a href="#persistance-des-donnees" class="ancre-titre after"></a></h2>

<div class="notice cadre">
Il est important de noter que les tâches ne partagent pas de stockage, car chaque tâche s'exécute dans une nouvelle machine virtuelle ou un conteneur. Si vos tâches doivent partager des fichiers (par exemple, en utilisant des artefacts de construction à partir de l'étape «Test» de déploiement dans l'étape «Deploy» suivante), vous devez utiliser un mécanisme de stockage externe tel que S3 et un serveur <code>scp</code> distant.
</div>

Voir l'exemple S3 ci-dessous.

<h2 id="exemples">Exemples.
<a href="#exemples" class="ancre-titre after"></a></h2>

<h3 id="deployer-sur-heroku">Déployer sur Heroku.
<a href="#deployer-sur-heroku" class="ancre-titre after"></a></h3>

Un exemple avec 5 étapes: 

* Deux tâches exécutant des tests unitaires en parallèle sur l'étape 1 ;
* Une tâche déployant l'application sur Heroku staging ;
* Une tâche testant le déploiement d'étapes sur Heroku ;
* Une tâche déployant l'application sur Heroku production ;
* Une tâche testant le déploiement de la production sur Heroku.

Vous pouvez trouver plus de [détails ici](./etapes-de-construction-sur-heroku.md).

<h3 id="deployer-sur-rubygems">Déployer sur Rubygems.
<a href="#deployer-sur-rubygems" class="ancre-titre after"></a></h3>

Cet exemple a deux étapes de construction : 

* Deux tâches qui exécutent des tests respectivement sur Ruby 2.2 et 2.3 ;
* Une tâche qui publie le gem sur rubygems.org.

Vous pouvez trouver plus de [détails ici](./etapes-de-construction-rubygems.md).

<h3 id="deployer-sur-npm">Déployer sur NPM.
<a href="#deployer-sur-npm" class="ancre-titre after"></a></h3>

Cet exemple a deux étapes de construction : 

* Quatre tâches qui exécutent des tests sur les versions 4 à 7 de Node ;
* Une tâche qui déploie (publie)  sur NPM.

Vous pouvez trouver plus de [détails ici](./etapes-de-construction-deployer-sur-npm.md).

<h3 id="deployer-sur-github-releases">Déployer sur GitHub Releases.
<a href="#deployer-sur-github-releases" class="ancre-titre after"></a></h3>

Cet exemple comporte deux étapes de construction :

*  Quatre tâches qui exécutent des tests ;
* Une tâche qui déploie vers GitHub Releases.

Vous trouverez plus de [détails ici](./etapes-de-construction-sur-github.md).

<h3 id="combiner-avec-extension-de-la-matrice">Combiner des étapes de construction avec l'extension de la matrice.
<a href="#combiner-avec-extension-de-la-matrice" class="ancre-titre after"></a></h3>

Cet exemple a deux étapes de construction : 

* Quatre tâches de test qui ont été étendues à partir des clés de matrice `rvm` et `env` ;
* Une tâche de déploiement.

Vous pouvez trouver plus de [détails ici](./etapes-de-construction-sur-github.md).

<h3 id="rechauffer-un-cache">Réchauffer un cache avec des dépendances coûteuses.
<a href="#rechauffer-un-cache" class="ancre-titre after"></a></h3>

Cela utilise deux étapes de construction afin de réchauffer un cache avec des dépendances coûteuses et d'optimiser les temps d'exécution : 

* Une tâche qui installe les dépendances et préchauffe le cache pour la branche donnée ;
* Trois tâches qui exécutent des tests en utilisant le cache.

Vous pouvez trouver plus de [détails ici](./etapes-de-construction-cache.md).

<h3 id="partager-une-image-docker">Partagez une image Docker.
<a href="#partager-une-image-docker" class="ancre-titre after"></a></h3>

Cet exemple a 2 étapes de construction : 

* Une tâche crée et pousse une image Docker ;
* Deux tâches récupèrent et testent l'image.

Vous pouvez trouver plus de [détails ici](./etapes-de-construction-image-docker.md).

<h3 id="partager-des-fichiers-via-s3">Partagez des fichiers entre les tâches via S3.
<a href="#partager-des-fichiers-via-s3" class="ancre-titre after"></a></h3>

Ceci utilise deux étapes de construction, partageant les fichiers de l'étape 1 dans l'étape 2 :

* Deux tâches qui configurent les fichiers sur S3 ;
* Une tâche qui utilise les deux fichiers de l'étape 1.

Vous pouvez trouver plus de [détails ici](./etapes-de-construction-via-s3.md).

<h3 id="differentes-etapes-dans-differentes-phases">Définir différentes étapes dans différentes phases.
<a href="#differentes-etapes-dans-differentes-phases" class="ancre-titre after"></a></h3>

Cet exemple comporte deux phases de build :

* Deux tâches qui exécutent différentes suites de tests sur Ruby 2.3.1;
* Une tâche qui exécute un script de déploiement personnalisé qui ne nécessite pas l'exécution des étapes d'installation ou de script par défaut.

Vous pouvez trouver plus de [détails ici](./etapes-de-construction-differentes-etapes.md).

<h3 id="etapes-avec-alias-yaml">Définir les étapes à l'aide d'alias YAML.
<a href="#etapes-avec-alias-yaml" class="ancre-titre after"></a></h3>

Cet exemple utilise des alias YAML pour définir les étapes. Il comporte 3 phases de build :

* Deux tâches qui exécutent des tests sur Ruby 2.2 et 2.3 ;
* Une tâche qui déploie sur l'environnement de test ;
* Trois tâches qui exécutent des tests sur l'environnement de test.

Vous pouvez trouver plus de [détails ici](./etapes-de-construction-alias-yaml).
<hr class="invisible">