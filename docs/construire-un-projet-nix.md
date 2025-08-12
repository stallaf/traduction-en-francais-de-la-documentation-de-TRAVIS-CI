# Construire un projet Nix

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#communaute-nix">Avertissement soutenu par la communauté</a></li>
            <li><a href="#presentation">Présentation</a></li>
            <li><a href="#outils-disponibles">Outils disponibles</a></li>
            <li><a href="#version-nix">Version Nix par défaut</a></li>
            <li><a href="#cible-par-defaut">Cible par défaut</a></li>
            <li><a href="#manuel-nix">Manuel Nix</a></li>
            <li><a href="#reference-de-configuration-de-construction-nix">Référence de configuration de construction</a></li>
        </ul>
    </div>
</div>
</p>

Ce guide traite de la configuration des projets Nix sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.

<h2 id="communaute-nix">Avertissement soutenu par la communauté.
<a href="#communaute-nix" class="ancre-titre after"></a></h2>

Le support de Travis CI pour Nix est apporté par la communauté et peut être supprimé ou modifié à tout moment. Si vous rencontrez des problèmes, veuillez les signaler dans le Tracker Issue Travis CI avec CC à [@garbas](https://github.com/garbas), [@matthewbauer](https://github.com/matthewbauer) et [@grahamc](https://github.com/grahamc).

<h2 id="presentation">Présentation.
<a href="#presentation" class="ancre-titre after"></a></h2>

Pour installer le magasin Nix et configurer un profil de base à utilisateur unique, définissez la clé `language` dans `.travis.yml` sur `nix`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: nix
```

Le canal par défaut pour `nixpkgs` sera `nixpkgs-unstable`.

<h2 id="outils-disponibles">Outils disponibles.
<a href="#outils-disponibles" class="ancre-titre after"></a></h2>

Les outils en ligne de commande suivants sont disponibles dans l'environnement Nix : 

* nix
* nix-build
* nix-channel
* nix-collect-garbage
* nix-copy-closure
* nix-daemon
* nix-env
* nix-instantiate
* nix-prefetch-url
* nix-shell
* nix-store

<h2 id="version-nix">Version Nix par défaut .
<a href="#version-nix" class="ancre-titre after"></a></h2>

Cela installe Nix 2.3.6 en utilisant [https://nixos.org/releases/nix/nix-2.3.6/install](https://nixos.org/releases/nix/nix-2.3.6/install). Vous pouvez spécifier une version différente du programme d'installation Nix à l'aide de la clé `nix:` dans votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: nix
nix: 2.3.6
```

<div class="notice cadre">
Remarque : Cette option prend en charge toutes les versions Nix, en commençant par la version 1.11.16.
</div>

<h2 id="cible-par-defaut">Cible par défaut.
<a href="#cible-par-defaut" class="ancre-titre after"></a></h2>

Le script de construction par défaut est `nix-build` qui construit tout dans le fichier `default.nix` de la racine du référentiel. Cela peut être remplacé en définissant la clé `script` dans le fichier `.travis.yml`. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: nix
script: nix-build -A tarball release.nix
```

La configuration ci-dessus tentera de construire l'attribut «tarball» à partir de l'expression Nix dans release.nix.

<h2 id="manuel-nix">Manuel Nix.
<a href="#manuel-nix" class="ancre-titre after"></a></h2>

Plus d'informations sur la rédaction d'expressions Nix et comment chacun des outils ci-dessus fonctionne est disponible dans le [manuel Nix](https://nixos.org/nix/manual/).

<h2 id="reference-de-configuration-de-construction-nix">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-nix" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Nix](https://config.travis-ci.com/ref/language/nix) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<hr class="invisible">
