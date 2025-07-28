# Utiliser Travis CI avec NodeJS

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#prerequis-nodejs">Prérequis</a></li>
            <li><a href="#specifiez-le-langage-nodejs">Spécifiez le langage et la version</a></li>
            <li><a href="#installez-les-dependances-nodejs">Installez les dépendances de build</a></li>
            <li><a href="#definir-la-commande-de-test-nodejs">Définir la commande de test</a></li>
            <li><a href="#validez-et-envoyez-nodejs"><i>Commit and push</i> (Validez et envoyez)</a></li>
            <li><a href="#lectures-complementaires-nodejs">Lectures complémentaires</a></li>
        </ul>
    </div>
</div>
</p>

Voici un guide pour démarrer avec Travis CI en utilisant Node.js.

<h2 id="prerequis-nodejs">Prérequis.
<a href="#prerequis-nodejs" class="ancre-titre after"></a></h2>

Avant de commencer, créez un fichier `.travis.yml` et ajoutez-le au répertoire racine de votre projet Node.js.

<h2 id="specifiez-le-langage-nodejs">Spécifiez le langage et la version.
<a href="#specifiez-le-langage-nodejs" class="ancre-titre after"></a></h2>

Spécifiez Node.js comme langage et version de Node.js que le projet utilisera pour indiquer à Travis CI que vous développez un projet Node.js. Voici un exemple de configuration :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: node_js
node_js:
    - "14"
    - "16"
    - "18"
```

<h2 id="installez-les-dependances-nodejs">Installer les dépendances de build.
<a href="#installez-les-dependances-nodejs" class="ancre-titre after"></a></h2>

Travis CI exécute la commande `npm install` par défaut pour installer vos dépendances.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
 install:
    - npm install
```

<h2 id="definir-la-commande-de-test-nodejs">Définir la commande de test.
<a href="#definir-la-commande-de-test-nodejs" class="ancre-titre after"></a></h2>

Travis CI exécute par défaut tous les tests définis dans votre fichier `package.json`. Exécutez explicitement la commande comme suit :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
    - npm install
```

<h2 id="validez-et-envoyez-nodejs"><i>Commit and push</i> (Validez et envoyez).
<a href="#validez-et-envoyez-nodejs" class="ancre-titre after"></a></h2>

Une fois votre fichier `.travis.yml` configuré, poussez-le vers votre référentiel, et Travis CI commencera à exécuter les tests pour chaque version Node spécifiée.

Voici l'exemple complet :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: node_js
node_js:
    - « 14 »
    - « 16 »
    - « 18 »
install:
    - npm install
script:
    - npm test
```

<h2 id="lectures-complementaires-nodejs">Lectures complémentaires.
<a href="#lectures-complementaires-nodejs" class="ancre-titre after"></a></h2>

Pour plus d'informations sur les projets Node.js, consultez :

* [Création d'un projet Node.js](./construire-un-projet-en-nodejs.md).
<hr class="invisible">