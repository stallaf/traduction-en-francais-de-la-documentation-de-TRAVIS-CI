# Constructions conditionnelles, étapes et tâches

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#constructions-conditionnelles">Constructions conditionnelles</a></li>
            <li><a href="#etapes-conditionnelles">Étapes conditionnelles</a></li>
            <li><a href="#taches-conditionnelles">Tâches conditionnelles</a></li>
            <li><a href="#exclusion-conditionnelles-de-taches">Exclusion conditionnelle de tâches</a></li>
            <li><a href="#autorisation-conditionnelle-des-echecs-de-taches">Autorisation conditionnelle d'échec de tâches</a></li>
            <li><a href="#specifier-et-tester-les-conditions">Spécifier et tester les conditions</a></li>
        </ul>
        </ul>
    </div>
</div>
</p>

Vous pouvez filtrer et rejeter les builds, les étapes et les tâches en spécifiant les conditions dans votre configuration de build (votre fichier `.travis.yml`).

Vous pouvez trouver plus d'informations sur le format de configuration de build dans notre [référence de configuration Travis CI Build](https://config.travis-ci.com/ref/job/if/condition).

<h2 id="constructions-conditionnelles">Constructions conditionnelles.
<a href="#constructions-conditionnelles" class="ancre-titre after"></a></h2>

Vous pouvez configurer Travis CI pour exécuter les builds uniquement lorsque certaines conditions sont remplies. Toutes les versions qui ne remplissent pas ces conditions sont répertoriées dans l'onglet _Requests_ de votre référentiel, même si la version réelle n'est pas générée. 

<div class="notice cadre">
Le système de Travis CI récupère et traite le fichier de configuration .travis.yml du référentiel et la branche explicitement spécifiée dans la demande de build.
</div>

Par exemple, cela permet à des builds uniquement d'exécuter sur la branche `master` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
# require the branch name to be master (note for PRs this is the base branch name)
if: branch = master
```

Les demandes de construction qui ne correspondent pas à la condition ne généreront pas de build, mais seront répertoriées dans l'onglet _Requests_.

<h2 id="etapes-conditionnelles">Étapes conditionnelles.
<a href="#etapes-conditionnelles" class="ancre-titre after"></a></h2>

Vous pouvez configurer Travis CI pour inclure uniquement les étapes lorsque certaines conditions sont remplies. Les étapes qui ne correspondent pas à l'état donné sont silencieusement sautées. Par exemple, cela permet à l'étape de déploiement de s'exécuter uniquement sur la branche `master` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
stages:
    - name: deploy
    # require the branch name to be master (note for PRs this is the base branch name)
    if: branch = master
```

Les étapes qui ne correspondent pas à la condition seront ignorées en silence.

<h2 id="taches-conditionnelles">Tâches conditionnelles.
<a href="#taches-conditionnelles" class="ancre-titre after"></a></h2>

Vous pouvez configurer Travis CI pour inclure uniquement les tâches lorsque certaines conditions sont remplies. Par exemple, cela inclut la tâche répertoriée uniquement pour construire sur la branche `master` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jobs:
    include:
      - # require the branch name to be master (note for PRs this is the base branch name)
        if: branch = master
        env: FOO=foo
```

Il faut lister explicitement les tâches, c'est-à-dire en utilisant `jobs.include` (ou son alias `matrix.include`), pour pouvoir définir leurs conditions. Les tâches créées via [l'expansion matricielle](./personnalisation-de-la-construction.md#matrice-de-construction) ne peuvent pas avoir de conditions pour le moment, mais elles peuvent être exclues de façon conditionnelle (voir [ci-dessous](#exclusion-conditionnelles-de-taches)).

<div class="notice cadre">
Les tâches qui ne correspondent pas à la condition seront ignorés en silence.
</div>

<h2 id="exclusion-conditionnelles-de-taches">Exclusion conditionnelle de tâches.
<a href="#exclusion-conditionnelles-de-taches" class="ancre-titre after"></a></h2>

Vous pouvez configurer Travis CI pour exclure les tâches lorsque certaines conditions sont remplies. Par exemple, cela créera deux tâches sur toutes les succursales, mais un seul travail (avec la var env `ONE = one`) sur la branche `master` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
env:
    - ONE=one
    - TWO=two
jobs:
    exclude:
      - if: branch = master
        env: TWO=two
```

<h2 id="autorisation-conditionnelle-des-echecs-de-taches">Autorisation conditionnelle d'échec de tâches.
<a href="#autorisation-conditionnelle-des-echecs-de-taches" class="ancre-titre after"></a></h2>

Vous pouvez configurer Travis CI pour que les tâches échouent uniquement lorsque certaines conditions sont remplies. Par exemple, cela permettra à la tâche avec la variable env `TWO=two` d'échouer lorsque la construction s'exécute sur la branche `dev` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
env:
    - ONE=one
    - TWO=two
jobs:
    allow_failures:
      - if: branch = dev
        env: TWO=two
```

<h2 id="specifier-et-tester-les-conditions">Spécifier et tester les conditions.
<a href="#specifier-et-tester-les-conditions" class="ancre-titre after"></a></h2>

Veuillez consulter les [conditions](./conditions-v1.md) pour des exemples et une spécification de la syntaxe des conditions.

Les conditions peuvent être testées à l'aide de la commande `travis-conditions`. Apprenez à [tester vos conditions](./tester-les-conditions.md).
<hr class="invisible">
