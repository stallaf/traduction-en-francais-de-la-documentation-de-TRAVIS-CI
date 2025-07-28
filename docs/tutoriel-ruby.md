# Utiliser Travis CI avec Ruby

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#prerequis-ruby">Prérequis</a></li>
            <li><a href="#specifiez-le-langage-ruby">Spécifiez le langage et la version</a></li>
            <li><a href="#installez-les-dependances-ruby">Installez les dépendances de build</a></li>
            <li><a href="#definir-la-commande-de-test-ruby">Définir la commande de test</a></li>
            <li><a href="#validez-et-envoyez-ruby"><i>Commit and push</i> (Validez et envoyez)</a></li>
            <li><a href="#lectures-complementaires-ruby">Lectures complémentaires</a></li>
        </ul>
    </div>
</div>
</p>

Ce qui suit est un guide pour démarrer avec Travis CI en utilisant Ruby.

<h2 id="prerequis-ruby">Prérequis.
<a href="#prerequis-ruby" class="ancre-titre after"></a></h2>

Pour commencer, créez un fichier `.travis.yml` et ajoutez-le au répertoire racine de votre projet Ruby.

<h2 id="specifiez-le-langage-ruby">Spécifiez le langage et la version.
<a href="#specifiez-le-langage-ruby" class="ancre-titre after"></a></h2>

Définissez la version de Ruby que vous souhaitez tester avec Travis CI. L'exemple ci-dessous utilise les versions 2.7 et 3.0 de Ruby.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
rvm:
    - 2.7
    - 3.0
```

<h2 id="installez-les-dependances-ruby">Installer les dépendances de build.
<a href="#installez-les-dependances-ruby" class="ancre-titre after"></a></h2>

Installez vos dépendances via Bundler à l'aide de la commande `bundle install`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
    - bundle install
```

<h2 id="definir-la-commande-de-test-ruby">Définir la commande de test.
<a href="#definir-la-commande-de-test-ruby" class="ancre-titre after"></a></h2>

Spécifiez une commande pour exécuter vos tests. L'exemple ci-dessous utilise RSpec :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script:
    - bundle exec rspec
```

<h2 id="validez-et-envoyez-ruby"><i>Commit and push</i> (Validez et envoyez).
<a href="#validez-et-envoyez-ruby" class="ancre-titre after"></a></h2>

Poussez le fichier `.travis.yml` vers votre référentiel, et Travis CI exécutera les tests pour votre projet Ruby. Voici l'exemple complet :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: ruby
rvm:
    - 2.7
    - 3.0
install:
    - bundle install
script:
    - bundle exec rspec
```

<h2 id="lectures-complementaires-ruby">Lectures complémentaires.
<a href="#lectures-complementaires-ruby" class="ancre-titre after"></a></h2>

Pour plus d'informations sur les projets Ruby, consultez :

* [Création d'un projet Ruby](./construire-un-projet-en-ruby.md).
<hr class="invisible">
