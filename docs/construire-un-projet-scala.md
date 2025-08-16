# Construire un projet Scala

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#presentation-scala">Présentation</a></li>
            <li><a href="#specifier-versions-scala">Spécifier les versions Scala</a></li>
            <li><a href="#sbt">Projets sbt</a></li>
            <li><a href="#gradle">Projets Gradle, Maven ou Ant</a></li>
            <li><a href="#jdks-scala">Tester avec plusieurs JDKs</a></li>
            <li><a href="#variables-environnement-scala">Variable d'environnement</a></li>
            <li><a href="#reference-de-configuration-de-construction-scala">Référence de configuration de construction</a></li>
            <li><a href="#exemples-scala">Exemples</a></li>
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
    <li><a href="environnement-noble.md">Noble</a>.</li>
  </ul>
  </p>
  </div>

  <p>
  <table class="tableau">
      <thead>
          <tr><th>SCALA</th><th>DÉFAUT</th></tr>
      </thead>
      <tbody>
          <tr>
              <td>Défaut</a><code>install</code></td>
              <td><a href="#gestion-des-dependances-sbt">sbt</a>, <a href="#gestion-des-dependances-gradle">Gradle</a>, <a href="#gestion-des-dependances-maven">Maven</a>, <a href="#gestion-des-dependances-ant">Ant</a></td>
          </tr>
          <tr>
              <td>Défaut<code>script</code></td>
              <td><a href="#script-sbt-par-defaut">sbt</a>, <a href="#script-gradle-par-defaut">Gradle</a>, <a href="#script-maven-par-defaut">Maven</a>, <a href="#script-ant-par-defaut">Ant</a></td>
          </tr>
          <tr>
              <td>Cle de matrice</a></td>
              <td><code>scala</code> ,<code>jdk</code> ,<code>env</code><td>
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
language: scala
```
          </th></tr>
      </tfoot>
  </table>
  </p>
  </div>

Les versions Scala ne sont pas disponibles sur l'environnement macOS.

Ce guide traite de la configuration des projets Rust sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.

<h2 id="presentation-scala">Présentation.
<a href="#presentation-scala" class="ancre-titre after"></a></h2>

L'environnement Travis CI fournit un large ensemble d'outils de construction pour les langues JVM avec [plusieurs JDK, Ant, Gradle, Maven et sbt](https://docs.travis-ci.com/user/languages/java/#overview).

<h2 id="specifier-versions-scala">Spécifier les versions Scala.
<a href="#specifier-versions-scala" class="ancre-titre after"></a></h2>

Pour spécifier les versions Scala dans votre construction :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: scala
scala:
  - 2.9.3
  - 2.10.6
  - 2.11.11
  - 2.12.2
```

Sur Ubuntu Precise, pour utiliser Scala 2.12.x, vous devez activer Oracle JDK 8 en ajoutant `jdk: oraclejdk8` à votre `.travis.yml`.

<h2 id="sbt">Projets sbt.
<a href="#sbt" class="ancre-titre after"></a></h2>

Si votre projet dispose d'un répertoire `project` ou d'un fichier `build.sbt` dans la racine du référentiel, le Travis CI utilise [sbt] pour le construire.

Grâce à [paulp/sbt-extras](https://github.com/paulp/sbt-extras) , la version sbt de votre projet est détectée et utilisée dynamiquement.

<h3 id="dependance-sbt">Gestion de la dépendance sbt.
<a href="#dependance-sbt" class="ancre-titre after"></a></h3>

Travis CI abrite automatiquement les dépendances sbt avant d'exécuter des tests pendant la phase `script` de votre construction.

<h3 id="script-sbt">Commande de script sbt par défaut.
<a href="#script-sbt" class="ancre-titre after"></a></h3>

La commande de script par défaut est :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
sbt ++$TRAVIS_SCALA_VERSION test
```

pour exécuter votre suite de test.

Pour utiliser une commande `script` différente, personnalisez [l'étape de construction](./cycle-de-vie-de-job.md/#personnaliser-la-phase-de-build).

<h3 id="arguments-sbt">Arguments SBT personnalisés.
<a href="#arguments-sbt" class="ancre-titre after"></a></h3>

Vous pouvez remplacer [les options sbt et JVM](https://github.com/paulp/sbt-extras#sbt--h) en transmettant des arguments supplémentaires à `sbt`.

Par exemple, pour exécuter `compile` et `test` avec différents paramètres JVM :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script:
  - sbt -jvm-opts travis/jvmopts.compile ... compile
  - sbt -jvm-opts travis/jvmopts.test ... test
```

Vous pouvez également spécifier des [arguments supplémentaires](https://github.com/paulp/sbt-extras#sbt--h) à transmettre au script de construction par défaut avec la clé `sbt_args` dans votre `.travis.yml`.

Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml;</div><div class="item droit">YAML</div>
</div>
```yml
sbt_args: -no-colors -J-Xss2m
```

générera :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
script: sbt -no-colors -J-Xss2m ++$TRAVIS_SCALA_VERSION test
```

<h2 id="gradle">Projets Gradle, Maven ou Ant.
<a href="#gradle" class="ancre-titre after"></a></h2>

Si votre projet n'est pas configuré pour sbt, le processus de construction se comporte comme un [projet Java](./construire-un-projet-java.md) typique.

<h2 id="jdks-scala">Tester avec plusieurs JDKs.
<a href="#jdks-scala" class="ancre-titre after"></a></h2>

Comme pour tout langage JVM, il est également possible d'effectuer des [tests sur plusieurs JDKs](./construire-un-projet-java.md).

<h3 id="java10">Utilisez Java 10 et plus .
<a href="#java10" class="ancre-titre after"></a></h3>

Pour tester avec Open JDK et OracleJDK 10 et plus, voir la [documentation Java](./construire-un-projet-java.md/#java10-et-plus).

<h2 id="variables-environnement-scala">Variable d'environnement.
<a href="#variables-environnement-scala" class="ancre-titre after"></a></h2>

La version de Scala utilisée par une tâche est disponible sous :

```console
TRAVIS_SCALA_VERSION
```

<h2 id="reference-de-configuration-de-construction-scala">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-scala" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Scala](https://config.travis-ci.com/ref/language/scala) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="exemples-scala">Exemples.
<a href="#exemples-scala" class="ancre-titre after"></a></h2>

* [twitter/scalding](https://github.com/twitter/scalding/blob/master/.travis.yml) ;
* [twitter/summingbird](https://github.com/twitter/summingbird/blob/master/.travis.yml) ;
* [novus/salat](https://github.com/novus/salat/blob/master/.travis.yml) ;
* [scalaz/scalaz](https://github.com/scalaz/scalaz/blob/scalaz-seven/.travis.yml) ;
* [spray/spray](https://github.com/spray/spray/blob/master/.travis.yml) (using a custom [`.jvmopts`](https://github.com/spray/spray/blob/master/.jvmopts) to override Travis CI defaults).
<div class="invisible">
