# Construireun projet en C#, , F#, ou Visual Basic.

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#tests-execution">Tests par rapport à l'exécution et à la version</a></li>
            <li><a href="#test-mono">Test par rapport à Mono et .NET Core</a></li>
            <li><a href="#modules-complementaires">Modules complémentaires</a></li>
            <li><a href="#tests-unitaires">Exécuter des tests unitaires pour NUnit et xUnit</a></li>
            <li><a href="#reference-de-configuration-de-construction">Référence de configuration de construction</a> 
        </ul>
        </ul>
    </div>
</div>
</p>

<div class="tableau-accueil">
    <table class="tableau">
        <thead>
             <tr><th>C#</th><th>Défaut</th></tr>
        </thead>
        <tbody>
            <tr>
                <td><a href="#gestion-des-dependances-c#">Défaut</a><code>install</code></td>
                <td><code>nuget restore solution-name.sln</code></td>
            </tr>
            <tr>
                <td><a href="#script-de-build-par-defaut-c#">Défaut</a><code>script</code></td>
                <td><code>msbuild /p:Configuration=</code><br><code>Release solution-name.sln</code></td>
            </tr>
            <tr>
                <td><a href="#cle-de-matrice-c#">Clé de matrice</a></td>
                <td><code>Matrix keys</code><td>
            </tr>
            <tr>
                <td>Support</td>
                <td><a href="https://travis-ci.community/c/languages/37-category)">Soutien de la communanuté</a></td>
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
language: csharp
```
            </th></tr>
        </tfoot>
    </table>
</div>

Ce guide couvre l'environnement de construction et les sujets de configuration spécifiques aux projets C #, F # et Visual Basic. Veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de construction](./personnalisation-de-la-construction.md).

<h3 id="langage-soutenu">Langage soutenu par la communauté.
<a href="#langage-soutenu" class="ancre-titre after"></a></h3>

La prise en charge de C#, F# et Visual Basic est assurée par la communauté dans Travis CI. Si vous rencontrez des problèmes, veuillez les signaler dans le [suivi des problèmes Travis CI](https://github.com/travis-ci/travis-ci/issues/new) et mettre en copie [@joshua-anderson](https://github.com/joshua-anderson), [@akoeplinger](https://github.com/akoeplinger) et [@nterry](https://github.com/nterry).

<h3 id="construire-un-environnement">Construire un environnement.
<a href="#construire-un-environnement" class="ancre-titre after"></a></h3>

Actuellement, Travis construit vos projets C#, F# et Visual Basic avec les moteurs d'exécution [Mono](http://www.mono-project.com/) ou [.NET Core](https://github.com/dotnet/core) sous Linux. Veuillez noter que ces moteurs d'exécution n'implémentent pas l'intégralité du framework .NET. Par conséquent, les programmes Windows .NET Framework peuvent ne pas être entièrement compatibles et nécessiter un portage.

<h3 id="apercu">Aperçu.
<a href="#apercu" class="ancre-titre after"></a></h3>

La configuration des projets C #, F # et Visual Basic ressemble à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: csharp
solution: solution-name.sln
```

Lorsque la clé facultative `solution` est présente, Travis exécute la restauration du paquetage NuGet et construit la solution donnée.

<h2 id="tests-execution">Test par rapport à l'exécution et à la version.
<a href="#tests-execution" class="ancre-titre after"></a></h2>

<h3 id="mono">Mono.
<a href="#mono" class="ancre-titre after"></a></h3>

Par défaut, Travis CI utilisera la dernière version Mono. Il est également possible de tester des projets avec des versions spécifiques de Mono. Pour ce faire, spécifiez la version à l'aide de la clé `mono` dans `.travis.yml`. Par exemple, pour tester avec les dernières versions, 3.12.0 et 3.10.0 :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: csharp
mono:
    - latest
    - 3.12.0
    - 3.10.0
...
```

Vous pouvez choisir parmi les versions mono suivantes :

<p>
<table class="tableau">
    <thead>
        <tr><th style="text-align:left">Version</th><th style="text-align:left">Paquetages installés (Linux uniquement)</th></tr>
    </thead>
    <tbody>
        <tr><td>3.10.0 et supérieur</td><td>mono-complete, mono-vbnc, fsharp, nuget, referenceassemblies-pcl.</td></tr>
        <tr><td>3.8.0</td><td>mono-complete, mono-vbnc, fsharp, nuget.</td></tr>
        <tr><td>3.2.8</td><td>mono-complete, mono-vbnc, fsharp.</td></tr>
        <tr><td>2.10.8</td><td>mono-complete, mono-vbnc.</td></tr>
        <tr><td>Aucun</td><td><i>désactive mono (utilisez-le si vous voulez uniquement .NET Core, voir ci-dessous)</i></td></tr>
    </tbody>
</table>
</p>

<div class="notice cadre">
Remarque : Même si vous spécifiez, par exemple, 3.12.0, la version utilisée par votre build peut être 3.12.1 en fonction de la dernière version de la série 3.12.x (c'est une limitation des référentiels Xamarin en ce moment).
</div>

**Canaux Alpha, Beta, and Weekly :** Pour installer et tester les prochaines versions de Mono, indiquez `alpha`, `beta` ou `weekly` comme numéro de version. Veuillez signaler les bogues que vous rencontrez sur ces canaux au projet Mono afin qu'ils puissent être corrigés avant la sortie.

<h3 id="net-core">.NET Core.
<a href="#net-core" class="ancre-titre after"></a></h3>

Par défaut, Travis CI ne teste pas .NET Core. Pour tester .NET Core, ajoutez ce qui suit à votre fichier `.travis.yml`. Notez qu'au moins un script `<command>` est nécessaire pour effectuer la build. L'utilisation de `dotnet restore` est un bon choix par défaut.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: csharp
mono: none
dotnet: 2.1.502
script:
    - dotnet restore
...
```

<div class="ntice cadre">
Remarque : vous devez spécifier le numéro de version du SDK .net Core (pas le .NET Core Runtime).
</div>

Les numéros de version du SDK se trouvent sur le [site web .NET Core](https://dot.net/core).

<h2 id="test-mono">Test par rapport à Mono et .NET Core.
<a href="#test-mono" class="ancre-titre after"></a></h2>

Vous pouvez effectuer des tests à la fois sur Mono et .NET Core à l'aide de `matrix.include`. Cet exemple effectue des tests à la fois sur la dernière version de Mono et .NET Core :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: csharp
solution: travis-mono-test.sln
jobs:
  include:
    - dotnet: 2.1.502
      mono: none
      env: DOTNETCORE=2  # optional, can be used to take different code paths in your script
    - mono: latest
...
```

<h2 id="modules-complementaires">Modules complémentaires.
<a href="#modules-complementaires" class="ancre-titre after"></a></h2>

Le module complémentaire [Coverity Scan](./analyse-coverity.md) n'est pas pris en charge car il ne fonctionne actuellement qu'avec msbuild sous Windows.

<h2 id="tests-unitaires">Exécuter des tests unitaires pour NUnit et xUnit.
<a href="#tests-unitaires" class="ancre-titre after"></a></h2>

Pour exécuter votre suite de tests unitaires, vous devrez d'abord installer un exécuteur de test. L'approche recommandée consiste à l'installer à partir de NuGet, car cela fonctionne également sur l'infrastructure Travis basée sur les conteneurs (c'est-à-dire qu'il n'a pas besoin de sudo).

Les exemples suivants montrent comment vous remplaceriez l'installation et le script pour installer un exécuteur de test et lui transmettre vos assemblages de tests pour les exécuter.

<h3 id="nunit">NUnit.
<a href="#nunit" class="ancre-titre after"></a></h3>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: csharp
solution: solution-name.sln
install:
  - nuget restore solution-name.sln
  - nuget install NUnit.Console -Version 3.9.0 -OutputDirectory testrunner
script:
  - msbuild /p:Configuration=Release solution-name.sln
  - mono ./testrunner/NUnit.ConsoleRunner.3.9.0/tools/nunit3-console.exe ./MyProject.Tests/bin/Release/MyProject.Tests.dll
```

<h3 id="xunit">xUnit.
<a href="#xunit" class="ancre-titre after"></a></h3>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: csharp
solution: solution-name.sln
install:
  - nuget restore solution-name.sln
  - nuget install xunit.runners -Version 1.9.2 -OutputDirectory testrunner
script:
  - msbuild /p:Configuration=Release solution-name.sln
  - mono ./testrunner/xunit.runners.1.9.2/tools/xunit.console.clr4.exe ./MyProject.Tests/bin/Release/MyProject.Tests.dll
```

<div class="notice cadre">
Remarque : il y a un <a href="https://github.com/mono/mono/pull/1654">bug</a> dans mono qui provoque le blocage de xUnit 2.0 après l'exécution des tests. Nous vous recommandons de continuer à utiliser la version 1.9.2 jusqu'à ce que ce bug soit corrigé.
</div>

<h3 id="solution-nuget">Utilisez des paquetages NuGet au niveau de la solution.
<a href="#solution-nuget" class="ancre-titre after"></a></h3>

Une autre façon consiste à ajouter l'exécuteur de tests votre choix en console en tant que paquetage NuGet.

Pour de nombreux projets .NET, ce sera le fichier `./.nuget/packages.config`.

`nuget restore solution-name.sln` installera ensuite ce paquetage également.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: csharp
solution: solution-name.sln
script:
  - msbuild /p:Configuration=Release solution-name.sln
  - mono ./packages/xunit.runners.*/tools/xunit.console.clr4.exe ./MyProject.Tests/bin/Release/MyProject.Tests.dll
```

Remarquez l'utilisation de l'expansion du nom de fichier (le `*`) afin d'éviter d'avoir à coder en dur la version de l'exécuteur de tests.

<h3 id="mstest">MSTest.
<a href="#mstest" class="ancre-titre after"></a></h3>

Le [_framework_ MSTest](https://www.nuget.org/packages/MSTest.TestFramework/) est pris en charge lors du test avec .NET Core ; exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: csharp
mono: none
dotnet: 2.1.502
solution: solution-name.sln
script:
  - dotnet restore
  - dotnet test
...
```

<h2 id="reference-de-configuration-de-construction">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [C#](https://config.travis-ci.com/ref/language/csharp) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<hr class="invisible">