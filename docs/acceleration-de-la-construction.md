# Accélération de la construction

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#paralleliser-sur-des-machines-virtuelles">Paralléliser les builds sur des machines virtuelles</a></li>
            <li><a href="#paralleliser-sur-une-seule-machine-virtuelle">Paralléliser une build sur une seule machine virtuelle</a></li>
            <li><a href="#nombreuses-parallelisations">Paralléliser RSPEC, Cucumber et Minitest sur plusieurs machines virtuelles</a></li>
            <li><a href="#mettre-en-cache-les-dependances">Mettre en cache les dépendances</a></li>
            <li><a href="#accelerer-une-build">Des moyens spécifiques à l'environnement pour accélérer une build</a></li>
        </ul>
    </div>
</div>
</p>

Travis CI met en œuvre quelques optimisations qui aident à accélérer votre build, comme le système de fichiers en mémoire pour les fichiers de BDD, mais il existe une gamme de choses qui peuvent être faites pour améliorer encore plus les temps de build.

<h2 id="paralleliser-sur-des-machines-virtuelles">Paralléliser les builds sur des machines virtuelles.
<a href="#paralleliser-sur-des-machines-virtuelles" class="ancre-titre after"></a></h2>

Pour accélérer une suite de tests, vous pouvez la diviser en plusieurs parties en utilisant la fonction de build de matrice de Travis CI.

Supposons que vous souhaitiez diviser vos tests unitaires et vos tests d'intégration en deux travaux de build différents. Ils fonctionneront en parallèle et utiliseront pleinement la capacité de build disponible pour votre compte.

Voici un exemple de la façon d'utiliser cette fonctionnalité dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
env:
    - TEST_SUITE=units
    - TEST_SUITE=integration
```

Ensuite, vous modifiez votre commande de script pour utiliser la nouvelle variable d'environnement pour déterminer le script à exécuter.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
script: "bundle exec rake test:$TEST_SUITE"
```

Travis CI déterminera la build de matrice en fonction des variables d'environnement et planifiera deux builds à exécuter.

L'avantage de cette configuration est que la suite de tests unitaires est généralement réalisée avant la suite de tests d'intégration, ce qui vous permet d'avoir un retour visuel plus rapide sur la couverture des tests de base.

En fonction de la taille et de la complexité de votre suite de tests, vous pouvez la diviser encore plus. Vous pouvez séparer les différentes préoccupations pour les tests d'intégration dans différents sous-dossiers et les exécuter dans des étapes distinctes d'une build de matrice.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
env:
    - TESTFOLDER=integration/user
    - TESTFOLDER=integration/shopping_cart
    - TESTFOLDER=integration/payments
    - TESTFOLDER=units
```

Ensuite, vous pouvez ajuster votre commande de script pour exécuter rspec pour chaque sous-dossier : 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
script: "bundle exec rspec $TESTFOLDER"
```

Par exemple, le projet Rails utilise la fonction de build de matrice pour créer des tâches séparées pour chaque base de données à tester et également pour diviser les tests par préoccupation. Un ensemble exécute les tests uniquement pour les _railties_, un autre pour _actionpack_, _actionmailer_, _activesupport_ et tout un tas d'ensembles exécutant les tests _activerecord_ sur plusieurs bases de données. Voir leur [fichier .travis.yml](https://github.com/rails/rails/blob/master/.travis.yml) pour plus d'exemples.

<h2 id="paralleliser-sur-une-seule-machine-virtuelle">Paralléliser une build sur une seule machine virtuelle.
<a href="#paralleliser-sur-une-seule-machine-virtuelle" class="ancre-titre after"></a></h2>

La parallélisation de la suite de tests sur une machine virtuelle dépend du langage et du programme d'exécution des tests :

* Pour Ruby et RSpec, utilisez la gemme [parallel_tests](https://github.com/grosser/parallel_tests) ;
* Pour Java, utilisez la fonction intégrée [pour exécuter des tests en parallèle à l'aide de JUnit](http://incodewetrustinc.blogspot.com/2009/07/run-your-junit-tests-in-parallel-with.html).

Pour vous donner une idée de l'accélération dont nous parlons, j'ai essayé d'exécuter des tests en parallèle sur `travis-core` et j'ai pu constater une baisse de 26 minutes à environ 19 minutes sur 4 tâches.

<h2 id="nombreuses-parallelisations">Parallélisez RSpec, Cucumber et Minitest sur plusieurs machines virtuelles.
<a href="#nombreuses-parallelisations" class="ancre-titre after"></a></h2>

Si vous souhaitez effectuer des tests parallèles pour RSpec, Cucumber ou MinItest sur plusieurs machines virtuelles pour obtenir des retours plus rapides d'IC, vous pouvez essayer le _gem_ [knapsack](https://github.com/ArturT/knapsack). Il divisera les tests sur des machines virtuelles et s'assurera que les tests exécuteront un temps comparable sur chaque machine virtuelle (chaque travail prendra du temps similaire). Vous pouvez utiliser notre fonctionnalité _matrix_ pour configurer _knapsack_.

<h3 id="exemple-de-parallelisation-rspec">Exemple de parallélisation RSpec.
<a href="#exemple-de-parallelisation-rspec" class="ancre-titre after"></a></h3>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
script: "bundle exec rake knapsack:rspec"
env:
    global:
      - MY_GLOBAL_VAR=123
      -  CI_NODE_TOTAL=2
    jobs:
      - CI_NODE_INDEX=0
      - CI_NODE_INDEX=1
```

Une telle configuration générera une matrice avec les 2 lignes ENV suivantes :

```console
MY_GLOBAL_VAR=123 CI_NODE_TOTAL=2 CI_NODE_INDEX=0
MY_GLOBAL_VAR=123 CI_NODE_TOTAL=2 CI_NODE_INDEX=1
```

<h3 id="exemple-de-parallelisation-cucumber">Exemple de parallélisation Cucumber.
<a href="#exemple-de-parallelisation-cucumber" class="ancre-titre after"></a></h3>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
script: "bundle exec rake knapsack:cucumber"
env:
    global:
      - CI_NODE_TOTAL=2
    jobs:
      - CI_NODE_INDEX=0
      - CI_NODE_INDEX=1
```

<h3 id="exemple-de-parallelisation-minitest">Exemple de parallélisation Minitest.
<a href="#exemple-de-parallelisation-minitest" class="ancre-titre after"></a></h3>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
script: "bundle exec rake knapsack:minitest"
env:
    global:
      - CI_NODE_TOTAL=2
    jobs:
      - CI_NODE_INDEX=0
      - CI_NODE_INDEX=1
```

<h3 id="exemple-toutes-parallelisation">Exemple de parallélisation RSpec, Cucumber et Minitest.
<a href="#exemple-toutes-parallelisation" class="ancre-titre after"></a></h3>

Si vous souhaitez paralléliser une suite de tests pour RSPEC, Cucumber et MinItest en même temps, définissez le script dans `.travis.yml` comme suit :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
script:
  - "bundle exec rake knapsack:rspec"
  - "bundle exec rake knapsack:cucumber"
  - "bundle exec rake knapsack:minitest"
```

Vous pouvez trouver plus d'exemples dans les [documents de knapsack](https://github.com/ArturT/knapsack#info-for-travis-users).

<h2 id="mettre-en-cache-les-dependances">Mise en cache des dépendances.
<a href="#mettre-en-cache-les-dependances" class="ancre-titre after"></a></h2>
 
L'installation de dépendance peut prendre un certain temps pour des projets plus importants. Pour accélérer le processus, vous pouvez essayer de mettre en cache les dépendances.

Vous pouvez soit utiliser notre mise en cache intégrée, soit déployer la vôtre sur S3. Si vous voulez utiliser votre propre cache et que vous utilisez Ruby avec Bundler, jetez un coup d'œil à l'excellent projet WAD. Pour les autres langages, vous pouvez utiliser les outils S3 directement pour envoyer et recevoir les dépendances.

<h2 id="accelerer-une-build">Des moyens spécifiques à l'environnement pour accélérer une build.
<a href="#accelerer-une-build" class="ancre-titre after"></a></h2>

En plus des optimisations implémentées par Travis, il existe également plusieurs moyens spécifiques à l'environnement que vous puissiez envisager pour augmenter la vitesse de vos tests.

<h3 id="optimisation-php">PHP Optimisations.
<a href="#optimisation-php" class="ancre-titre after"></a></h3>

Les images PHP VM sur Travis CI fournissent plusieurs versions PHP qui incluent XDebug. L'extension XDebug est utile si vous souhaitez générer des rapports de couverture de code dans vos builds Travis, mais il a été démontré qu'il a un effet négatif sur les performances.

Vous souhaiteriez peut-être envisager de [désactiver l'extension PHP XDebug](./php#desactiver-extension-phpxdebug.md) pour vos versions si : 

* vous ne générez pas de rapports de couverture de code dans vos tests Travis ;
* ou vous testez sur PHP 7.0 ou plus et pouvez utiliser le débogueur [PHP Debugger (phpdbg)](https://github.com/krakjoe/phpdbg) qui peut être plus rapide.

<h5 id="exemple-phpdbg">Exemple phpdbg.
<a href="#exemple-phpdbg" class="ancre-titre after"></a></h5>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
before_script:
  - phpenv config-rm xdebug.ini
  - composer install
script:
  - phpdbg -qrr phpunit
```

<h3 id="optimisation-makefile">Optimisation du Makefile.
<a href="#optimisation-makefile" class="ancre-titre after"></a></h3>

Si votre construction de makefile se compose de pièces indépendantes qui peuvent être parallélisées en toute sécurité, vous pouvez [exécuter plusieurs recettes simultanément](https://www.gnu.org/software/make/manual/html_node/Parallel.html). Consultez la section [environnements de virtualisation](./reference.md) pour déterminer le nombre de CPU qu'un environnement possède normalement et fixez le paramètre du job `make` à un nombre similaire (ou légèrement plus élevé si votre build attend fréquemment des E/S sur disque).

<div class="notice cadre">
Notez que cette opération entraînera l'entrelacement des sorties de recettes simultanées.
</div>

<h5 id="parallelisation-makefile">Exemple de parallélisation de Makefile.
<a href="#parallelisation-makefile" class="ancre-titre after"></a></h5>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
env:
    global:
      - MAKEFLAGS="-j 2"
```
<hr class="invisible">