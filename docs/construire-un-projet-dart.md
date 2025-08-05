# Construire un projet Dart

<p>
    <div class="tdm">
        <div class="deux-colonnes">
        <ul>
            <li><a href="#tests-versions-dart">Tests par rapport aux versions de Dart</a></li>
            <li><a href="#executer-les-tests"> Exécuter les tests</a></li>
            <li><a href="#autres-taches">Autres tâches</a></li>
            <li><a href="#variables-environnement">Variables d'environnement</a></li>
            <li><a href="#configuration-construction-dart">Référence de configuration de construction</a></li>
        </ul>
        </div>
    </div>
</p>

<div class="tableau-accueil">
    <table class="tableau">
        <thead>
             <tr><th>C#</th><th>DÉFAUT</th></tr>
        </thead>
        <tbody>
            <tr>
                <td><a href="#gestion-des-dependances-c#">Défaut</a><code>install</code></td>
                <td><code> pub get</code></td>
            </tr>
            <tr>
                <td><a href="#script-de-build-par-defaut-c#">Défaut</a><code>script</code></td>
                <td><code>pub run test</code></td>
            </tr>
            <tr>
                <td><a href="#cle-de-matrice-c#">Clé de matrice</a></td>
                <td><code>dart</code>,<code>dart_task</code>,<code>env</code><td>
            </tr>
            <tr>
                <td>Support</td>
                <td><a href="https://travis-ci.community/c/languages/dart">Soutien de la communanuté</a></td>
            </tr>
        </tbody>
        <tfoot>
            <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
            <tr><th colspan="2" text-align="left">Exemple minimal</th></tr>
            <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
            <tr><th colspan="2">
            <div class="titre-code">
                <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
            </div>
```yml
language: dart
```
            </th></tr>
        </tfoot>
    </table>
</div>

Ce guide couvre l'environnement de construction et les sujets de configuration spécifiques aux projets [Dart](https://dart.dev/). Veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de construction](./personnalisation-de-la-construction.md).

<h3 id="soutien-communaute-dart">Avertissement soutenu par la communauté.
<a href="#soutien-communaute-dart" class="ancre-titre after"></a></h3>

Le soutien de Travis CI à Dart est apporté par la communauté et peut être supprimé ou modifié à tout moment. Si vous rencontrez des problèmes, veuillez les signaler dans le [_Travis CI Issue Tracker_](https://github.com/travis-ci/travis-ci/issues/new?labels=community:dart) et CC [@athomas](https://github.com/athomas) et [@ a14n](https://github.com/a14n).

<h2 id="tests-versions-dart">Tests par rapport aux versions de Dart.
<a href="#tests-versions-dart" class="ancre-titre after"></a></h2>

Dart sur Travis CI télécharge et installe les archives SDK Dart. Consultez les [archives de téléchargement Dart](https://www.dartlang.org/install) pour obtenir la liste des archives disponibles. Par défaut, la dernière version stable du SDK est téléchargée. Pour sélectionner explicitement une ou plusieurs versions, utilisez la clé `dart`. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: dart
dart:
# Install the latest stable release
- stable
# Install the latest beta release
- beta
# Install the latest dev release
- dev
# Install a specific stable release - 1.15.0
- "1.15.0"
# Install a specific dev release, using a partial download URL - 2.9.0-2.0.dev
- "dev/release/2.9.0-2.0.dev"
# Install a specific beta release, using a partial download URL - 2.9.0-2.0.beta
- "beta/release/2.9.0-2.0.beta"
```

<h2 id="executer-les-tests">Exécuter les tests.
<a href="#executer-les-tests" class="ancre-titre after"></a></h2>

Si votre paquetage dépend de []`test` paquetage](https://pub.dartlang.org/packages/test), `pub run test` sera exécuté par défaut. Cela ne fait généralement que des tests sur la machine virtuelle Dart, mais vous pouvez la [configurer](https://github.com/dart-lang/test/blob/master/pkgs/test/doc/configuration.md#platforms) pour l'exécuter sur des plates-formes supplémentaires par défaut.

Vous pouvez également personnaliser les arguments que Travis laisse à l'exécuteur de test à l'aide du champ `dart_task` dans `.travis.yml`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: dart
dart_task:
- test: --platform vm
- test: --platform chrome
```

<h3 id="navigateurs-disponibles">Navigateurs disponibles.
<a href="#navigateurs-disponibles" class="ancre-titre after"></a></h3>

Travis est livré avec Firefox et Chrome installés par défaut sur Linux. Cependant, si vous souhaitez exécuter vos tests sur Dartium, vous devrez l'installer en ajoutant`install_dartium: true` soit au niveau supérieur ou pour une tâche particulière.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: dart
dart_task:
- test: --platform vm
- test: --platform dartium
  install_dartium: true
```

<h3 id="xvfb">XVFB.
<a href="#xvfb" class="ancre-titre after"></a></h3>

Sur Linux, l'exécuteur de test utilise [XVFB](https://www.x.org/archive/X11R7.6/doc/man/man1/Xvfb.1.xhtml) par défaut afin qu'il puisse utiliser des navigateurs comme Chrome qui nécessitent un affichage. Cependant, cela peut interférer avec certaines applications, vous pouvez donc le désactiver en définissant `xvfb: false` soit au niveau supérieur, soit pour une tâche particulière.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: dart
dart_task:
- test: --exclude-tags no-xvfb
- test: --tags no-xvfb
    xvfb: false
```

<h2 id="autres-taches">Autres tâches.
<a href="#autres-taches" class="ancre-titre after"></a></h2>

Plusieurs tâches sont disponibles en plus de l'exécution des tests.

<h3 id="analyzer">Analyzer.
<a href="#analyzer" class="ancre-titre after"></a></h3>

Pour exécuter [Dart analyzer](https://github.com/dart-lang/sdk/tree/master/pkg/analyzer_cli#dartanalyzer) pour vérifier que votre code n'a pas d'erreurs statiques, ajoutez une tâche avec `dartanalyzer: true`. Par défaut, il analyse tous les fichiers Dart  dans votre référentiel, mais vous pouvez le configurer en fournissant des arguments au lieu de `true`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: dart
dart_task:
# As long as you don't want any other configuration, you can just use the name
# of a task instead of "name: true".
- test
# Warnings are fatal, but we only analyze the lib/ directory.
- dartanalyzer: --fatal-warnings lib
```

<h3 id="formatter">Formatter.
<a href="#formatter" class="ancre-titre after"></a></h3>

Pour exécuter le [Dart formatter](https://github.com/dart-lang/dart_style#readme) pour vérifier que tous vos fichiers sont correctement formatés, ajoutez une tâche avec `dartfmt: true`. Si votre paquetage dépend de `dart_style`, il utilisera la version de format de ce paquetage ; sinon, il utilisera le `dartfmt` fourni avec votre Dart SDK.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: dart
dart_task:
- test: --platform vm
- test: --platform chrome
- dartfmt
```

<h2 id="variables-environnement">Variables d'environnement.
<a href="#variables-environnement" class="ancre-titre after"></a></h2>

La version de Dart utilisée par une tâche est disponible sous `TRAVIS_DART_VERSION`.
* `TRAVIS_DART_TEST` sera `true` si la tâche actuelle utilise `test` ;
* `TRAVIS_DART_ANALYZE` sera `true` si la tâche actuelle utilise `dartanalyzer` ;
* `TRAVIS_DART_FORMAT` sera `true` si la tâche actuelle utilise `dartfmt`.

<h2 id="configuration-construction-dart">Référence de configuration de construction.
<a href="#configuration-construction-dart" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Dart](https://config.travis-ci.com/ref/language/dart) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<hr class="invisible">