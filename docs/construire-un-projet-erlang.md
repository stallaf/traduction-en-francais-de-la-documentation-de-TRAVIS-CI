# Construire un projet Erlang

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#tests-otp">Tests par rapport aux versions OTP</a></li>
            <li><a href="#script-de-tests-par-defaut">Script de test par défaut</a></li>
            <li><a href="#gestion-des-dependances-erlang">Gestion des dépendances</a></li>
            <li><a href="#variables-environnement-erlang">Variable d'environnement</a></li>
            <li><a href="#reference-de-configuration-de-construction">Référence de configuration de construction</a></li>
            <li><a href="#otp-et-versions-de-publication">OTP et versions de publication</a></li>
            <li><a href="#exemples-erlang">Exemples</a></li>
            <li><a href="#tutoriels-erlang">Tutoriels</a></li>
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
      <tr><th>ERLANG</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
      <tr>
        <td>Typique</a><code>install</code></td>
        <td><code> 	rebar get-deps</code></td>
      </tr>
      <tr>
        <td>Typique</a><code>script</code></td>
        <td><code>rebar compile && rebar</code><br><code>skip_deps=true eunit</code></td>
      </tr>
      <tr>
        <td><a href="#matrice-de-construction">Cle de matrice</a></td>
        <td><code>env</code>,<code>otp_release</code></td>
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
language: erlang
```
      </th></tr>
    </tfoot>
  </table>
</p>
</div>

Ce guide traite des sujets liés à l'environnement de construction et à la configuration spécifiques aux projets Erlang. Veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de la construction](./personnalisation-de-la-construction.md).

Les versions Erlang ne sont pas disponibles sur l'environnement macOS.

<h2 id="tests-otp">Tests par rapport aux versions OTP.
<a href="#tests-otp" class="ancre-titre after"></a></h2>

Les machines virtuelles Travis CI fournissent des versions [OTP Erlang](http://www.erlang.org/download.html) 64 bits construites à l'aide de [kerl](https://github.com/spawngrid/kerl). Pour spécifier les versions OTP pour lesquelles vous souhaitez que votre projet soit testé, utilisez la clé `otp_release` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: erlang
otp_release:
  - "23.0.2"
  - "22.3.4"
```

Obtenez la liste complète des versions précompilées disponibles sur la VM en ajoutant `kerl list installations ` à la section `before_script:` de votre fichier `.travis.yml`. Notez que cette liste n'inclut pas les versions téléchargées à la demande, telles que la version 18.1.

<h2 id="script-de-tests-par-defaut">Script de test par défaut.
<a href="#script-de-tests-par-defaut" class="ancre-titre after"></a></h2>

Travis CI, par défaut, suppose que votre projet soit construit à l'aide de [Rebar3](https://github.com/erlang/rebar3) et utilise Eunit. La commande exacte qu'Erlang Builder utilisera par défaut est :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
Rebar3 eunit
```

si votre projet dispose de fichiers `rebar.config` ou `Rebar.config` dans la racine du référentiel.

Sur les images plus anciennes où `rebar3` n'est pas disponible, nous retombons à [rebar](https://github.com/rebar/rebar) et appelons :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
rebar compile && rebar skip_deps=true eunit
```

Si ni `rebar.config` ni `Rebar.config` se trouve à la racine du référentiel, Erlang Builder retombera à :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
make test
```

<h2 id="gestion-des-dependances-erlang">Gestion des dépendances.
<a href="#gestion-des-dependances-erlang" class="ancre-titre after"></a></h2>

Le constructeur Erlang sur Travis CI suppose que Rebar3 est utilisé pour la gestion des dépendances. Voir la [documentation de Rebar3](http://www.rebar3.org/docs/dependencies) pour plus de détails.

Sur les images plus anciennes où rebar3 n'est pas disponible, nous retombons à [rebar](https://github.com/rebar/rebar) et exécutons :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
rebar3 get-deps
```

pour installer les [dépendances du projet](https://github.com/basho/riak/blob/master/rebar.config) comme indiqué dans le fichier `rediar.config`.

<h2 id="variables-environnement-erlang">Variable d'environnement.
<a href="#variables-environnement-erlang" class="ancre-titre after"></a></h2>

La version OTP qu'une tâche utilise est disponible comme :

```console
TRAVIS_OTP_RELEASE
```

<h2 id="reference-de-configuration-de-construction">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Erlang](https://config.travis-ci.com/ref/language/erlang) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="otp-et-versions-de-publication">OTP et versions de publication.
<a href="#otp-et-versions-de-publication" class="ancre-titre after"></a></h2>

[Ces archives sont disponibles pour une installation à la demande].(https://docs.travis-ci.com/user/languages/erlang#opt-and-release-versions).

<h2 id="exemples-erlang">Exemples.
<a href="#exemples-erlang" class="ancre-titre after"></a></h2>

* [elixir](https://github.com/elixir-lang/elixir/blob/master/.travis.yml) ;
* [mochiweb](https://github.com/mochi/mochiweb/blob/master/.travis.yml) ;
* [ibrowse](https://github.com/cmullaparthi/ibrowse/blob/master/.travis.yml).

<h2 id="tutoriels-erlang">Tutoriels.
<a href="#tutoriels-erlang" class="ancre-titre after"></a></h2>

* (Anglais) [Intégration continue pour Erlang avec Travis-CI](http://blog.equanimity.nl/blog/2013/06/04/continuous-integration-for-erlang-with-travis-ci/) ;
* (Allemand) [Tests automatisés avec Erlang et Travis-CI](http://blog.equanimity.nl/blog/2013/04/25/geautomatiseerd-testen-met-erlang/).
<hr class="invisible">
