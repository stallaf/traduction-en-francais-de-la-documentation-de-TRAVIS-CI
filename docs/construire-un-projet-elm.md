# Construire un projet ELM

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#tests-versions-elm">Tests avec les versions Elm</a></li>
            <li><a href="#gestion-des-dependances-elm">Gestion des dépendances</a></li>
            <li><a href="#variables-environnement-elm">Variables d'environnement</a></li>
            <li><a href="#reference-de-configuration-de-construction-elm">Référence de configuration de construction</a></li>
        </ul>
    </div>
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
                <td><code>npm install</code></td>
            </tr>
            <tr>
                <td><a href="#script-de-build-par-defaut-c#">Défaut</a><code>script</code></td>
                <td><code>elm-format --validate .</code><br><code>&& elm-test</code></td>
            </tr>
            <tr>
                <td><a href="#cle-de-matrice-elm#">Clé de matrice</a></td>
                <td><code>elm</code>,<code>env</code>,<code>node_js</code><td>
            </tr>
            <tr>
                <td>Support</td>
                <td><a href="https://travis-ci.community/c/languages/elm">Soutien de la communanuté</a></td>
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
language: elm
```
            </th></tr>
        </tfoot>
    </table>
</div>

Ce guide couvre l'environnement de construction et les sujets de configuration spécifiques aux projets [Elm](https://elm-lang.org/). Veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de construction](./personnalisation-de-la-construction.md).

<h3 id="avertissement-soutenu-par-la-communaute-elm">Avertissement soutenu par la communauté.
<a href="#avertissement-soutenu-par-la-communaute-elm" class="ancre-titre after"></a></h3>

Le soutien de Travis CI à Elm est apporté par la communauté et peut être supprimé ou modifié à tout moment. Si vous rencontrez des problèmes, veuillez les signaler dans le [Forum de la communauté Travis CI](https://travis-ci.community/c/languages/elm).

<h2 id="tests-versions-elm">Tests avec les versions Elm.
<a href="#tests-versions-elm" class="ancre-titre after"></a></h2>

Vous pouvez spécifier les versions du langage Elm pour tester votre projet avec la clé `elm`. Il peut s'agir d'une valeur de chaîne unique (par exemple, 0,19.0), ou un tableau de chaîne, auquel cas une matrice de construction composée de tâches exécutant chacune des versions spécifiées dans le tableau.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: elm
elm:
  - elm0.19.0 # default, or equivalently, '0.19.0'
  - elm0.18.0
```

<h3 id="outils-assistance-elm">Outils d'assistance Elm.
<a href="#outils-assistance-elm" class="ancre-titre after"></a></h3>

Les tâches Elm installeront également `elm-test` et `elm-format`. Par défaut, ces outils correspondent à la version `elm` spécifiée, avec le préfixe `elm`. Par exemple, avec `elm: 0.19.0`, la version `elm-test` et `elm-format` avec la balise `elm0.19.0` sera installée. Vous pouvez également les remplacer indépendamment avec les clés `elm-test` et `elm-format` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: elm
elm-test: 0.19.0-rev3
elm-format: 0.8.0
```

Notez que ces valeurs sont transmises à `npm`. Cela signifie qu'elles peuvent être spécifiées en tant que chaîne de version (par exemple, `0.19.0`) ou une balise (par exemple, `elm0.19.0`). `elm-test` et `elm-format` par défaut en balises avec le préfixe `elm`, dérivé de la valeur `elm`.

<h3 id="test-avec-nodejs">Test avec les versions Node.js.
<a href="#test-avec-nodejs" class="ancre-titre after"></a></h3>

Les projets Elm peuvent également spécifier la version [Node.js](./construire-un-projet-javasript.md), comme c'est le cas pour les projets [Node.js](./construire-un-projet-javascript.md).

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: elm
node_js: '10' # latest 10.x
```

La version Node.js par défaut est `10.13.0`.

<h2 id="gestion-des-dependances-elm">Gestion des dépendances.
<a href="#gestion-des-dependances-elm" class="ancre-titre after"></a></h2>

Pour la gestion des dépendances, les projets Elm utilisent la même logique que les projets Node.js. Voir la [documentation Node.js](./construire-un-projet-javascript.md#todo) pour plus de détails.

<h2 id="variables-environnement-elm">Variables d'environnement.
<a href="#variables-environnement-elm" class="ancre-titre after"></a></h2>

* La version d'Elm utilisée par une tâche est disponible sous `TRAVIS_ELM_VERSION`. La valeur par défaut est `elm0.19.0` ;
* `TRAVIS_ELM_TEST_VERSION` pointe vers la version `elm-test` utilisée. Si vous remplacez `elm-test`, cette valeur est utilisée. Si `elm-test` n'est pas remplacé mais que `elm` l'est, la valeur avec le préfixe `elm` est utilisée. (Par exemple, `elm: 0.18.0` choisit `elm-test@elm0.18.0.`) Si ni `elm-test` ni `elm` ne sont remplacés, la valeur par défaut, `elm0.19.0`, est utilisée.
* `TRAVIS_ELM_FORMAT_VERSION` pointe vers la version `elm-format` utilisée. Si vous remplacez `elm-format`, cette valeur est utilisée. Si `elm-format` n'est pas remplacé mais que `elm` l'est, la valeur avec le préfixe `elm` est utilisée. (Par exemple, `elm: 0.18.0` choisit `elm-format@elm0.18.0.`) Si ni `elm-format` ni `elm ne sont remplacés, la valeur par défaut, `elm0.19.0`, est utilisée.
* `TRAVIS_NODE_VERSION` pointe vers la version `node_js` utilisée.

<h2 id="reference-de-configuration-de-construction-elm">Construire la référence de configuration.
<a href="#reference-de-configuration-de-construction-elm" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Elm](https://config.travis-ci.com/ref/language/elm) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<hr class="invisible">