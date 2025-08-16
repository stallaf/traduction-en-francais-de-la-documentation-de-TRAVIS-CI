# Construire un projet Rust

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#choisir-version-de-rust">Choisissez une version de Rust</a></li>
            <li><a href="#gestion-des-dependances-rust">Gestion des dépendances</a></li>
            <li><a href="#script-par-defaut-rust">Script de construction par défaut</a></li>
            <li><a href="#variables-environnement-rust">Variablse d'environnement</a></li>
            <li><a href="#reference-de-configuration-de-construction-rust">Référence de configuration de construction</a></li>
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
    <li><a href="environnement-macos.md">macOs</a> ;</li>
    <li><a href="environnement-windows.md">Windows</a> ;</li>
    <li><a href="environnement-freebsd.md">FreeBSD</a>.</li>
  </ul>
  </p>
  </div>

  <p>
  <table class="tableau">
      <thead>
          <tr><th>RUST</th><th>DÉFAUT</th></tr>
      </thead>
      <tbody>
          <tr>
              <td><a href="#gestion-des-dependances-rust">Défaut</a><code>install</code></td>
              <td><code>cargo build --verbose</code></td>
          </tr>
          <tr>
              <td><a href="#script-par-defaut-rust">Défaut<code>script</code></td>
              <td><code>cargo build --verbose; <br>cargo test --verbose</code></td>
          </tr>
          <tr>
              <td>Cle de matrice</a></td>
              <td><code>rust</code>,<code>env</code><td>
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
language: rust
```
          </th></tr>
      </tfoot>
  </table>
  </p>
  </div>

Ce guide traite de la configuration des projets Rust sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.

<h2 id="choisir-version-de-rust">Choisissez une version de Rust.
<a href="#choisir-version-de-rust" class="ancre-titre after"></a></h2>

Par défaut, nous téléchargeons et installons la dernière version de Rust stable au début de la construction (grâce à `rustup`). Le profil minimal est utilisé et comprend les outils linguistiques suivants : `cargo`, `rustc`, et `rustup`.

Si vous voulez des outils linguistiques supplémentaires comme `rustfmt` ou `clippy`, veuillez les installer dans `before_install`.

Pour tester avec des versions de Rust spécifiques :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: rust
rust:
  - 1.0.0
  - 1.1.0
```

Travis CI prend également en charge les trois [canaux de publication](https://doc.rust-lang.org/book/appendix-07-nightly-rust.html#choo-choo-release-channels-and-riding-the-trains) Rust : `stable`, `beta` et `nightly`.

L'équipe Rust apprécie les tests effectués sur les canaux `beta` et `nightly`, même si vous ne visez que la version stable. Une configuration complète ressemble à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: rust
rust:
  - stable
  - beta
  - nightly
jobs:
  allow_failures:
    - rust: nightly
  fast_finish: true
```

Cela permettra d'exécuter vos tests sur les trois canaux, mais toute interruption dans le canal `nightly` n'entraînera pas l'échec du reste de la compilation.

<h2 id="gestion-des-dependances-rust">Gestion des dépendances.
<a href="#gestion-des-dependances-rust" class="ancre-titre after"></a></h2>

Travis CI utilise Cargo pour installer vos dépendances :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
cargo build --verbose
```

Vous pouvez mettre en cache vos dépendances afin qu'elles ne soient recompilés que si elles ou le compilateur ont été mis à niveau :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
cache: cargo
```

Cela ajoute les répertoires suivants au cache : 

* `$TRAVIS_HOME/.cache/sccache` ;
* `$TRAVIS_HOME/.cargo/` ;
* `$TRAVIS_HOME/.rustup/` ;
* `target`

De plus, il ajoute la commande suivante à la phase `before_cache` de la tâche afin de réduire la taille du cache :

```console
rm -rf "$TRAVIS_HOME/.cargo/registry/src"
```

Cela signifie que si vous remplacez l'étape `before_cache` pour une autre raison, vous devez ajouter l'étape ci-dessus afin de réduire la taille du cache :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_cache:
  - rm -rf "$TRAVIS_HOME/.cargo/registry/src"
  ⋮ # rest of your existing "before_cache"
```

<h2 id="script-par-defaut-rust">Script de construction par défaut.
<a href="#script-par-defaut-rust" class="ancre-titre after"></a></h2>

Travis CI utilise Cargo pour exécuter votre version, les commandes par défaut sont :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
cargo test --verbose
```

Vous pouvez toujours configurer différentes commandes si nécessaire. Par exemple, si votre projet est un [espace de travail](http://doc.crates.io/manifest.html#the-workspace-section), vous devez passer `--workspace` aux commandes de construction pour compiler et tester toutes les caisses membre :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: rust
script:
  - cargo build --verbose --workspace
  - cargo test --verbose --workspace
```

<h2 id="variables-environnement-rust">Variables d'environnement.
<a href="#variables-environnement-rust" class="ancre-titre after"></a></h2>

La version Rust spécifiée dans le `.travis.yml` est disponible pendant la compilation dans la variable d'environnement `TRAVIS_RUST_VERSION`.

<h2 id="reference-de-configuration-de-construction-rust">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-rust" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Rust](https://config.travis-ci.com/ref/language/rust) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<div class="invisible">

