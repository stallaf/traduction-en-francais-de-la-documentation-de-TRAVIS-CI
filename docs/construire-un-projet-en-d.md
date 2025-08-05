# Construire un projet en D

<p>
    <div class="tdm">
        <ul>
            <li><a href="#tests-compilateurs">Tests par rapport aux compilateurs</a></li>
        </ul>
    </div>
</p>

<div class="tableau-accueil">
    <table class="tableau">
        <thead>
             <tr><th>C#</th><th>DÉFAUT</th></tr>
        </thead>
        <tbody>
            <tr>
                <td><a href="#gestion-des-dependances-c#">Défaut</a><code>install</code></td>
                <td>N/A</code></td>
            </tr>
            <tr>
                <td><a href="#script-de-build-par-defaut-c#">Défaut</a><code>script</code></td>
                <td><code>dub test --compiler=${DC}</code></td>
            </tr>
            <tr>
                <td><a href="#cle-de-matrice-c#">Clé de matrice</a></td>
                <td><code>d</code>,<code>env</code><td>
            </tr>
            <tr>
                <td>Support</td>
                <td><a href="https://travis-ci.community/c/languages/d">Soutien de la communanuté</a></td>
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
language: d
```
            </th></tr>
        </tfoot>
    </table>
</div>

Ce guide couvre l'environnement de construction et les sujets de configuration spécifiques aux projets D. Veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de construction](./personnalisation-de-la-construction.md).

<h3 id="soutien-communaute-d">Langue soutenue par la communauté
<a href="#soutien-communaute-d" class="ancre-titre after"></a></h3>

D est un langage soutenu par la communauté dans Travis CI, maintenu par [@MartinNowak](https://github.com/MartinNowak) et [@wilzbach](https://github.com/wilzbach). Si vous rencontrez des problèmes, veuillez les signaler sur le [forum communautaire Travis CI](https://travis-ci.community/c/languages/d). Veuillez signaler les problèmes spécifiques au compilateur sur le [suivi des problèmes de DMD](https://issues.dlang.org/), le [suivi des problèmes de LDC](https://github.com/ldc-developers/ldc/issues) ou le [suivi des problèmes de GDC](https://gcc.gnu.org/bugzilla). Les problèmes liés à DUB doivent être signalés sur le [suivi des problèmes de DUB](https://github.com/dlang/dub/issues).

<h2 id="tests-compilateurs">Tests par rapport aux compilateurs.
<a href="#tests-compilateurs" class="ancre-titre after"></a></h2>

Par défaut, Travis CI utilisera la dernière version DMD. Il est également possible de tester des projets contre GDC ou LDC et de choisir des versions de compilateur spécifiques. Pour ce faire, spécifiez le compilateur à l'aide de la clé `d:` dans `.travis.yml`.

Exemples:

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
d: dmd-2.089.1
```
<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
# latest dmd, gdc and ldc
d:
  - dmd
  - gdc
  - ldc
```
<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
# nightlies and betas of dmd, ldc
d:
  - dmd-nightly
  - ldc-latest-ci
  - dmd-beta
  - ldc-beta
```

Toutes les versions valides du [script d'installation officiel de D](https://dlang.org/install.html) sont prises en charge. Les tests sur plusieurs compilateurs créeront une ligne dans votre matrice de construction pour chaque compilateur. Le générateur Travis CI exportera la variable d'environnement `DC` pour pointer vers `dmd`, `ldc2` ou `gdc` et la variable d'environnement `DMD` pour pointer vers `dmd`, `ldmd2` ou `gdmd`.

<div class="notice cadre">
Vous pouvez également jeter un œil à la <a href="https://config.travis-ci.com/ref/language/d">section D</a> dans notre <a href="https://config.travis-ci.com/">référence de configuration Travis CI Build</a>.
</div>
<hr class="invisible">

