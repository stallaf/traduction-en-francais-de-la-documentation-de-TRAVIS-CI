# Construire un projet Crystal

<p>
    <div class="tdm">
        <ul>
            <li><a href="#options-de-configuration">Options de configuration</a></li>
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
                <td><code>shards install</code></td>
            </tr>
            <tr>
                <td><a href="#script-de-build-par-defaut-c#">Défaut</a><code>script</code></td>
                <td><code>msbuild /p:Configuration=</code><br><code>crystal spec</code></td>
            </tr>
            <tr>
                <td><a href="#cle-de-matrice-c#">Clé de matrice</a></td>
                <td><code>crystal</code>,<code>env</code><td>
            </tr>
            <tr>
                <td>Support</td>
                <td><a href="https://travis-ci.community/c/languages/crystal">Soutien de la communanuté</a></td>
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
language: crystal
```
            </th></tr>
        </tfoot>
    </table>
</div>

Ce guide couvre l'environnement de construction et les sujets de configuration spécifiques aux projets Crystal. Veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de construction](./personnalisation-de-la-construction.md).

<h3 id="avertissement">Avertissement soutenu par la communauté.
<a href="#avertissement" class="ancre-titre after"></a></h3>

Le soutien de Travis CI à Crystal est apporté par la communauté et peut être supprimé ou modifié à tout moment. Si vous rencontrez des problèmes, veuillez les signaler dans le [_Travis CI Issue Tracker_](https://github.com/travis-ci/travis-ci/issues/new?labels=community:crystal) et CC [@asterite](https://github.com/asterite), [@jhass](https://github.com/jhass), [@waj](https://github.com/waj), [@will](https://github.com/will) et [@bcardiff](https://github.com/bcardiff).

<h2 id="options-de-configuration">Options de configuration.
<a href="#options-de-configuration" class="ancre-titre after"></a></h2>

Par défaut, Travis CI utilisera la dernière version stable de Crystal. Il est également possible de tester des projets par rapport à la version nightly ou à une version spécifique de Crystal. Pour ce faire, définissez la clé `crystal` dans `.travis.yml`. Par exemple, pour tester par rapport à la dernière version stable, à la version _nightly_ la plus récente et à une version stable spécifique :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
dist: xenial
language: crystal
crystal:
  - latest
  - nightly
  - 0.35.1. # example of specific version
```

<div class="notice cadre">
Notez que la construction _nightly_ ne sera disponible que sur les environnements Linux. 
</div>

<div class="notice cadre">
Vous pouvez également jeter un œil à la section <a href="https://config.travis-ci.com/ref/language/crystal">Crystal</a> dans notre <a href="https://config.travis-ci.com/">référence de configuration de construction Travis CI</a>.
</div>
<hr class="invisible">
