# Utiliser Travis CI avec C/C++

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#prerequis-c-c++"> Prérequis</a></li>
            <li><a href="#specifiez-le-langage-c-c++">Spécifiez le langage</a></li>
            <li><a href="#installez-les-dependances-c-c++">Installez les dépendances de build</a></li>
            <li><a href="#configurez-votre-fichier">Configurez votre fichier</a></li>
            <li><a href="#validez-et-envoyez-c-c++">Validez et envoyez</a></li>
            <li><a href="#lectures-complementaires-c-c++">Lectures complémentaires</a></li>
        </ul>
    </div>
</div>
</p>

Ce qui suit est un guide pour démarrer avec Travis CI en utilisant C/C++.

<h2 id="prerequis-c-c++">Prérequis.
<a href="#prerequis-c-c++" class="ancre-titre after"></a></h2>

Avant de commencer, créez un fichier `.travis.yml` et ajoutez-le au répertoire racine de votre projet C/C++. Ce fichier définit comment Travis CI construit et teste votre projet.

<h2 id="specifiez-le-langage-c-c++">Spécifiez le langage.
<a href="#specifiez-le-langage-c-c++" class="ancre-titre after"></a></h2>

Définissez C/C++ comme langage choisi et spécifiez au compilateur de l'utiliser.

Ouvrez votre fichier et entrez :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: c
compiler:
    - gcc
    - clang
```

<h2 id="installez-les-dependances-c-c++">Installer les dépendances de build.
<a href="#installez-les-dependances-c-c++" class="ancre-titre after"></a></h2>

Il est maintenant temps d'installer tous les paquets nécessaires dans le projet, tels que les outils de compilation ou les bibliothèques. Utilisez la section `before_install` pour ajouter la commande suivante :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
    - sudo apt-get update
    - sudo apt-get install -y build-essential
```

<h2 id="configurez-votre-fichier">Configurez votre fichier.
<a href="#configurez-votre-fichier" class="ancre-titre after"></a></h2>

Définissez la build et spécifiez toute commande de test pour compiler et exécuter les tests. Utilisez la section `script` pour spécifier les commandes. Voici un exemple utilisant un `Makefile` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script :
    - make
```

<h2 id="validez-et-envoyez-c-c++"><i>Commit and push</i> (Validez et envoyez).
<a href="#validez-et-envoyez-c-c++" class="ancre-titre after"></a></h2>

Une fois le fichier configuré, validez-le dans votre référentiel, et Travis commencera à compiler et à tester votre projet C/C++. À la fin, votre fichier devrait ressembler à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: c
compiler:
    - gcc
    - clang
before_install:
    - sudo apt-get update
    - sudo apt-get install -y build-essential
script:
    - make
```

<h2 id="lectures-complementaires-c-c++">Lectures complémentaires.
<a href="#lectures-complementaires-c-c++" class="ancre-titre after"></a></h2>

Pour plus d'informations sur les projets C et C++, consultez :

* [Compilation d'un projet C](./construire-un-projet-en-c.md) ;
* [Compilation d'un projet C++](./construire-un-projet-en-c++.md) ;
* [Compilation d'un projet C#](./construire-un-projet-en-c#) ;
* [Recette Travis CI  : C++](https://www.youtube.com/watch?v=9rKfaT8Quzs).
<hr class="invisible">
