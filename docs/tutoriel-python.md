# Utiliser Travis CI avec Python

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#prerequis-python">Prérequis</a></li>
            <li><a href="#specifiez-le-langage-python">Spécifiez le langage et la version</a></li>
            <li><a href="#installez-les-dependances-python">Installez les dépendances de build</a></li>
            <li><a href="#definir-la-commande-de-test-python">Définir la commande de test</a></li>
            <li><a href="#validez-et-envoyez-python"><i>Commit and push</i> (Validez et envoyez)</a></li>
            <li><a href="#lectures-complementaires-python">Lectures complémentaires</a></li>
        </ul>
    </div>
</div>
</p>

Ce qui suit est un guide pour démarrer avec Travis CI en utilisant Python.

<h2 id="prerequis-python">Prérequis.
<a href="#prerequis-python" class="ancre-titre after"></a></h2>

À la racine de votre projet Python, créez un fichier nommé `.travis.yml`. Ce fichier définit comment Travis CI construit et teste votre projet.

<h2 id="specifiez-le-langage-python">Spécifiez le langage et la version.
<a href="#specifiez-le-langage-python" class="ancre-titre after"></a></h2>

Ajoutez le code suivant pour indiquer à Travis CI que vous travaillez avec Python et les versions que vous souhaitez tester :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: python
python:
    - « 3.8 »
    - « 3.9 »
    - « 3.10 »
```

<h2 id="installez-les-dependances-python">Installer les dépendances de build.
<a href="#installez-les-dependances-python" class="ancre-titre after"></a></h2>

Travis CI exécutera automatiquement les commandes pour installer vos dépendances. Dans notre exemple, un fichier requirements.txt gère les dépendances.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
    - pip install -r requirements.txt
```

<h2 id="definir-la-commande-de-test-python">Définir la commande de test.
<a href="#definir-la-commande-de-test-python" class="ancre-titre after"></a></h2>

Spécifiez une commande pour exécuter vos tests. L'exemple ci-dessous utilise pytest :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script:
    - pytest
```

<h2 id="validez-et-envoyez-python"><i>Commit and push</i> (Validez et envoyez).
<a href="#validez-et-envoyez-python" class="ancre-titre after"></a></h2>

Une fois que vous avez configuré votre fichier `.travis.yml`, poussez-le vers votre dépôt GitHub, et Travis CI déclenchera automatiquement le processus de build et de test. Voici l'exemple complet :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
langage : python
python :
    - « 3.8 »
    - « 3.9 »
    - « 3.10 »
install:
    - pip install -r requirements.txt
script:
    - pytest
```

<h2 id="lectures-complementaires-python">Lectures complémentaires.
<a href="#lectures-complementaires-python" class="ancre-titre after"></a></h2>

Pour plus d'informations sur les projets Python, consultez :

* [Création d'un projet Python](./construire-un-projet-en-python) ;
* [Premier fichier .travis.yml](https://youtu.be/MLMwfDjMMIE) ;
* [Premiers pas avec Python](https://www.youtube.com/watch?v=nkqgB7VNDEE).
<hr class="invisible">
