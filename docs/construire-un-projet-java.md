# Construire un projet Java

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#presentation">Présentation</a></li>
            <li><a href="#projet-maven">Projet Maven</a></li>
            <li><a href="#projet-gradle">Projet Gradle</a></li>
            <li><a href="#projet-ant">Projet Ant</a></li>
            <li><a href="#java10-et-plus">Utilisation de Java 10 et versions ultérieures</a></li>
            <li><a href="#fournisseurs-jdk">Fournisseurs JDK actuels</a></li>
            <li><a href="#exemples"> Exemples</a></li>
            <li><a href="#references-de-configuration-de-construction-java"> Référence de configuration de construction</a></li>
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
    <li><a href="environnement-macos.md">macOS</a> ;</li>
    <li><a href="environnement-freebsd.md">FreeBSD</a> ;</li>
  </ul>
  </p>
  </div>

<p>
<table class="tableau">
    <thead>
        <tr><th>JAVA</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td>Défaut<code>install</code></td>
            <td><code>cabal install --only-</code><br><code>dependencies --enable-tests</code></td>
        </tr>
        <tr>
            <td>Défaut<code>script</code></td>
            <td><code> 	cabal configure --enable-tests</code><br><code>&& cabal build</code><br><code>&& cabal test</code></td>
        </tr>
        <tr>
            <td>Cle de matrice</a></td>
            <td><code>env</code>,<code>ghc</code><td>
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
language: haskell
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>

Ce guide traite de la configuration des projets Java sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de la construction](./personnalisation-de-la-construction.md).

<h2 id="presentation">Présentation.
<a href="#presentation" class="ancre-titre after"></a></h2>

L'environnement Travis CI contient diverses versions d'OpenJDK, Gradle, Maven et Ant.

Pour utiliser l'environnement Java, ajoutez ce qui suit à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: java
```

<h2 id="projet-maven">Projet Maven.
<a href="#projet-maven" class="ancre-titre after"></a></h2>

<h3 id="gestion-des-dependances-maven">Gestion des dépendances Maven.
<a href="#gestion-des-dependances-maven" class="ancre-titre after"></a></h3>

Avant d'exécuter la construction, Travis CI installe les dépendances :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
mvn install -DskipTests=true -Dmaven.javadoc.skip=true -B -V
```

ou si votre projet utilise le script de wrapper `mvnw` :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
./mvnw install -DskipTests=true -Dmaven.javadoc.skip=true -B -V
```

<div class="notice cadre">
Notez que le cycle de vie de la construction de Travis CI et le cycle de vie Maven Build utilisent une terminologie similaire pour différentes phases de construction. Par exemple, <code>install</code> dans une construction Travis CI arrive beaucoup plus tôt que <code>install</code> dans le cycle de vie de la construction Maven. Plus de détails peuvent être trouvés sur le <a href="./cycle-de-vie-de-job.md">cycle de vie de Travis Build</a> et le <a href="https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html">cycle de vie Maven Build</a>.
</div>

<h3 id="commande-de-script-maven">Commande de script par défaut Maven
<a href="#commande-de-script-maven" class="ancre-titre after"></a></h3>

Si votre projet a un fichier `pom.xml` dans la racine du référentiel mais pas de `build.gradle`, Travis CI construit votre projet avec Maven 3 :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
mvn test -B
```

Si votre projet inclut également le script de wrapper `mvnw` dans la racine du référentiel, Travis CI l'utilise à la place :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
./mvnw test -B 
```

<div class="notice cadre">
La commande par défaut ne génère pas Javadoc (<code>-Dmaven.javadoc.skip=true</code>).
</div>

Pour utiliser une commande de script différente, personnalisez l'étape de construction.

<h2 id="projet-gradle">Projet Gradle.
<a href="#projet-gradle" class="ancre-titre after"></a></h2>

<h3 id="gestion-des-dependances-gradle">Gestion des dépendances Gradle.
<a href="#gestion-des-dependances-gradle" class="ancre-titre after"></a></h3>

Avant d'exécuter la construction, Travis CI installe les dépendances :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
gradle assemble
```

ou

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
./gradlew assemble
```

Pour utiliser une commande d'installation différente, personnalisez l'étape d'installation.

<h3 id="commande-de-script-gradle">Commande de script par défaut Gradle.
<a href="#commande-de-script-gradle" class="ancre-titre after"></a></h3>

Si votre projet contient un fichier `build.gradle` dans la racine du référentiel, Travis CI construit votre projet avec Gradle :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
gradle check
```

Si votre projet comprend également le script de wrapper `gradlew` dans la racine du référentiel, Travis CI utilise à la place ce wrapper :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
./gradlew check
```

Pour utiliser une commande de script différente, personnalisez l'étape de construction.

<h3 id="cache">Mise en cache.
<a href="#cache" class="ancre-titre after"></a></h3>

Une particularité du cache de dépendances dans Gradle implique que, pour éviter de télécharger le cache après chaque build, vous devez ajouter les lignes suivantes à votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_cache:
  - rm -f  $HOME/.gradle/caches/modules-2/modules-2.lock
  - rm -fr $HOME/.gradle/caches/*/plugin-resolution/
cache:
  directories:
    - $HOME/.gradle/caches/
    - $HOME/.gradle/wrapper/
```

<div class="notice cadre">
Notez que si vous utilisez Gradle avec <code>sudo</code>> (c'est-à-dire <code>sudo ./gradlew assemble</code>), la configuration de mise en cache ci-dessus n'aura aucun effet, car les profondeurs seront dans <code>/root/.gradle</code> auxquelles le compte d'utilisateur <code>travis</code> n'a pas accès en écriture.
</div>

<h2 id="projet-ant">Projet Ant.
<a href="#projet-ant" class="ancre-titre after"></a></h2>

<h3 id="gestion-des-dependances-ant">Gestion des dépendances Ant.
<a href="#gestion-des-dependances-ant" class="ancre-titre after"></a></h3>

Parce qu'il n'y a pas de moyen standard d'installation de dépendances de projet avec Ant, vous devez spécifier la commande exacte en utilsant la clé `install:` dans votre `.travis.yml`, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: java
install: ant deps
```

<h3 id="commande-de-script-ant">Commande de script par défaut Ant.
<a href="#commande-de-script-ant" class="ancre-titre after"></a></h3>

Si Travis CI ne détecte pas les fichiers Maven ou Gradle, il exécute Ant :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
ant test
```

Pour utiliser une commande `script` différente, personnalisez l'étape de construction.

<h3 id="ant-avec-xenial">Utiliser Ant sur Ubuntu Xenial (16.04).
<a href="#ant-avec-xenial" class="ancre-titre after"></a></h3>

Malheureusement, `ant` n'est actuellement pas préinstallé sur notre image Xenial. Vous devrez l'installer manuellement en ajoutant la solution suivante à votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
dist: xenial
language: java
addons:
  apt:
    packages:
      - ant
```

La liste des JVM disponibles pour différentes distors est à: 

* [JDKs installé pour Noble](./environnement-noble.md) ; 
* [JDKs installé pour Jammy](./environnement-jammy.md) ; 
* [JDKs installé pour Focal](./environnement-focal.md) ; 
* [JDKs installé pour Bionic](./environnement-bionic.md) ; 
* [JDKs installé pour Xenial](./environnement-xenial.md) ; 
* [JDKs installé pour Trusty](./environnement-trusty.md) ; 
* [JDKs installé pour Precise](./environnement-precise.md).

<h3 id="changer-de-sdk">Changer de JDK (Java 8 et versions antérieures) au sein d'une même tâche.
<a href="#changer-de-sdk" class="ancre-titre after"></a></h3>

Si votre version doit changer de JDK (Java 8 et moins) pendant une tâche, vous pouvez le faire avec `jdk_switcher`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script:
  - jdk_switcher use openjdk8
  - # do stuff with open Java 8
```

L'utilisation de `jdk_switcher` met également à jour `$JAVA_HOME` de manière appropriée.

<h2 id="java10-et-plus">Utilisation de Java 10 et versions ultérieures.
<a href="#java10-et-plus" class="ancre-titre after"></a></h2>

<div class="notice cadre">
Prenez note que <code>oraclejdk10</code> est EOL depuis octobre 2018 et en tant que tel, il n'est plus pris en charge sur Travis CI. Voir <a href="https://www.oracle.com/technetwork/java/javase/eol-135779.html">https://www.oracle.com/technetwork/java/javase/eol-135779.html</a>.
<code>openjdk</code> est désormais le JDK par défaut disponible sur nos machines virtuelles car <code>install-jdk</code> n'installe plus <code>oraclejdk</code>. Veuillez consulter ce <a href="https://github.com/sormuras/bach/issues/56">problème GitHub pour le contexte</a>.
</div>

<h3 id="changer-de-sdk10">Changer de JDK (vers Java 10 et versions postérieures) au sein d'une même tâche.
<a href="#changer-de-sdk10" class="ancre-titre after"></a></h3>

Si votre version doit changer de JDK (Java 10 et plus) pendant une tâche, vous pouvez le faire avec `install-jdk.sh`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jdk: openjdk10
script:
  - jdk_switcher use openjdk10
  - # do stuff with OpenJDK 10
  - wget https://github.com/sormuras/bach/raw/master/install-jdk.sh
  - chmod +x $TRAVIS_BUILD_DIR/install-jdk.sh
  - export JAVA_HOME=$HOME/openjdk11
  - $TRAVIS_BUILD_DIR/install-jdk.sh -F 11 --target $JAVA_HOME
  - # do stuff with open OpenJDK 11
```

<h2 id="fournisseurs-jdk">Fournisseurs JDK actuels.
<a href="#fournisseurs-jdk" class="ancre-titre after"></a></h2>

Actuellement, nos builds utilisent les fournisseurs JDK Bellsoft et Adoptium. Ils sont sélectionnés en fonction de la distribution et de l'architecture que vous utilisez dans votre build. De plus, nous avons ajouté le JDK Semeru d'IBM. Pour l'utiliser, au lieu de la syntaxe jdk: jdkX ou jdk: openjdkX, utilisez simplement jdk: semeruX comme ici :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: java
jdk: semeru11
```

JDKs Semeru disponibles pour les architectures AMD, S390X, PPC64LE et architectures ARM : 8, 11, 16, 17, 18, 19, 20, 21, 22.

<h2 id="exemples">Exemples.
<a href="#exemples" class="ancre-titre after"></a></h2>

* [JRuby](https://github.com/jruby/jruby/blob/master/.travis.yml) ;
* [Riak Java client](https://github.com/basho/riak-java-client/blob/master/.travis.yml) ;
* [Cucumber JVM](https://github.com/cucumber/cucumber-jvm/blob/master/.travis.yml) ;
* [Symfony 2 plugin Eclipse](https://github.com/pulse00/Symfony-2-Eclipse-Plugin/blob/master/.travis.yml) ;
* [RESThub](https://github.com/resthub/resthub-spring-stack/blob/master/.travis.yml) ;
* [Joni](https://github.com/jruby/joni/blob/master/.travis.yml), Implémentation des expressions régulières dans JRuby.

<h2 id="references-de-configuration-de-construction-java">Référence de configuration de construction.
<a href="#references-de-configuration-de-construction-java" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Java](https://config.travis-ci.com/ref/language/java) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<hr class="invisible">
