# Construire un projet Groovy

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#presentation">Présentation</a></li>
            <li><a href="#projet-gradle">Projet Gradle</a></li>
            <li><a href="#projet-maven">Projet Maven</a></li>
            <li><a href="#projet-ant">Projet Ant</a></li>
            <li><a href="#test-sur-plusieurs-jdks">Test sur plusieurs JDKs</a></li>
            <li><a href="#reference-de-configuration-de-construction-groovy">Référence de configuration de construction</a></li>
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
        <tr><th>GROOVY</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td>Défaut<code>install</code></td>
            <td><a href="#projet-gradle">Gradle</a>, <a href="#projet-maven">Maven</a>, <a href="#projet-ant">Ant</a></td>
        </tr>
        <tr>
            <td>Défaut<code>script</code></td>
            <td><a href="#test-gradle">Gradle</a>, <a href="#test-maven">Maven</a>, <a href="#test-ant">Ant</a></td>
        </tr>
        <tr>
            <td><a href="#matrice-de-construction">Cle de matrice</a></td>
            <td><code>env</code>,<code>jdk</code><td>
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
language: groovy
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>

Le reste de ce guide traite de la configuration des projets Groovy sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de la construction](./personnalisation-de-la-construction.md).

Les compilations Groovy ne sont pas disponibles sur l'environnement macOS.

<h2 id="presentation">Présentation.
<a href="#presentation" class="ancre-titre after"></a></h2>

L'environnement Travis CI contient diverses versions d'OpenJDK, Oracle JDK, Gradle, Maven et Ant, ainsi que des défauts raisonnables, donc vous n'aurez souvent pas à configurer quoi que ce soit au-delà :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: groovy
```

<h2 id="projet-gradle">Projet Gradle.
<a href="#projet-gradle" class="ancre-titre after"></a></h2>

<h3 id="gestion-des-dependances-gradle">Gestion des dépendances Gradle.
<a href="#gestion-des-dependances-gradle" class="ancre-titre after"></a></h3>

Si votre projet a un fichier `build.gradle` dans la racine du référentiel, Travis CI s'exécute :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
gradle assemble
```

pour installer les dépendances de votre projet.

<h3 id="test-gradle">Commande de test par défaut de Gradle.
<a href="#test-gradle" class="ancre-titre after"></a></h3>

Si votre projet a un fichier `build.gradle` dans la racine du référentiel, Travis CI s'exécute :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
gradle check
```

<h3 id="mise-en-cache-gradle">Mise en cache Gradle.
<a href="#mise-en-cache-gradle" class="ancre-titre after"></a></h3>

Une particularité du cache de dépendances dans Gradle implique que, pour éviter de télécharger le cache après chaque build, vous devez ajouter les lignes suivantes à votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_cache:
  - rm -f $HOME/.gradle/caches/modules-2/modules-2.lock
cache:
  directories:
    - $HOME/.gradle/caches/
    - $HOME/.gradle/wrapper/
```

<h2 id="projet-maven">Projet Maven.
<a href="#projet-maven" class="ancre-titre after"></a></h2>

<h3 id="gestion-des-dependances-maven">Gestion des dépendances Maven.
<a href="#gestion-des-dependances-maven" class="ancre-titre after"></a></h3>

Si votre projet a un fichier `pom.xml` dans la racine du référentiel et n'a pas de `build.gradle`, Travis CI utilise Maven 3 pour installer les dépendances de votre projet :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
mvn install -DskipTests=true -Dmaven.javadoc.skip=true -B -V
```

<h3 id="commande-de-test-maven-par-defaut">Commande de test Maven par défaut.
<a href="#commande-de-test-maven-par-defaut" class="ancre-titre after"></a></h3>

Si votre projet a un fichier `pom.xml` dans la racine du référentiel et n'a pas de `build.gradle`, Travis CI utilise Maven 3 pour exécuter votre script de construction :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
mvn test -B
```

<h2 id="projet-ant">Projet Ant.
<a href="#projet-ant" class="ancre-titre after"></a></h2>

<h3 id="commande-de-test-ant-par-defaut">Commande de test Ant par défaut.
<a href="#commande-de-test-ant-par-defaut" class="ancre-titre after"></a></h3>

Si le projet Groovy n'a pas de fichiers de configuration Gradle ou Maven, Travis CI utilise Ant pour créer votre projet :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
ant test
```

<h3 id="gestion-des-dependances-ant">Gestion des dépendances Ant.
<a href="#gestion-des-dependances-ant" class="ancre-titre after"></a></h3>

Comme il n'existe pas de méthode standard unique pour installer les dépendances d'un projet avec Ant, vous devez spécifier une commande personnalisée à l'aide de la clé `install:` dans votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: groovy
install: ant deps
```

<h2 id="test-sur-plusieurs-jdks">Test sur plusieurs JDKs.
<a href="#test-sur-plusieurs-jdks" class="ancre-titre after"></a></h2>

Pour tester plusieurs JDKs, utilisez la clé `jdk:` dans `.travis.yml`. Par exemple, pour tester ensemble Oracle JDK 8 et OpenJDK 7 :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jdk:
  - oraclejdk8
  - openjdk7
```

<h3 id="utiliser-java10-et-ulterieur">Utilisez Java 10 et ultérieur.
<a href="#utiliser-java10-et-ulterieur" class="ancre-titre after"></a></h3>

Pour tester avec OpenJDK et OracleJDK 10 et plus, voir la [documentation Java](https://docs.travis-ci.com/user/languages/java/#using-java-10-and-later).

<h2 id="reference-de-configuration-de-construction-groovy">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-groovy" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Groovy](https://config.travis-ci.com/ref/language/groovy) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<hr class="invisible">

