# Construire un projet Julia

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#communaute-julia">Avertissement soutenu par la communauté</a></li>
            <li><a href="#test-versions-julia">Test des versions Julia</a></li>
            <li><a href="#couverture">Couverture</a></li>
            <li><a href="#gestion-des-dependances-julia">Gestion des dépendances</a></li>
            <li><a href="#script-de-build-par-defaut-julia">Script de construction par défaut</a></li>
            <li><a href="#matrice-de-construction">Matrice de construction</a></li>
            <li><a href="#variable-environnement-julia"> Variable d'environnement</a></li>
            <li><a href="#exemples-julia">Exemples</a></li>
            <li><a href="#reference-de-compilation-de-construction-julia">Référence de configuration de construction<a></li>
        </ul>
    </div>
</div>
</p>

Ce guide traite de la configuration des projets [Julia](http://julialang.org/) sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.

<h2 id="communaute-julia">Avertissement soutenu par la communauté.
<a href="#communaute-julia" class="ancre-titre after"></a></h2>

Le soutien de Travis CI à Julia est apporté par la communauté et peut être supprimé ou modifié à tout moment. Si vous rencontrez des problèmes, veuillez les signaler dans les [forums communautaires de Travis CI Julia](https://travis-ci.community/c/languages/julia) avec CC à [@ararslan](https://github.com/ararslan), [@StaticFloat](https://github.com/staticfloat) et [@Stefankarpinski](https://github.com/StefanKarpinski).

Pour obtenir une assistance générale de Julia sur Travis CI, rendez-vous à la [communauté Travis](https://travis-ci.community/c/languages/julia) ou sur le [canal Slack Julia Lang](https://julialang.org/slack/) dans le canal **#testing**.

<h2 id="test-versions-julia">Test des versions Julia.
<a href="#test-versions-julia" class="ancre-titre after"></a></h2>

Les tâches Julia sur Travis CI téléchargent et installent un binaire Julia. Vous pouvez spécifier les versions Julia à tester dans la clé `julia:` de votre fichier `.travis.yml`. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: julia
julia:
  - nightly
  - 1.0.6
  - 1.5.2
```

Les formats acceptables sont : 

* `nightly` testera la dernière version nightly de Julia ;
* `X` testera la dernière version disponible pour cette version majeure. (S'applique uniquement aux versions majeures 1 et ultérieures.) ;
* `X.Y` testera la dernière version disponible pour cette version mineure ;
* `X.Y.Z` testera cette version exacte.

La version la plus ancienne pour laquelle les binaires sont disponibles sont 0.3.1 pour Linux.

<h2 id="couverture">Couverture.
<a href="#couverture" class="ancre-titre after"></a></h2>

Des services tels que [codecov.io](https://codecov.io/) et [coverals.io](https://coveralls.io/) fournissent des résumés et des analyses de la couverture de la suite de tests. Après avoir activé les services respectifs des référentiels, les options `codecov` et `coveralls` peuvent être utilisées comme suit, en les plaçant au niveau supérieur du document YAML :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
codecov: true
coveralls: true
```

Cela téléchargera ensuite les statistiques de couverture une fois la réussite des tests vers les services spécifiés.

<h2 id="script-de-build-par-defaut-julia">Script de construction par défaut.
<a href="#script-de-build-par-defaut-julia" class="ancre-titre after"></a></h2>

Si votre référentiel contient le fichier `Juliaproject.toml` ou `Project.toml` et que vous construisez sur Julia V0.7 ou version ultérieure, le script de construction par défaut sera :

<div class="titre-code">
    <div class="item gauche">&nbsp;Juliaproject.toml</div><div class="item droit">Julia</div>
</div>
```julia
using Pkg
Pkg.build() # Pkg.build(; verbose = true) for Julia 1.1 and up
Pkg.test(coverage=true)
```

sinon, il utilisera l'ancienne forme :

<div class="titre-code">
    <div class="item gauche">&nbsp;Juliaproject.toml</div><div class="item droit">Julia</div>
</div>
```julia
if VERSION >= v"0.7.0-DEV.5183"
    using Pkg
end
Pkg.clone(pwd())
Pkg.build("$pkgname") # Pkg.build("$pkgname"; verbose = true) for Julia 1.1 and up
Pkg.test("$pkgname", coverage=true)
```

où le nom du paquet `$pkgname` correspond au nom du référentiel, sans le suffixe `.jl`.

Notez que l'argument de la `coverage=true` indique uniquement à `Pkg.test` d'émettre des informations de couverture sur les tests qu'il a effectués; Il ne soumet pas ces informations à aucun service. Pour soumettre des informations de couverture, consultez la section de couverture ci-dessus.

Il existe deux scripts qui décrivent le comportement par défaut pour l'utilisation de Julia avec Travis CI : [julia.rb](https://github.com/travis-ci/travis-build/blob/master/lib/travis/build/script/julia.rb) et [julia_spec.rb](https://github.com/travis-ci/travis-build/blob/master/spec/build/script/julia_spec.rb).

<h2 id="gestion-des-dependances-julia">Gestion des dépendances.
<a href="#gestion-des-dependances-julia" class="ancre-titre after"></a></h2>

Si votre paquetage Julia dispose d'un fichier `deps/build.jl`, alors `Pkg.build("$name")` exécutera ce fichier pour installer toutes les dépendances du paquetage. Si vous devez installer manuellement toutes les dépendances qui ne sont pas gérées par `deps/build.jl`, il est possible de spécifier une commande d'installation de dépendance personnalisée comme décrit dans le guide de [configuration de build général](./personnalisation-de-la-construction.md).

Dans un cas rare, vous devrez peut-être cloner un dépôt privé s'il s'agit d'une dépendance du dépôt que vous essayez de tester. Pour ajouter un dépôt privé, consultez le lien ici : [dépendances privées](./dependances-privees.md). Une fois que vous avez ajouté le dépôt, vous devrez le copier dans votre dossier Julia, puis exécuter le script de construction par défaut. Découvrez le script ci-dessous pour que Linux puisse voir comment cela se fait :

```console
script:
 #- ls #Optional command.  Just here to confirm the Dependency is in the folder you think it is. 
 #- pwd #Optional command. Just here so you can see where you are in the file system to make sure the path is correct below. 
 - julia --project --color=yes --check-bounds=yes -e 'using Pkg; Pkg.develop(PackageSpec(path="/home/travis/build/path_to_private_Dependency")); Pkg.instantiate()'
 - julia --project --color=yes --check-bounds=yes -e 'using Pkg; Pkg.instantiate(); Pkg.build();'
```

Remarque : vous devrez disposer du fichier `project.toml` dans votre référentiel pour que les commandes ci-dessus fonctionnent. Vous le trouverez dans votre dossier `~/.julia/enviroments/`.

<h2 id="matrice-de-construction">Matrice de construction.
<a href="#matrice-de-construction" class="ancre-titre after"></a></h2>

Pour les projets Julia, `env` et `julia` peuvent être donnés en tant que tableaux pour construire une matrice de construction.

<h2 id="variable-environnement-julia">Variable d'environnement.
<a href="#variable-environnement-julia" class="ancre-titre after"></a></h2>

La version de Julia utilisée par une tâche est disponible sous :

```console
TRAVIS_JULIA_VERSION
```

De plus, `JULIA_PROJECT` est défini sur `@.`, ce qui signifie que Julia recherchera dans les répertoires parents jusqu'à ce qu'un fichier `project.toml` ou `juliaproject.toml` soit trouvé ; le répertoire contenu est alors utilisé dans le projet/environnement domestique.

<h2 id="exemples-julia">Exemples.
<a href="#exemples-julia" class="ancre-titre after"></a></h2>

Voici une liste des projets Julia open source en utilisant Travis CI de différentes manières : 

* [AbstractPlotting.jl](https://github.com/JuliaPlots/AbstractPlotting.jl/blob/master/.travis.yml) ;
* [DiffEqDocs.jl](https://github.com/JuliaDiffEq/DiffEqDocs.jl/blob/master/.travis.yml) ;
* [Pkg.jl](https://github.com/JuliaLang/Pkg.jl/blob/master/.travis.yml) ;
* [NeuralVerification.jl](https://github.com/sisl/NeuralVerification.jl/blob/master/.travis.yml) ;
* [POMDPs.jl](https://github.com/JuliaPOMDP/POMDPs.jl/blob/master/.travis.yml).

<h2 id="reference-de-compilation-de-construction-julia">Référence de configuration de construction.
<a href="#reference-de-compilation-de-construction-julia" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Julia](https://config.travis-ci.com/ref/language/julia) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<hr class="invisible">
