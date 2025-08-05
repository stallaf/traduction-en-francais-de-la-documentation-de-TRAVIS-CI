# Construire un projet Elixir

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#environnement-ci-pour-les-projets-elixir">Environnement CI pour les projets Elixir</a></li>
            <li><a href="#variables-environnement-elixir">Variables d'environnement</a></li>
            <li><a href="#reference-de-configuration-de-construction-elixir">Référence de configuration de construction</a></li>
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
        <tr><th>ELIXIR</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td>Typique<code>install</code></td>
            <td><code>mix local.rebar --force; mix local.hex --force; mix deps.get</code></td>
        </tr>
        <tr>
            <td>Typique<code>script</code></td>
            <td><code>mix test</code></td>
        </tr>
        <tr>
            <td>Cle de matrice</td>
            <td><code>env</code>,<code>elixir</code>,<code>otp_release</code></td>
        </tr>
        <tr>
            <td>Support</td>
            <td><a href="mailto:support@travis-ci.com">Travis CI</a></td>
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
language: elixir
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>

Ce guide traite des sujets liés à l'environnement de construction et à la configuration spécifiques aux projets Elixir. Veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de la construction](./personnalisation-de-la-construction.md).

Les versions Elixir ne sont pas disponibles sur l'environnement macOS.

<h2 id="environnement-ci-pour-les-projets-elixir">Environnement CI pour les projets Elixir.
<a href="#environnement-ci-pour-les-projets-elixir" class="ancre-titre after"></a></h2>

Pour choisir la machine virtuelle Elixir, déclarez dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: elixir
```

<h3 id="version-elixir">Spécifier une version d'Elixir.
<a href="#version-elixir" class="ancre-titre after"></a></h3>

Vous pouvez spécifier la version Elixir à construire avec la clé  `elixir`.

Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
elixir: '1.5.2'
```

ou

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
elixir: '1.5'
```

Le premier indique la version spécifique indiquée, tandis que le second indique la dernière construction de branche de développement qui a les derniers correctifs mais peut être parfois brisée. Voir ce [commentaire sur le problème de GitHub](https://github.com/elixir-lang/elixir/issues/6618#issuecomment-333374372) pour plus de détails.

<h3 id="version-opt">Spécifiez la version de OTP.
<a href="#version-opt" class="ancre-titre after"></a></h3>

Notez que Elixir a des exigences concernant la version sous-jacente de Erlang OTP.

Si la version de OTP spécifiée (implicitement ou explicitement) ne répond pas à cette exigence, Travis CI en choisira une pour vous.

Vous pouvez également remplacer ce choix de version OTP en ajoutant `otp_release`. Par exemple: 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: elixir
elixir:
  - '1.2.2'
otp_release:
  - '18.2.1'
```

Pour tester plusieurs versions Elixir avec différentes versions de OTP :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: elixir
elixir:
  - '1.0.5'
otp_release: '17.4'
jobs:
  include:
    - elixir: '1.2'
      otp_release: '18.0'
```

<h2 id="variables-environnement-elixir">Variables d'environnement.
<a href="#variables-environnement-elixir" class="ancre-titre after"></a></h2>

La version d'Elixir qu'une tâche utilise est disponible sous le nom de :

```console
TRAVIS_ELIXIR_VERSION
```

Comme pour la machine virtuelle Erlang, la version de OTP qu'une tâche utilise est disponible comme :

```console
TRAVIS_OTP_RELEASE
```

<h2 id="reference-de-configuration-de-construction-elixir">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-elixir" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Elixir](https://config.travis-ci.com/ref/language/elixir) dans notre [référence de configuration de construction Travis CI](TRAVIS_OTP_RELEASE).
<hr class="invisible">
