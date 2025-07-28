# Utiliser Travis CI avec Java

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#prerequis2">Prérequis</a></li>
            <li><a href="#specifiez-le-langage-et-le-jdk">Spécifier le langage et la version JDK</a></li>
            <li><a href="#definir-la-commande-de-test">Définir la commande de test</a></li>
            <li><a href="#validez-et-envoyez-java"><i>Commit and push</i> (Validez et envoyez)</a></li>
            <li><a href="#lectures-complementaires">Lectures complémentaires</a></li>
        </ul>
    </div>
</div>
</p>

Ce qui suit est un guide pour démarrer avec Travis CI en utilisant Java.

<h2 id="prerequis2">Prérequis.
<a href="#prerequis2" class="ancre-titre after"></a></h2>

Avant de commencer, créez un fichier `.travis.yml` et ajoutez-le au répertoire racine de votre projet Java. Ce fichier définit comment Travis CI construit et teste votre projet.

<h2 id="specifiez-le-langage-et-le-jdk">Spécifier le langage et la version JDK.
<a href="#specifiez-le-langage-et-le-jdk" class="ancre-titre after"></a></h2>

Ajoutez le code suivant pour indiquer à Travis CI que vous travaillez avec Java :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: java
```

Vous pouvez tester votre projet sur plusieurs versions Java. L'exemple suivant utilise OpenJDK 11 et OpenJDK 17.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
jdk:
    - openjdk11
    - openjdk17
```

<h3 id="installer-dependances-de-build-java">Installer les dépendances de build.
<a href="#installer-dependances-de-build-java" class="ancre-titre after"></a></h3>

Travis CI installe automatiquement les dépendances du projet en fonction de votre outil de compilation (comme Maven ou Gradle). Pour les projets Maven, utilisez la commande suivante :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
    - mvn install -DskipTests=true -B -V
```

La commande ci-dessus installe toutes les dépendances nécessaires sans exécuter de tests pendant l'installation.

<h2 id="definir-la-commande-de-test">Définir la commande de test.
<a href="#definir-la-commande-de-test" class="ancre-titre after"></a></h2>

Utilisez la clé `script` pour spécifier la commande que vous souhaitez que Travis CI exécute pour vos tests unitaires. Pour un projet Maven, utilisez le code suivant :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script:
    - mvn test
```

Le code ci-dessus exécute les tests unitaires avec les paramètres Maven par défaut.

<h2 id="validez-et-envoyez-java"><i>Commit and push</i> (Validez et envoyez).
<a href="#validez-et-envoyez-java" class="ancre-titre after"></a></h2>

Une fois le fichier créé, validez-le dans votre référentiel. Travis CI détecte le fichier et commence automatiquement à construire et à tester votre projet chaque fois que vous poussez une nouvelle modification. Voici le code complet :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: java
jdk:
    - openjdk11
    - openjdk17
install:
    - mvn install -DskipTests=true -B -V
script:
    - mvn test
```

<h2 id="lectures-complementaires">Lectures complémentaires.
<a href="#lectures-complementaires" class="ancre-titre after"></a></h2>

Pour plus d'informations sur les projets Java, consultez :

* [Création d'un projet Java](./construire-un-projet-en-java.md) ;
* [Création d'un projet JavaScript](./construire-un-projet-en-javascript.md).
<hr class="invisible">