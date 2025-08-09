# Construire un projet Haskell

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#specifier-les-versions-du-compilateur-haskell">Spécifier les versions du compilateur Haskell</a></li>
            <li><a href="#gestion-des-dependances-haskell">Gestion des dépendances</a></li>
            <li><a href="#deploiement-sur-hackage"> Déploiement sur hackage</a></li>
            <li><a href="#reference-de-configuration-de-construction-haskell">Référence de configuration de construction</a></li>
            <li><a href="#construire-avec-stack">Construire avec Stack</a></li>
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
            <td><a href="#gestion-des-dependances-haskell">Défaut<code>install</code></td>
            <td><code>cabal install --only-</code><br><code>dependencies --enable-tests</code></td>
        </tr>
        <tr>
            <td>Défaut<code>script</code></td>
            <td><code> 	cabal configure --enable-tests &&</code><br><code>cabal build && cabal test</code></td>
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

Le reste de ce guide traite de la configuration des projets Haskell sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de la construction](./personnalisation-de-la-construction.md).

<h2 id="specifier-les-versions-du-compilateur-haskell">Spécifier les versions du compilateur Haskell.
<a href="#specifier-les-versions-du-compilateur-haskell" class="ancre-titre after"></a></h2>

L'environnement Haskell sur Travis CI a des versions récentes de GHC (Compilateur Glasgow Haskell) préinstallées. Pour une liste détaillée des versions préinstallées, veuillez consulter «Build Informations System» dans le journal de construction.

Vous pouvez spécifier une ou plusieurs versions GHC en utilisant la notation `major.minor`. Les versions de niveau correctif (7.6.2 par exemple) peuvent changer à tout moment :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: haskell
ghc:
  - "7.10"
  - "7.8"
  - "7.6"
  - "8.4.1"
```

<h2 id="gestion-des-dependances-haskell">Gestion des dépendance.
<a href="#gestion-des-dependances-haskell" class="ancre-titre after"></a></h2>

Par défaut, Travis CI utilise `cabal` pour gérer les dépendances de votre projet :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
cabal install --only-dependencies --enable-tests
```

<h3 id="version-cabal">Spécifiez la version d'installation de cabal.
<a href="#version-cabal" class="ancre-titre after"></a></h3>

Vous pouvez spécifier la version de `cabal` utilisée :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: haskell
cabal: "2.4"
ghc:
  - "8.6.4"
```

<h3 id="paquetages-dans-sous-repertoires">Plusieurs paquetages dans les sous-répertoires.
<a href="#paquetages-dans-sous-repertoires" class="ancre-titre after"></a></h3>

Si vous avez plusieurs paquetages dans des sous-répertoires (chacun contenant un fichier `.cabal`), vous pouvez spécifier ces répertoires dans une variable d'environnement :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: haskell
ghc:
  - "7.10"
  - "7.8"
  - "7.6"
env:
  - PACKAGEDIR="some-package"
  - PACKAGEDIR="some-other-package"
before_install: cd ${PACKAGEDIR}
```

La matrice de build est ensuite construite de telle sorte que chaque paquetage soit compilé avec chaque version de GHC.

<h2 id="deploiement-sur-hackage">Déploiement sur hackage.
<a href="#deploiement-sur-hackage" class="ancre-titre after"></a></h2>

Travis peut télécharger automatiquement votre paquetage sur [Hackage](https://hackage.haskell.org/). Voir le [déploiement sur hackage](./deploiement-sur-hackage.md).

<h2 id="reference-de-configuration-de-construction-haskell">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-haskell" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Haskell](#construire-un-projet-haskell) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="construire-avec-stack">Construire avec Stack.
<a href="#construire-avec-stack" class="ancre-titre after"></a></h2>

Consultez [Stack sur Travis CI](https://docs.haskellstack.org/en/stable/travis_ci/) si vous souhaitez utiliser Stack pour construire votre projet.
<hr class="invisible">
