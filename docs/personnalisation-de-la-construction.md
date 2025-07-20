# Personnalisation de la construction

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#configuration-et-parametres-de-build-de-travis">Configuration et paramètres de build de Travis CI </a></li>
            <li><a href="#delais-attente-de-la-build">Délais d'attente de la build </a></li>
            <li><a href="#cycle-de-vie-de-la-build">Cyycle de vie de la build </a></li>
            <li><a href="#limiter-les-jobs-simultanes">Limiter les jobs simultanés </a></li>
            <li><a href="#build-du-dernier-commit-uniquement">Build du dernier commit  uniquement </a></li>
            <li><a href="#profondeur-du-clone-git">Profondeur du clone Git</a></li>
            <li><a href="#silence-du-clone-git">Silence du clone Git</a></li>
            <li><a href="#sous-modules-git">Sous-modules Git </a></li>
            <li><a href="#git-lfs">Git LFS </a></li>
            <li><a href="#verification-git-sparce">Vérification Git Sparce<a></li>
            <li><a href="#controle-fin-de-ligne-git">Contrôle de conversion de fin de ligne de Git</a></li>
            <li><a href="#desactiver-git-clone">Désactiver git clone</a></li>
            <li><a href="#definir-option-liens-symboliques">Définir l'option des liens symboliques </a></li>
            <li><a href="#build-branches-specifiques">Build branches spécifiques </a></li>
            <li><a href="#sauter-une-build">Sauter une build<a></li>
            <li><a href="#build-de-matrice">Build de matrice</a></li>
            <li><a href="#installer-deuxième-langage-de-programmation">Installer un deuxième langage de programmation</a></li>
            <li><a href="#builds-complexes">Mettre en œuvre des étapes de construction complexes</a></li>
            <li><a href="#noms-hotes-personnalises">Noms d'hôtes personnalisés<a></li>
           <li><a href="#fournisseur-de-depots-et-de-controle-de-versions">Fournisseurs de référentiels et systèmes de contrôle de version<a></li>
            <li><a href="#version-yaml-pour-travis-yml">Versions YAML pour un fichier .travis.yml</a></li>
            <li><a href="#depannage">Dépannage</a></li>
        </ul>
    </div>
</div>
</p>

<h2 id="configuration-et-parametres-de-build-de-travis">Configuration et paramètres de build de Travis CI
<a href="#configuration-et-parametres-de-build-de-travis" class="ancre-titre after"></a></h2>

Les builds sur Travis CI sont configurées principalement via la configuration stockée dans le fichier `.travis.yml` dans votre référentiel. Cela permet à votre configuration d'être contrôlée par version et d'être flexible.

Pour les cas d'utilisation avancés, le fichier de configuration principal `.travis.yml` peut importer d'autres sources de configuration partagées à l'aide de la fonction [Build Config Imports](./import-de-configuration-pour-la-construction.md).

Des informations détaillées sur le format de configuration de build de Travis CI peuvent être trouvées dans notre [référence de configuration de build Travis CI](https://config.travis-ci.com/). Des informations supplémentaires sur l'utilisation par Travis CI de YAML comme langue pour décrire la configuration de la construction peuvent [être trouvées ici](./configuration-travis-yaml.md).

D'autres fonctionnalités peuvent être vues à travers les paramètres du référentiel, soit à travers l'interface utilisateur de Travis CI, ou alors à travers le [client en ligne de commande](https://github.com/travis-ci/travis.rb#readme) de Travis CI.

<h2 id="delais-attente-de-la-build">Délais d'attente de la build. 
<a href="#delais-attente-de-la-build" class="ancre-titre after"></a></h2>

Il est très fréquent que les suites de tests ou les scripts de build se bloquent. Travis CI a des limites de temps spécifiques pour chaque tâche, il arrêtera la build et ajoutera un message d'erreur au journal de build dans les situations suivantes :

* Lorsqu'un travail ne produit aucune sortie de journal pendant 10 minutes ;
* Lorsqu'un travail sur un dépôt public prend plus de 50 minutes ;
* Lorsqu'un travail sur un dépôt privé prend plus de 120 minutes.

Voici quelques raisons courantes pour lesquelles les constructions peuvent se bloquer :

* Attente d'une saisie au clavier ou d'un autre type d'interaction humaine ;
* Problèmes de concordance (interblocages, impasses, etc.) ;
* Installation d'extensions natives qui prennent beaucoup de temps à compiler.

<div class="notice cadre">
Il n'y a pas de temps mort pour une build ; elle fonctionnera aussi longtemps que nécessaire pour terminer tous les travaux, mais s'arrêtera immédiatement si un travail atteint une limite de temps.
</div>

<h2 id="cycle-de-vie-de-la-build">Cycle de vie de la build. 
<a href="#cycle-de-vie-de-la-build" class="ancre-titre after"></a></h2>

La documentation du [cycle de vie de la build](./travail-cycle-de-vie-build.md) a désormais sa propre page.

<h2 id="limiter-les-jobs-simultanes">Limiter les jobs simultanés.
<a href="#limiter-les-jobs-simultanes" class="ancre-titre after"></a></h2>

Le nombre maximal de travaux simultanés dépend de la charge totale du système, mais une situation dans laquelle vous voudrez peut-être fixer une limite particulière est : 

* Si votre build dépend d'une ressource externe et risque de se heurter à une condition de course avec des tâches simultanées.

Vous pouvez définir le nombre maximum de travaux simultanés dans le volet paramètres pour chaque référentiel.

![Paramètres -> Limiter les versions simultanées](https://docs.travis-ci.com/images/screenshots/concurrent-builds-how-to.png)

Ou, en utilisant le client en ligne de commande :

```console
$ travis settings maximum_number_of_builds --set 1
```

<h2 id="build-du-dernier-commit-uniquement">Build du dernier commit  uniquement.
<a href="#build-du-dernier-commit-uniquement" class="ancre-titre after"></a></h2>

Si vous êtes uniquement intéressé par créer le commit le plus récent sur chaque branche, vous pouvez utiliser cette nouvelle fonctionnalité pour annuler automatiquement les builds plus anciennes dans la file d'attente _qui ne sont pas encore en cours d'exécution_. Les versions existantes seront autorisées à terminer.

Le _paramètre d'annulation automatique_ est dans l'onglet Paramètres de chaque référentiel, et vous pouvez l'activer séparément pour :  

*  _Annulation automatique des builds de branches_  - annule la file d'attente dans votre branche et apparaît dans l'onglet _Historique de build_ de votre référentiel.
*  _Annulation automatique des builds de pull request_ - annule la file d'attente pour les pull request (le futur résultat de la fusion de votre branche de changement/fonctionnalités par rapport à sa cible) et apparaît dans l'onglet _Pull Requests_ de votre référentiel.

![Paramètre d'annulation automatique](https://docs.travis-ci.com/images/autocancellation.png)

Par exemple, dans la capture d'écran suivante, nous avons poussé le commit `CA31C2B` de la branche `MDA-FIX-NOTICE` tandis que les constructions # 226 et # 227 sont en file d'attente. Avec la fonction d'annulation automatique, les builds # 226 et # 227 ont été automatiquement annulées :

![Exemple d'annulation automatique](https://docs.travis-ci.com/images/autocancellation-example.png)

<h2 id="profondeur-du-clone-git">Profondeur du clone Git.
<a href="#profondeur-du-clone-git" class="ancre-titre after"></a></h2>

<div class="notice cadre">
Veuillez noter que si vous utilisez une profondeur de 1 et que vous avez une file d'attente de jobs, Travis CI ne construira pas de validations qui sont dans la file d'attente lorsque vous poussez un nouveau commit.
</div>

Vous pouvez définir la [profondeur du clone](https://git-scm.com/docs/git-clone#git-clone---depthltdepthgt) dans `.travis.yml` :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
git:
    depth: 3
```

Vous pouvez également supprimer entièrement le drapeau `--depth` avec :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
git:
    depth: false
```

<div class="notice cadre">
Certaines opérations sur le référentiel, telles que les scripts de revue de code automatisé courants (par exemple, Pronto pour Ruby), peuvent échouer en raison de la profondeur limitée du clone git qui n'est pas en mesure d'accéder à tous les objets du référentiel. La suppression de l'indicateur de profondeur ou l'exécution de <code>git fetch --unshallow</code> pourrait résoudre le problème.
</div>

<h2 id="silence-du-clone-git">Silence du clone Git.
<a href="#silence-du-clone-git" class="ancre-titre after"></a></h2>

Travis CI clone les dépôts sans l'option quiet (`-q`) par défaut. Activer l'indicateur silencieux peut être utile si vous essayez d'éviter les limites de taille du fichier journal ou même si vous n'avez tout simplement pas besoin de l'inclure.

Vous pouvez activer le [drapeau silencieux](https://git-scm.com/docs/git-clone#git-clone---quiet) dans .travis.yml :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
git:
    quiet: true
```

<h2 id="sous-modules-git">Sous-modules Git.
<a href="#sous-modules-git" class="ancre-titre after"></a></h2>

Travis CI clone les sous-modules Git par défaut. Pour éviter ce réglage :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
git:
    submodules: false
```

<h2 id="git-lfs">Git LFS.
<a href="#git-lfs" class="ancre-titre after"></a></h2>

<h4 id="authentification-github">Authentification GitHub.
<a href="#authentification-github" class="ancre-titre after"></a></h4>

Nous vous recommandons d'utiliser un jeton GitHub OAuth en lecture seule pour vous authentifier lors de l'utilisation de [|Git LFS](https://git-lfs.github.com/) :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
before_install:
   - echo -e "machine github.com\n  login $GITHUB_TOKEN" > ~/.netrc
   - git lfs pull
```

Cette authentification est requise lors de la connexion à des dépôts privés, et empêche la limitation des taux lors de la connexion à des dépôts open source.

Les clés de déploiement ne sont actuellement pas prises en charge par LFS, vous devez donc utiliser un jeton GitHub OAuth pour vous authentifier, comme dans l'exemple ci-dessus.

<h4 id="authentification-bitbucket">Authentification Bitbucket.
<a href="#authentification-bitbucket" class="ancre-titre after"></a></h4>

Nous vous recommandons d'utiliser un token Bitbucket en lecture seule pour vous authentifier lors de l'utilisation de [|Git LFS](https://git-lfs.github.com/) :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
before_install:
   - echo -e "machine bitbucket.com\n  login $BITBUCKET_TOKEN" > ~/.netrc
   - git lfs pull
```

Cette authentification est requise lors de la connexion aux référentiels privés et empêche la limitation des taux lors de la connexion aux référentiels open source.

Les clés de déploiement ne sont pas actuellement prises en charge par LFS, vous devez donc utiliser un token Bitbucket OAuth pour vous authentifier comme dans l'exemple ci-dessus.

<h4 id="authentification-gitlab">Authentification Gitlab.
<a href="#authentification-gitlab" class="ancre-titre after"></a></h4>

Nous vous recommandons d'utiliser un jeton GitLab OAuth en lecture seule pour vous authentifier lors de l'utilisation de Git LFS :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
before_install:
   - echo -e "machine gitlab.com\n  login $GITLAB_TOKEN" > ~/.netrc
   - git lfs pull
```

Cette authentification est requise lors de la connexion aux référentiels privés et empêche la limitation des taux lors de la connexion aux référentiels open source.

Les clés de déploiement ne sont pas actuellement prises en charge par LFS, vous devez donc utiliser un jeton GitLab OAuth pour vous authentifier comme dans l'exemple ci-dessus.

<h4 id="authentification-assembla">Authentification Assembla.
<a href="#authentification-assembla" class="ancre-titre after"></a></h4>

Nous vous recommandons d'utiliser un token Assembla en lecture seule pour vous authentifier lors de l'utilisation de Git LFS :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
before_install:
   - echo -e "machine assembla.com\n  login $ASSEMBLA_TOKEN" > ~/.netrc
   - git lfs pull
```

Cette authentification est requise lors de la connexion aux référentiels privés et empêche la limitation des taux lors de la connexion aux référentiels open source.

Les clés de déploiement ne sont pas actuellement prises en charge par LFS, vous devez donc utiliser un jeton Assembla OAuth pour vous authentifier comme dans l'exemple ci-dessus.

<h4 id="authentification-linux">Linux.
<a href="#authentification-linux" class="ancre-titre after"></a></h4>

Git LFS est pris en charge par défaut sur nos images Ubuntu Fility, Xenial et Bionic.

<h4 id="sauter-smudge">Git LFS sauter Smudge.
<a href="#sauter-smudge" class="ancre-titre after"></a></h4>

Le taux GitHub limite les demandes Git LFS pendant le processus `git clone`. Si vous rencontrez des problèmes de limitation de taux, vous pouvez ignorer la récupération des fichiers Git-LFS pendant le `git clone` initial (équivalent à `git lfs smudge --skip)`), et téléchargez ces actifs pendant la phase `before_install` de votre build. Pour y parvenir, vous pouvez utiliser la configuration suivante dans `.travis.yml` :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
git:
    lfs_skip_smudge: true
```

<h2 id="verification-git-sparce">Vérification Git Sparce.
<a href="#verification-git-sparce" class="ancre-titre after"></a></h2>

Travis CI prend en charge la capacité `git` de vérification sparce.

Pour cloner votre référentiel, ajouter:

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
git:
    sparse_checkout: skip-worktree-map-file
```

où `skip-worktree-map-file` est un chemin vers le fichier existant dans le référentiel actuel avec les données que vous souhaitez mettre dans `$GIT_DIR/info/sparse-checkout`,  fichier de [format décrit dans la documentation Git](https://git-scm.com/docs/git-read-tree#_sparse_checkout).

<h2 id="controle-fin-de-ligne-git">Contrôle de conversion de fin de ligne de Git.
<a href="#controle-fin-de-ligne-git" class="ancre-titre after"></a></h2>

Travis CI clone les dépôts avec un comportement [core.autocrlf](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coreautocrlf) dépendant de la plateforme. Ce comportement peut être modifié via l'attribut autoclf dans `.travis.yml`. Les valeurs valides sont `true`, `false` et `input`.

Pour cloner votre référentiel sans conversion de fin de ligne, ajoutez :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
git:
git:
    autocrlf: input
```

Cela équivaut à `git config --global core.autocrlf input` avant le clonage du référentiel.

<h2 id="desactiver-git-clone">Désactiver git clone.
<a href="#desactiver-git-clone" class="ancre-titre after"></a></h2>

Dans certains flux de travail, comme les [étapes de build](./etapes-de-construction.md), il pourrait être avantageux de sauter l'étape automatique  `git clone`.

Vous pouvez le faire en ajoutant :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
git:
    clone: false
```

<div class="notice cadre">
Notez que si vous utilisez cette option, la variable d'environnement TRAVIS_COMMIT_MESSAGE ne sera pas définie.
</div>

<h2 id="definir-option-liens-symboliques">Définir l'option des liens symboliques.
<a href="#definir-option-liens-symboliques" class="ancre-titre after"></a></h2>

Dans certains cas, lorsqu'un référentiel est utilisé à la fois pour Linux et Windows, il peut être souhaitable de définir l'option [core.symlinks](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coresymlinks).

Pour faire ceci :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
git:
    symlinks: true
```

<h2 id="build-branches-specifiques">Build branches spécifiques.
<a href="#build-branches-specifiques" class="ancre-titre after"></a></h2>

Travis CI utilise le fichier `.travis.yml` de la branche contenant le commit Git qui déclenche la build. Incluez des branches à l'aide d'une liste de sécurité  ou excluez-les à l'aide d'une liste de blocage. 

<div class="notice cadre">
Notez que vous devez également prendre en compte les<a href="../pr.md">builds pull requests</a> automatiques lorsque vous décidez de mettre certaines branches sur liste de sécurité ou sur liste de blocage.
</div>

<h4 id="branches-liste-de-securite-ou-blocage">Branches de liste de sécurité ou de blocage.
<a href="#branches-liste-de-securite-ou-blocage" class="ancre-titre after"></a></h4>

Spécifier les branches à construire à l'aide d'une liste de sécurité, ou bloquer les branches que vous ne voulez pas construire :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
# blocklist
branches:
    except:
   - legacy
   - experimental
# safelist
branches:
    only:
   - master
   - stable
```

<div class="notice cadre">
Notez que la liste de sécurité empêche également la build de commits tagués. Si vous marquez constamment vos versions dans le format <code>v1.3</code>, vous pouvez les saisir tous avec [des expressions régulières](./personnalisation-de-la-construction.md), par exemple <code>/^V\d+\.\d+(\.\d+)?(-\s*)?$/</code>.
</div>

Si vous utilisez à la fois une liste de sécurité et une liste de blocage, la liste de sécurité a priorité. Par défaut, la branche `gh-pages` n'est pas construite sauf si vous l'ajoutez à la liste de sécurité.

Pour construire _toutes_ les branches :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
branches:
    only:
   - gh-pages
   - /.*/
```

<div class="notice cadre">
Notez que pour des raisons historiques `.travis.yml` doit être présent sur toutes les branches actives de votre projet.
</div>

<h4 id="expressions-regulieres">Expressions régulière.
<a href="#expressions-regulieres" class="ancre-titre after"></a></h4>

Vous pouvez utiliser des expressions régulières pour les branches de liste de sécurité ou celles de blocage.

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
branches:
    only:
   - master
   - /^deploy-.*$/
```

Tout nom entouré de `/` dans la liste des branches est traité comme une expression régulière et peut contenir tous les quantificateurs, ancres ou classes de caractères pris en charge par les expressions régulières Ruby.

Les options qui sont spécifiées après le dernier `/` (par exemple, `i` pour une correspondance insensible à la casse) ne sont pas prises en charge mais peuvent être données en ligne à la place. Par exemple, `/^(?i:deploy)-.*$/` correspond à `Deploy-2014-06-01` et aux autres branches et balises qui commencent par `deploy-` dans toutes les combinaisons de cas.

<h2 id="sauter-une-build">Sauter une build.
<a href="#sauter-une-build" class="ancre-titre after"></a></h2>

Si vous ne souhaitez pas lancer une build pour un commit particulier qu'elle qu'en soit la raison, vous pouvez demander à Travis CI de ne pas construire ce commit par le biais d'une commande dans le message de commit.

La commande doit être l'une des formes suivantes: 

```console
[<KEYWORD> skip]
```

ou

```console
[skip <KEYWORD>]
```

où `<KEYWORD>` est soit `ci`, `travis`, `travis ci`, `travis-ci`, ou `travisci`. Par exemple :

```console
[skip travis] Update README
```

Notez que dans le cas où plusieurs commits sont poussés ensemble, la commande skip n'est efficace que si elle est présente dans le message du HEAD commit.

<h2 id="build-de-matrice">Build de matrice.
<a href="#build-de-matrice" class="ancre-titre after"></a></h2>

Vous pouvez également définir des exclusions à la build matrice :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
jobs:
    exclude:
   - rvm: 1.9.3
        gemfile: gemfiles/Gemfile.rails-2.3.x
        env: ISOLATED=true
   - rvm: jruby
        gemfile: gemfiles/Gemfile.rails-2.3.x
        env: ISOLATED=true
```

<div class="notice cadre">
Toutes les buils de matrice  sont actuellement limitées à <code>200 jobs</code> pour les référentiels privés et publics. Si vous êtes sur un plan open-source, n'oubliez pas que Travis CI fournit ce service gratuitement à la communauté. Veuillez donc spécifier uniquement la matrice dont vous avez réellement besoin.
</div>

<h4 id="nom-des-jobs-dans-les-matrices">Nom des travaux dans les matrices.
<a href="#nom-des-jobs-dans-les-matrices" class="ancre-titre after"></a></h4>

Vous pouvez définir des noms pour des jobs spécifiques dans une matrice. Nous recommandons des noms de travail uniques, mais ne l'appliquez pas (bien que cela puisse changer à l'avenir).  Les jobs définis dans la section `matrix.include` peuvent se voir attribuer un des noms de jobs suivant :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
language: python
jobs:
    include:
   - name: "3.5 Unit Test"
        python: "3.5"
        env: TEST_SUITE=suite_3_5_unit
   - name: "3.5 Integration Tests"
        python: "3.5"
        env: TEST_SUITE=suite_3_5_integration
   - name: "pypy Unit Tests"
        python: "pypy"
        env: TEST_SUITE=suite_pypy_unit
    script: ./test.py $TEST_SUITE
```

Les travaux générés par l'expansion de la matrice ne peuvent pas recevoir des attributs de nom.

<h4 id="jobs-exclus">Jobs exclus.
<a href="#jobs-exclus" class="ancre-titre after"></a></h4>
 
Si les travaux que vous souhaitez exclure de la build matrice partagent les mêmes paramètres de matrice, vous pouvez spécifier uniquement ceux-ci et omettre les parties variables.

Supposons que vous ayez :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
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

Il en résulte une build matrice de 3 × 3 × 4. Pour exclure tous les travaux qui ont `rvm` value `2.0.0` et `gemfile` value `Gemfile`, vous pouvez écrire :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
jobs:
    exclude:
    - rvm: 2.0.0
        gemfile: Gemfile
```

Ce qui équivaut à :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
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

<h5 id="jobs-exclus-avec-env">Exclure des jobs avec la Valeur env.
<a href="#jobs-exclus-avec-env" class="ancre-titre after"></a></h5>
 
Lorsque vous excluez les jobs avec les valeurs `env`, la valeur doit correspondre _exactement_.

Par exemple :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
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

Définit une matrice 3 × 4, car la valeur `env` ne correspond à aucun job défini dans la matrice.

Pour exclure tous les travaux Ruby 1.9.3 avec les réglages `DB=mongodb`, écrivez :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
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
      env: DB=mongodb SUITE=all # not 'env: DB=mongodb  SUITE=all' or 'env: SUITE=all DB=mongodb'
      - rvm: 1.9.3
      env: DB=mongodb SUITE=compact # not 'env: SUITE=compact DB=mongodb'
```

<h4 id="jobs-explicitement-inclus">Emplois explicitement inclus.
<a href="#jobs-explicitement-inclus" class="ancre-titre after"></a></h4>

Il est également possible d'inclure des entrées dans la matrice avec `matrix.include` :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
jobs:
    include:
    - rvm: ruby-head
        gemfile: gemfiles/Gemfile.rails-3.2.x
        env: ISOLATED=false
```

Cela ajoute un travail particulier à la build matrice qui a déjà été remplie.

Ceci est utile si vous souhaitez tester uniquement la dernière version d'une dépendance avec la dernière version de l'exécution.

Vous pouvez utiliser cette méthode pour créer une build de matrice ne contenant que des combinaisons spécifiques. Par exemple, ce qui suit crée une build de matrice  avec 3 travaux, qui exécute une suite de tests pour chaque version de Python :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
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

<h5 id="heritage-premiere-valeur-tableau">Les jobs inclus explicitement héritent la première valeur du tableau.
<a href="#heritage-premiere-valeur-tableau" class="ancre-titre after"></a></h5>

Les jobs qui sont explicitement inclus héritent de la première valeur des clés d'extension définies.

Dans cet exemple avec une build de matrice Python à 3 jobs, chaque job dans `matrix.include` a la valeur `python` fixée à `'3.8'`. Vous pouvez définir explicitement la version de Python pour une entrée spécifique :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
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

<h5 id="autoriser-intentionnellement-les-jobs-a-echouer">Autoriser intentionnellement les jobs à échouer.
<a href="#autoriser-intentionnellement-les-jobs-a-echouer" class="ancre-titre after"></a></h5>

Vous pouvez définir des jobs autorisés à échouer dans la build de matrice.

Les échecs autorisés sont des jobs de votre build de matrice qui sont autorisées à échouer sans entraîner l'échec de la build entière. Cela vous permet d'ajouter des builds expérimentales et préparatoires, par exemple, pour tester des versions d'exécution ou des configurations que vous n'êtes pas prêt à prendre en charge officiellement.

Définir les échecs autorisés dans la build de matrice comme paires de clé/valeur :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
jobs:
    allow_failures:
    - rvm: 1.9.3
```

<h5 id="autoriser-conditionnellement-les-jobs-a-echouer">Autoriser conditionnellement les jobs à échouer.
<a href="#autoriser-conditionnellement-les-jobs-a-echouer" class="ancre-titre after"></a></h5>

Les échecs autorisés peuvent inclure une [condition](./constrctions-conditionnelle.md) en utilisant la clé `if`.

Par exemple, l'option suivante permettrait au job utilisant `rvm : 1.9.3` de n'échouer que sur la branche principale :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
jobs:
    allow_failures:
    - rvm: 1.9.3
        if: branch = master
```

<h5 id="jobs-avec-attribut">Correspondance des jobs avec l'attribut allow_failures.
<a href="#jobs-avec-attribut" class="ancre-titre after"></a></h5>

Lors de la comparaison des travaux avec les définitions données dans `allow_failures`, _tous_ les attributs spécifiés dans une entrée de `allow_failures` doivent être exactement respectés et toutes les clés de l'élément `allow_failures` doivent exister au niveau supérieur de la build de matrice (c'est-à-dire pas dans `matrix.include`).

<h5 id="exemples-allow-failures">Exemples allow_failures.
<a href="#exemples-allow-failures" class="ancre-titre after"></a></h5>

Considérer ce qui suit :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
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

Ici, aucun travail n'est autorisé à échouer car aucun travail n'a la valeur `env` définie `SECRET_VAR1=SECRET1 SECRET_VAR2=SECRET2`.

Suivant :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
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

Sans `env` de haut niveau, aucun travail ne sera autorisé à échouer.

<h5 id="finition-rapide">Utilisez une finition rapide.
<a href="#finition-rapide" class="ancre-titre after"></a></h5>

Si certains jobs dans la build de matrice sont autorisés à échouer, la build ne sera pas marquée comme terminée avant sa fin.

Pour marquer la build telle que terminée dès que possible, ajoutez `fast_finish: true`  à la section matrice de votre `.travis.yml` comme ceci :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
jobs:
    fast_finish: true
```

Désormais, le résultat de la build sera déterminé dès que tous les travaux requis se terminent, en fonction de ces résultats, tandis que le reste des travaux `allow_failures` continuent de s'exécuter.

<h2 id="installer-deuxième-langage-de-programmation">Installez un deuxième langage de programmation.
<a href="#installer-deuxième-langage-de-programmation" class="ancre-titre after"></a></h2>

Si vous devez installer un deuxième langage de programmation dans votre environnement de build actuel, vous pouvez le faire dans l'étape `before_install` de la build.

Par exemple, vous pouvez utiliser la recette suivante pour installer une version personnalisée de Ruby dans une build Python :

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
language: python
before_install:
  - rvm install 2.1.5
```

Vous pouvez également installer une version PHP personnalisée dans une build Node.js comme ceci:

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
language: node_js
before_install:
  - phpenv global 7.0
```

Il est également possible d'utiliser d'autres méthodes d'installation linguistique telles que `apt-get`, `pyenv` pour Python, `nvm`pour Node.js, etc.

<h2 id="builds-complexes">Mettre en œuvre des étapes de construction complexes.
<a href="#builds-complexes" class="ancre-titre after"></a></h2>

Si vous avez un environnement de build complexe qui est difficile à configurer dans le fichier `.travis.yml`, envisagez de déplacer les étapes dans un script shell séparé. Le script peut faire partie de votre référentiel et être facilement appelé depuis le fichier `.travis.yml`.

Voir [commandes de construction complexes](./etapes-de-construction.md) pour plus d'informations sur la façon de procéder.

<h2 id="noms-hotes-personnalises">Noms d'hôtes personnalisés.
<a href="#noms-hotes-personnalises" class="ancre-titre after"></a></h2>

Si votre build nécessite la mise en place de noms d'hôtes personnalisés, vous pouvez spécifier un seul hôte ou une liste d'hôtes dans votre fichier `.travis.yml`. Travis CI configurera automatiquement les noms d'hôtes dans `/etc/hosts` pour IPv4 et IPv6.

<div class="titre-code"> &nbsp;.travis.yml</div>
```yaml
addons:
    hosts:
    - travis.test
    - joshkalderimis.com
```

<h2 id="fournisseur-de-depots-et-de-controle-de-versions">Fournisseurs de référentiels et systèmes de contrôle de version.
<a href="#fournisseur-de-depots-et-de-controle-de-versions" class="ancre-titre after"></a></h2>

Construisez et testez vos dépôts open source et privés hébergés sur GitHub sur [travis-ci.com](https://travis-ci.com/). Travis CI peut également s'intégrer à Atlassian [Bitbucket](https://bitbucket.org/), [GitLab](https://about.gitlab.com/) et [Assembla](https://www.assembla.com/).

Travis CI ne supporte pas actuellement les dépôts git hébergés sur d'autres systèmes de contrôle de version tels que Mercurial.

<h2 id="version-yaml-pour-travis-yml">Versions YAML pour un fichier .travis.yml.
<a href="#version-yaml-pour-travis-yml" class="ancre-titre after"></a></h2>

Travis CI utilise la bibliothèque Ruby libYAML, ce qui signifie que votre fichier `.travis.yml` doit être conforme YAML 1.1.

<h2 id="depannage">Dépannage
<a href="#depannage" class="ancre-titre after"></a></h2>

Consultez la liste des [problèmes de build les plus courants](./problemes-courants-de-construction.md).
<hr class="invisible">
