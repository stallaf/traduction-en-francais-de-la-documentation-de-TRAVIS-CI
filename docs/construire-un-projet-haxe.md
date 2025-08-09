# Construire un projet Haxe

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#test-versions-haxe">Test par rapport aux versions Haxe</a></li>
            <li><a href="#version-haxe">Version Haxe par défaut</a></li>
            <li><a href="#version-neko">Version Neko par défaut</a></li>
            <li><a href="#configuration-du-test">Configuration du test</a></li>
            <li><a href="#matrice-de-construction">Matrice de construction</a></li>
            <li><a href="#variable-environnement-haxe">Variable d'environnement</a></li>
            <li><a href="#reference-de-configuration-de-construction-haxe"> Référence de configuration de construction</a></li>
        </ul>
    </div>
</div>
</p>

Ce guide traite de la configuration des projets [Haxe](http://haxe.org/) sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de la construction](./personnalisation-de-la-construction.md).

<h3 id="avertissement">Avertissement soutenu par la communauté.
<a href="#avertissement" class="ancre-titre after"></a></h3>

Le soutien de Travis CI à Haxe est apporté par la communauté et peut être supprimé ou modifié à tout moment. Si vous rencontrez des problèmes, veuillez les signaler dans le [Traqueur de problèmes de Travis CI](https://github.com/travis-ci/travis-ci/issues/new?labels=haxe) avec CC à [@andyli](https://github.com/andyli), [@waneck](https://github.com/waneck) et [@simn](https://github.com/Simn).

<h2 id="test-versions-haxe">Test par rapport aux versions Haxe.
<a href="#test-versions-haxe" class="ancre-titre after"></a></h2>

Les workers Haxe sur Travis CI téléchargent et installent le binaire Haxe. Pour sélectionner une ou plusieurs versions, utilisez la clé `haxe:` dans votre fichier `.travis.yml`, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: haxe
haxe:
  - "3.2.1"
  - stable      # the latest stable release defined in https://haxe.org/download/list/
  - development # the latest build of the development branch in http://build.haxe.org/
```

<h2 id="version-haxe">Version Haxe par défaut
<a href="#version-haxe" class="ancre-titre after"></a></h2>

Si vous quittez la clé `haxe:` de votre `.travis.yml`, Travis CI utilisera `stable`, qui est la dernière version stable définie dans la [liste de téléchargement haxe.org](https://haxe.org/download/list/).

<h2 id="version-neko">Version Neko par défaut
<a href="#version-neko" class="ancre-titre after"></a></h2>

Par défaut, [Neko](http://nekovm.org/) 2.3.0 sera également téléchargé et installé. Utilisez la clé `neko:` dans votre fichier `.travis.yml` pour spécifier une autre version Neko, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: haxe
neko: "2.0.0"
```

Cependant, contrairement à `haxe:`, vous ne pouvez fournir qu'une seule valeur (pas un tableau) à `neko:`.

<h2 id="configuration-du-test">Configuration du test.
<a href="#configuration-du-test" class="ancre-titre after"></a></h2>

Si votre projet utilise les fichiers HXML standard pour la création, vous pouvez spécifier la liste des fichiers HXML à l'aide de la clé `hxml:`, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: haxe
hxml:
  - build.hxml
```

Au cours de la phase _install_, le build exécutera `yes | haxelib install $hxml` pour chacune des valeurs fournies. De même, au cours de la phase de _script_ (test), le build exécutera `haxe $hxml` pour chacune des valeurs fournies.

Vous pouvez remplacer le comportement de _install_ et de _script_ par défaut en utilisant respectivement les cles `install:` et `script:`, comme décrit dans le guide de [configuration de build général](./personnalisation-de-la-construction.md).

<h2 id="matrice-de-construction">Matrice de construction.
<a href="#matrice-de-construction" class="ancre-titre after"></a></h2>

Pour les projets Haxe, `env:` et `haxe:` peuvent être donnés sous forme de tableaux pour construire une matrice de construction.

<h2 id="variable-environnement-haxe">Variable d'environnement.
<a href="#variable-environnement-haxe" class="ancre-titre after"></a></h2>

Les versions de Haxe et Neko utilisées par une tâche sont disponibles sous les formes suivantes :

```console
TRAVIS_HAXE_VERSION
TRAVIS_NEKO_VERSION
```

<h2 id="reference-de-configuration-de-construction-haxe">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-haxe" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Haxe](https://config.travis-ci.com/ref/language/haxe) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<hr class="invisible">
