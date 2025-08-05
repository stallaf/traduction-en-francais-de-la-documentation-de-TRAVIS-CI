# Construire un projet Clojure

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#environnement-ci-pour-les-projets-clojure">Environnement CI pour les projets Clojure</a></li>
            <li><a href="#gestion-des-dependances-clojure">Gestion des dépendances</a></li>
            <li><a href="#script-de-build">Script de build</a></li>
            <li><a href="#utilisation-de-leiningen">Utilisation de Leiningen 1</a></li>
            <li><a href="#reference-de-configuration-de-construction">Référence de configuration de construction</a></li>
            <li><a href="#test-avec-plusieurs-jdk">Test avec plusieurs JDK</a></li>
            <li><a href="#test-avec-plusieurs-clojure">Test avec plusieurs versions de Clojure</a></li>
            <li><a href="#exemples">Exemples</a></li>
        </ul>
    </div>
</div>
</p>

<div class="deux-colonnes">
    <div class="notice cadre">
    Pour les versions de langage et d'autres informations spécifiques à l'environnement de compilation, consultez nos pages de référence :
    <p>
    <ul>
        <li><a href="environnement-precise.md">Precise</a> ;</li>
        <li><a href="environnement-trusty.md">Trusty</a> ;</li>
        <li><a href="environnement-xenial.md">Xenial</a> ;</li>
        <li><a href="environnement-bionic.md">Bionic</a> ;</li>
        <li><a href="environnement-focal.md">Focal</a> ;</li>
        <li><a href="environnement-jammy.md">Jammy</a> ;</li>
        <li><a href="environnement-noble.md">Noble</a> ;</li>
    </ul>
    </p>
    </div>

<p>
<table class="tableau">
    <thead>
        <tr><th>Clojure</th><th>Défaut</th></tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="#gestion-des-dependances">Défaut</a><code>install</code></td>
            <td><code>lein deps</code></td>
        </tr>
        <tr>
            <td><a href="#script-de-build-par-defaut">Défaut</a><code>script</code></td>
            <td><code>leintest</code></td>
        </tr>
        <tr>
            <td><a href="#matrice-de-construction">Cle de matrice</a></td>
            <td><code>env</code>,<code>lein</code>,<code>jdk</code></td>
        </tr>
        <tr>
            <td>Support</td>
            <td><a href="mailtosupport@travis-ci.com">Travis CI</a></td>
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
language: clojure
```
        </th></tr>
     </tfoot>
</table>
</p>
</div>

Ce guide traite des sujets liés à l'environnement de construction et à la configuration spécifiques aux projets Clojure. Veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de la construction](./personnalisation-de-la-construction.md).

Les constructions Clojure ne sont pas disponibles pour l'environnement macOS.

<h2 id="environnement-ci-pour-les-projets-clojure">Environnement CI pour les projets Clojure.
<a href="#environnement-ci-pour-les-projets-clojure" class="ancre-titre after"></a></h2>

L'environnement Travis CI fournit un grand ensemble d'outils de construction pour les langues JVM avec [plusieurs JDK, Ant, Gradle, Maven](./langage-java.md) et [Leiningen](http://leiningen.org/) 1.7.x et 2.4.x (par défaut).

<h2 id="gestion-des-dependances-clojure">Gestion des dépendances.
<a href="#gestion-des-dependances-clojure" class="ancre-titre after"></a></h2>

Si vous utilisez Leiningen, il installera automatiquement toutes les dépendances répertoriées dans le fichier `project.clj`.

<h3 id="etape-installation-alternative">Étape d'installation alternative
<a href="#etape-installation-alternative" class="ancre-titre after"></a></h3>

Si vous devez installer d'autres dépendances avant que vos tests puissent fonctionner, vous devez configurer le bon `:hook` dans `project.clj`.

Si pour une raison quelconque, vous ne pouvez pas utiliser les _hooks_, vous pouvez remplacer l'étape d'installation dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install: lein protobuf install
```

Voir le guide de [configuration de construction](./personnalisation-de-la-construction.md) pour en savoir plus.

<h2 id="script-de-build">Script de build.
<a href="#script-de-build" class="ancre-titre after"></a></h2>
 
<h3 id="utiliser-midje">Utiliser MidJe.
<a href="#utiliser-midje" class="ancre-titre after"></a></h3>

Si votre projet utilise [Midje](https://github.com/marick/Midje), assurez-vous que [lein-midje](https://github.com/marick/Midje/wiki/Lein-midje) figure dans la liste des dépendances de développement de votre fichier `project.clj` et remplacez `script :` dans `.travis.yml` pour exécuter la tâche Midje :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script: lein midje
```

Pour Leiningen 1 Ajouter `:dev-dependencies` à `project.clj` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.project.clj</div><div class="item droit">YAML</div>
</div>
```yml
:dev-dependencies [[midje "1.4.0"]
                   [lein-midje "1.0.10"]])
```

Leiningen 2 remplace `:dev-dependencies` par des profils :

<div class="titre-code">
    <div class="item gauche">&nbsp;.project.clj</div><div class="item droit">YAML</div>
</div>
```yml
:profiles {:dev {:dependencies [[midje "1.6.3"]]
                 :plugins [[lein-midje "3.0.0"]]}}
```

Veuillez noter que pour les projets qui ne prennent en charge que les versions Clojure 1.3.0 et ultérieures, vous devrez peut-être exclure `org.clojure/clojure`pour Midje dans project.clj :

<div class="titre-code">
    <div class="item gauche">&nbsp;.project.clj</div><div class="item droit">YAML</div>
</div>
```yml
:dev-dependencies [[midje "1.4.0" :exclusions [org.clojure/clojure]]
                   [lein-midje "1.0.10"]])
```

Pour des exemples du monde réel, voir [Knockbox](https://github.com/reiddraper/knockbox)
.

<h3 id="speclj">Utilisez Speclj sur Travis CI.
<a href="#speclj" class="ancre-titre after"></a></h3>

Si votre projet utilise [Speclj](https://github.com/slagyr/speclj), assurez-vous qu'il est répertorié dans vos dépendances de développement dans `project.clj` et incluez le dans la ligne  `script:` de votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script: lein spec
```

Pour Leiningen 1, Speclj doit être répertorié sous `:dev-dependencies` dans `project.clj` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.project.clj</div><div class="item droit">YAML</div>
</div>
```yml
:dev-dependencies [[speclj "3.3.1"]]
```

Leiningen 2 remplace `:dev-dependencies` par des profils :

<div class="titre-code">
    <div class="item gauche">&nbsp;.project.clj</div><div class="item droit">YAML</div>
</div>
```yml
:profiles {:dev {:dependencies [[speclj "3.3.1"]]}}
```

<h2 id="utilisation-de-leiningen">Utilisation de Leiningen 1.
<a href="#utilisation-de-leiningen" class="ancre-titre after"></a></h2>

Leiningen 1 est fourni en parallèle avec la version 2.4.x. Pour l'utiliser, spécifiez la clé `lein` dans `.travis.yml`  :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
lein: lein1
```

Dans le cas où vous avez besoin d'utiliser `lein` binaire dans les commandes `before_script`, `install:`,` script:` ainsi de suite, utilisez `lein1` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - lein1 bootstrap
```

<h2 id="reference-de-configuration-de-construction">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Clojure](./construire-un-projet-clojure.md) dans notre [référence de configuration Travis CI Build](https://config.travis-ci.com/).

Le chaînage de tâches nécessite l'utilisation de la tâche `do` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script: lein1 do javac, test
```

<h2 id="test-avec-plusieurs-jdk">Test avec plusieurs JDK.
<a href="#test-avec-plusieurs-jdk" class="ancre-titre after"></a></h2>

Comme pour tout langage JVM, il est également possible d'effectuer des tests sur plusieurs JDK.

<h3 id="exemples-jdk">Exemples.
<a href="#exemples-jdk" class="ancre-titre after"></a></h3>

* [Monger](https://github.com/michaelklishin/monger/blob/master/.travis.yml) ;
* [Welle](https://github.com/michaelklishin/welle/blob/master/.travis.yml) ;
* [Langohr](https://github.com/michaelklishin/langohr/blob/master/.travis.yml) ;
* [Neocons](https://github.com/michaelklishin/neocons/blob/master/.travis.yml).

<h2 id="test-avec-plusieurs-clojure">Test avec plusieurs versions de Clojure.
<a href="#test-avec-plusieurs-clojure" class="ancre-titre after"></a></h2>

<h3 id="tester-avec-leiningen1">Tester avec Leiningen 1.
<a href="#tester-avec-leiningen1" class="ancre-titre after"></a></h3>

Leiningen a un excellent plugin appelé [lein-multi](https://github.com/maravillas/lein-multi) qui vous permet de tester sans effort sur plusieurs versions de Clojure (par exemple, 1.3, 1.4 et alphas/bêtas/instantanés de la version de développement la plus récente). Étant donné que Leiningen peut exécuter des tests avec n'importe quelle version de Clojure (pas nécessairement la même version que celle utilisée par Leiningen lui-même), il n'y a pas besoin de commutateurs d'exécution (comme RVM) pour Clojure.

Pour utiliser lein-multi sur Travis CI, ajoutez-le à `:plugins` dans `project.clj` (remarque : cette fonctionnalité n'est disponible qu'à partir de Leiningen 1.7.0) et remplacez `script:` pour exécuter `lein multi test` au lieu de `lein test` par défaut :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: clojure
script: lein1 multi test
```

Pour un exemple du monde réel, voir [Monger](https://github.com/michaelklishin/monger).

<h3 id="tester-avec-leiningen2">Tester avec Leiningen 2.
<a href="#tester-avec-leiningen2" class="ancre-titre after"></a></h3>

Leiningen 2 a une caractéristique centrale qui remplace `lein-multi`  []:Profils](https://github.com/technomancy/leiningen/blob/master/doc/TUTORIAL.md). Pour exécuter vos tests avec plusieurs profils (et donc, plusieurs ensembles de dépendances ou de versions Clojure), utilisez la commande `lein with-profile` comme ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
lein: lein
script: lein with-profile dev:1.4 test
```

où `dev:1.4` est une liste séparée par des deux-points des profils sur lesquels exécuter la tâche `test`. Utilisez `lein profiles` pour répertorier les profils de votre projet et `lein help with-profile` pour en savoir plus sur la tâche `with-profiles`.

<h3 id="tester-versions-recentes-leiningen">Tester avec les versions récentes de Leiningen.
<a href="#tester-versions-recentes-leiningen" class="ancre-titre after"></a></h3>

Si votre projet Clojure nécessite une version plus récente de Leiningen, vous pouvez le spécifier avec :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: clojure
lein: 2.6.1 # version 2 and up
```

La tâche installera la version spécifiée de Leiningen si elle n'est pas préinstallée et passe à l'installation des dépendances de votre projet. 

<div class="notice cadre">
<strong>Remarque :</strong> Pour un exemple du monde réel, voir <a href="https://github.com/michaelklishin/neocons">Néocons</a>.
</div>

<h2 id="exemples">Exemples.
<a href="#exemples" class="ancre-titre after"></a></h2>

* [monger’s .travis.yml](https://github.com/michaelklishin/monger/blob/master/.travis.yml) ;
* [welle’s .travis.yml](https://github.com/michaelklishin/welle/blob/master/.travis.yml) ;
* [langohr’s .travis.yml](https://github.com/michaelklishin/langohr/blob/master/.travis.yml) ;
* [neocons’ .travis.yml](https://github.com/michaelklishin/neocons/blob/master/.travis.yml) ;
* [Knockbox’s .travis.yml](https://github.com/reiddraper/knockbox/blob/master/.travis.yml) ;
* [Sumo’s .travis.yml](https://github.com/reiddraper/sumo/blob/master/.travis.yml).
<hr class="invisible">
