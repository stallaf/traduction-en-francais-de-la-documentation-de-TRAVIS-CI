# Cycle de vie d'une tâche

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#phases-cycle-de-vie-de-tache">Phases du cycle de vie d'une taĉhe</a></li>
            <li><a href="#personnaliser-la-phase-installation">Personnaliser la phase d'installation</a></li>
            <li><a href="#personnaliser-la-phase-de-build">Personnaliser la phase de build</a></li>
            <li><a href="#interrompre-la-build">Interrompre la build</a></li>
            <li><a href="#deploiement-du-code">Déploiement du code</a></li>
        </ul>
    </div>
</div>
</p>

Travis CI fournit un environnement de build par défaut et un ensemble de phases par défaut pour chaque langage de programmation. Il crée une machine virtuelle avec l'environnement de build pour votre tâche, y clone votre référentiel, installe des modules complémentaires optionnels et exécute vos phases de build comme spécifié.

Des étapes de build personnalisées seront probablement utilisées ultérieurement pour des définitions de compilation plus complexes. Il est important de comprendre comment chaque tâche passe par les différentes phases du cycle de vie, car vous pouvez les utiliser pour personnaliser vos définitions de build.

Dans cette section, vous apprendrez le processus du cycle de vie d'une tâche et comment personnaliser n'importe quelle phase du processus de build.

<div class="notice cadre">
<strong>Remarque :</strong> avant de continuer, assurez-vous de bien comprendre notre terminologie de base. Consultez notre <a href="concepts-de-base-pour-debutants.md#construction-etapes-taches-et-phases">documentation sur les concepts fondamentaux</a> et jetez un œil à la <a href="https://config.travis-ci.com/">référence de configuration de compilation Travis CI</a> pour plus d'informations.
</div>

<h2 id="phases-cycle-de-vie-de-tache">Phases du cycle de vie d'une taĉhe.
<a href="#phases-cycle-de-vie-de-tache" class="ancre-titre after"></a></h2>

Le fichier « .travis.yml » décrit le processus de build pour Travis CI. Une _build_ dans Travis CI se compose d'au moins une étape sans nom ou d'une séquence [d'étapes](./etapes-de-construction.md). Chaque _étape_ se compose d'une ou plusieurs _tâches_ s'exécutant en parallèle à l'intérieur de l'étape.

![Diagramme des builds, étapes et travaux](https://docs.travis-ci.com/user/images/Build-stages-jobs.png)

Chaque _tâche_ est une séquence de _phases_, qui sont des étapes séquentielles formant une tâche. Le tableau suivant présente les principales phases que les utilisateurs peuvent ajouter aux tâches.

<p>
<table class="tableau">
    <thead>
        <tr>
            <th>Phase de pré-installation (facultative)</th>
            <th>Phase d'installation</th>
            <th>Phase de script</th>
            <th>Phase de déploiement (facultative)</th>
            <th>Après la phase de script</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Travis CI peut exécuter ces commandes facultatives au cours de cette phase, dans l'ordre suivant :<br>
            <code>- apt addons</code><br>
            <code>- cache components</code></td>
            <td>Travis CI peut exécuter les commandes personnalisées suivantes dans cette phase principale, dans cet ordre :<br>
            <code>- before_install</code> : avant la phase d'installation.<br>
            <code>- install</code> : installe toutes les dépendances requises.</td>
            <td>Travis CI peut exécuter les commandes personnalisées suivantes dans cette phase principale, dans l'ordre suivant :<br>
            <code>- before_script</code> : avant la phase de script.<br>
            <code>- script</code> : exécute le script de build.<br>
            <code>- before_cache</code> : (FACULTATIF, si et seulement si la mise en cache est efficace)<br>
            <code>- after_success</code> : lorsque la build réussit (par exemple, build de la documentation), le résultat se trouve dans la variable d'environnement <code>TRAVIS_TEST_RESULT</code>.<br>
            <code>- after_failure</code> : lorsque la build échoue (par exemple, téléchargement des fichiers journaux), le résultat se trouve dans la variable d'environnement <code>TRAVIS_TEST_RESULT</code>.</td>
            <td>Travis CI peut exécuter des commandes personnalisées dans les phases, dans l'ordre suivant :<br>
            <code>- before_deploy</code> : (si et seulement si le déploiement est actif)<br>
            <code>- deploy</code> : phase de déploiement facultative.<br>
            <code>- after_deploy</code> : (si et seulement si le déploiement est actif)</td>
            <td>Travis CI peut exécuter des commandes personnalisées dans les phases, dans l'ordre suivant :<br>
            <code>- after_script</code> : après la phase de script.</td>
        </tr>
    </tbody>
</table>
</p>

<div class="notice cadre">
<strong>Remarque :</strong> Une build peut être composée de nombreuses tâches.
</div>

Ce qui suit est un exemple de la séquence complète de phases d'un cycle de vie de tâche..

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
`apt addons:` // facultatif
`cache components:` // facultatif
`before_install:`
`install:`
`before_script:`
`script:`
`before_cache:` // facultatif (si et seulement si la mise en cache est efficace)
`after_success` ou `after_failure`
`before_deploy:` // Facultatif (si et seulement si le déploiement est actif)
`deploy:` // facultatif
`after_deploy:` // facultatif (si et seulement si le déploiement est actif)
`after_script:`
```

<h2 id="personnaliser-la-phase-installation">Personnalisez la phase d'installation.
<a href="#personnaliser-la-phase-installation" class="ancre-titre after"></a></h2>

Les commandes d'installation de dépendance par défaut dépendent du langage du projet choisi. Par exemple, les versions _Java_ utilisent Maven ou Gradle, selon le fichier de build dans le référentiel. Les projets _Ruby_ utilisent Bundler lorsqu'un Gemfile est dans le référentiel.

Installez vos dépendances de projet en spécifiant un script comme suit :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install: ./install-dependencies.sh
```

<div class="notice cadre">
Lorsque vous utilisez des scripts personnalisés, ils doivent être exécutables (par exemple, à l'aide de `chmod +x`) et contenir une ligne Shebang valide telle que <code>/usr/bin/env sh</code>, <code>/usr/bin/env ruby</code>, ou <code>/usr/bin/env python</code>.
</div>

Vous pouvez également fournir plusieurs étapes, par exemple, pour installer les dépendances Ruby et Node et mettre à jour votre fichier comme indiqué ci-dessous :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
    - bundle install --path vendor/bundle
    - npm install
```

Si une étape échoue pendant l'installation, la build s'arrête immédiatement et est marquée comme erronée.

Vous pouvez également utiliser `apt-get`ou `snap` pour installer des dépendances.

<h3 id="sauter-la-phase-installation">Sauter la phase d'installation.
<a href="#sauter-la-phase-installation" class="ancre-titre after"></a></h3>

Ignorez entièrement l'étape d'installation en ajoutant ce qui suit à votre fichier `.travis.yml`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install: skip
```

<h2 id="personnaliser-la-phase-de-build">Personnaliser la phase de build.
<a href="#personnaliser-la-phase-de-build" class="ancre-titre after"></a></h2>

La commande de build par défaut dépend de la langue du projet. Les projets _Ruby_ utilisent `rake`, le dénominateur commun pour la plupart des projets _Ruby_.

Vous pouvez écraser l'étape de build par défaut dans `.travis.yml` comme suit :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script: bundle exec thor build
```

ou, spécifiez plusieurs commandes de script comme indiqué ci-dessous :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script:
    - bundle exec rake build
    - bundle exec rake builddoc
```

Lorsque l'une des commandes de builds renvoie un code de sortie non nul, la build de Travis CI exécute les commandes suivantes et accumule le résultat de la build.

Dans l'exemple ci-dessus, si `bundle exec rake build` renvoie un code de sortie de «1» alors, la commande suivante, `bundle exec rake build doc`, s'exécute toujours, mais la build échouera.

Si votre première étape consiste à exécuter des tests unitaires, suivis des tests d'intégration, vous pouvez toujours voir si les tests d'intégration réussissent lorsque les tests unitaires échouent.

Changez ce comportement en utilisant _shell magic_ pour exécuter toutes les commandes par la suite, mais la build échoue toujours lorsque la première commande renvoie un code de sortie non nul. Voici l'extrait de votre fichier `.travis .yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script: bundle exec rake build && bundle exec rake builddoc
```

L'exemple ci-dessus échoue immédiatement lorsque `bundle exec rake build` échoue. Notez le `&&`.

<h3 id="utiliser-la-commande$?">Utiliser la commande $?
<a href="#utiliser-la-commande$?" class="ancre-titre after"></a></h3>

Chaque commande de `script` est traitée par une fonction de bash spéciale. Cette fonction manipule la commande `$?` pour produire des journaux adaptés à l'affichage. Par conséquent, vous ne devriez pas compter sur la valeur de `$?` dans la section `script` pour modifier le comportement de la build.

Voir ce [problème sur GitHub](https://github.com/travis-ci/travis-ci/issues/3771) pour une discussion plus technique.

<h3 id="commandes-de-build-complexes">Commandes de build complexes
<a href="#commandes-de-build-complexes" class="ancre-titre after"></a></h3>

Si vous avez un environnement de build complexe et difficile à configurer dans le fichier `.travis.yml`, envisagez de déplacer les étapes dans un script shell séparé.

Le script peut faire partie de votre référentiel et peut facilement être appelé à partir du fichier `.travis.yml`.

Considérons un cas dans lequel vous souhaitez exécuter des scénarios de test plus complexes, mais uniquement pour les builds qui ne proviennent pas de _pull requests_. Dans ce cas, un script shell pourrait ressembler à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.bash</div><div class="item droit">Bash</div>
</div>
```bash
#!/bin/bash
set -ev
bundle exec rake:units
if [ "${TRAVIS_PULL_REQUEST}" = "false" ]; then
    bundle exec rake test:integration
fi
```

<div class="notice cadre">
Remarquez le <code>set -ev</code> en haut. L'indicateur `-e` fait quitter le script une fois qu'une commande renvoie un code de sortie non nul. Ceci est pratique si vous voulez qu'un script sorte tôt. Il aide également à des scripts d'installation complexes où une commande échouée ne ferait pas échouer l'installation.
</div>

L'indicateur `-v` fait imprimer au shell toutes les lignes du script avant de les exécuter, ce qui aide à identifier les étapes qui ont échoué.

Suivez ces étapes pour exécuter ce script à partir de votre fichier `.travis.yml` : 

1. Enregistrez-le dans votre référentiel en tant que `scripts/run-tests.sh` ;
2. Rendez-le exécutable au moyen de `chmod ugo+x scripts/run-tests.sh` ;
3. Validez-le dans votre référentiel. ;
4. Ajoutez-le à votre fichier `.travis.yml` : 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
 script: ./scripts/run-tests.sh
```

<h5 id="utilisez-la-commande-de-sortie">Utilisez la commande de sortie.
<a href="#utilisez-la-commande-de-sortie" class="ancre-titre after"></a></h5>

Après avoir précisé les étapes du cycle de vie des tâches, celles-ci sont compilées en un seul script bash et exécutées.

Si vous avez besoin de remplacer les étapes spécifiées, n'utilisez pas la commande intégrée de shell `exit`. Cela risque de terminer le processus de build sans permettre à Travis d'effectuer les tâches postérieures.

En revanche, l'utilisation de la commande `exit` à l'intérieur d'un script personnalisé est sûre. Si une erreur est indiquée, la tâche sera marquée en cas d'échec.

<h2 id="interrompre-la-build">Interrompre la build.
<a href="#interrompre-la-build" class="ancre-titre after"></a></h2>

La build est considérée comme échouée lorsqu'une ou plusieurs de ses tâches se terminent avec un état qui n'est pas validé.

La build est rompue si l'une des commandes dans les quatre premières phases du cycle de vie de la tâche renvoie un code de sortie non nul.

Voici les scénarios de problème de builds courants : 

* Si `before_install`, `install`, ou `before_script` renvoie un code de sortie non nul, la version est erronée et s'arrête immédiatement ;
* Si `script` renvoie un code de sortie non nul, la génération entraîne **l'échec**, mais continue en s'exécutant avant d'être marqué comme un échec.

Le code de sortie de `after_success`, `after_failure`, `after_script`, `after_deploy` et les étapes suivantes n'affectent pas le résultat de la build. Cependant, si l'une de ces étapes expire, la build est marquée comme échouée.

Pour résoudre les problèmes plus courants, consultez notre [documentation des problèmes de construction communs](./problemes-courants-de-construction.md).

<h2 id="deploiement-du-code">Déploiement du code.
<a href="#deploiement-du-code" class="ancre-titre after"></a></h2>

Le déploiement est une phase facultative du cycle de vie d'une tâche. Il est défini en utilisant l'un de nos fournisseurs de déploiement continu pour déployer du code sur Heroku, Amazon ou une autre plate-forme prise en charge.

Si la build est cassée, les étapes de déploiement sont ignorées.

Lors du déploiement de fichiers vers un fournisseur, empêchez Travis CI de réinitialiser votre répertoire de travail et de supprimer toutes les modifications apportées lors de la build (`git stash - all`) en ajoutant une commande `skip_cleanup` à votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
    skip_cleanup: true
```

La phase `before_deploy` vous permet d'exécuter des commandes avant le déploiement. Un code de sortie non nul dans cette phase marquera la construction comme erronée.

Vous pouvez utiliser la phase `after_deploy` pour exécuter toutes les étapes après le déploiement. 

<div class="notice cadre">
Notez que `after_deploy` n'affecte pas l'état de la build. 
</div>

<div class="notice cadre">
<strong>Remarque :</strong> les phases <code>before_deploy</code> et <code>after_deploy</code> s'exécutent avant et après chaque fournisseur de déploiement, ils s'exécutent donc plusieurs fois si de nombreux fournisseurs existent.
</div>
<hr class="invisible">
