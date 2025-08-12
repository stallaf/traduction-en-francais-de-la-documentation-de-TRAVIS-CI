# Construire-un-projet-matlab.md

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#communaute-matlab">Langage soutenu par la communauté</a></li>
            <li><a href="#versions-matlab">Spécifier les versions MATLAB et exécuter des tests</a></li>
            <li><a href="#executer-commandes-matlab-personnalisees">Exécuter des commandes MATLAB personnalisées</a></li>
            <li><a href="#lectures-complementaires">Lectures complémentaires</a></li>
        </ul>
    </div>
</div>
</p>

<div class="deux-colonnes">
Ce guide traite de la configuration des projets <a href="https://www.mathworks.com/products/matlab.html">MATLAB</a> et  <a href="https://www.mathworks.com/products/simulink.html">Simulink</a> sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.

<div class="notice cadre">
Actuellement, les builds MATLAB sont disponibles uniquement pour les projets publics dans les environnements Linux ®.
</div>

<h2 id="communaute-matlab">Langagae soutenu par la communauté.
<a href="#communaute-matlab" class="ancre-titre after"></a></h2>

Le langage Matlab est maintenu par MathWorks®. Si vous avez des questions ou des suggestions, veuillez contacter Mathworks à <a href="mailto:continuous-integration@mathworks.com">continuous-integration@mathworks.com</a>.

<p>
<table class="tableau">
    <thead>
        <tr><th>MATLAB</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="#gestion-des-dependances">Défaut<code>install</code></td>
            <td>N/A</td>
        </tr>
        <tr>
            <td><a href="#construction-du-script-par-defaut">Défaut<code>script</code></td>
            <td><code>matlab -batch "results =</code><br>
            <code>runtests('IncludeSubfolders',true);</code><br>
            <code>assertSuccess(results);"</code></td>
        </tr>
        <tr>
            <td>Cle de matrice</a></td>
            <td><code>matlab</code>,<code>env</code><td>
        </tr>
        <tr>
            <td>Support</td>
            <td><a href="mailto:continuous-integration@mathworks.com">Travis CI</a></td>
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
language: matlab
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>

<h2 id="versions-matlab">Spécifiez les versions MATLAB et exécutez les tests.
<a href="#versions-matlab" class="ancre-titre after"></a></h2>

Spécifiez les versions MATLAB à l'aide de la clé `matlab`. Vous pouvez spécifier R2020A ou une version ultérieure. Si vous ne spécifiez pas de version, Travis CI utilise la dernière version de MATLAB.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: matlab
matlab:
  - latest  # Default MATLAB release on Travis CI
  - R2020a
```

Lorsque vous incluez `language: matlab` dans votre `.travis.yml` : 

* Travis CI installe la version MATLAB spécifiée sur un agent de construction basé sur Linux. Si vous ne spécifiez pas de version, Travis CI installe la dernière version de MATLAB ;
* MATLAB exécute les tests dans votre référentiel et échoue à la construction si l'un des tests échoue.

Si votre code source est organisé en fichiers et dossiers dans un [projet MATLAB](https://www.mathworks.com/help/matlab/projects.html), MATLAB exécute tous les fichiers de test dans le projet qui a été étiqueté comme `Test`.  Si votre code n'utilise pas de projet MATLAB, MATLAB exécute tous les tests à la racine de votre référentiel, y compris ses sous-dossiers.

Vous pouvez remplacer l'exécution de test par défaut et générer des artefacts en créant un exécuteur de test et en le personnalisant à l'aide des classes de plug-in du paquetage [`matlab.unittest.plugins`](https://www.mathworks.com/help/matlab/ref/matlab.unittest.plugins-package.html). Pour plus d'informations sur la manière de spécifier et d'exécuter des commandes MATLAB, consultez la section ci-dessous : [exécuter des commandes MATLAB personnalisées](#executer-les-commandes-matlab-personnalisees).

<h2 id="executer-commandes-matlab-personnalisees">Exécuter des commandes MATLAB personnalisées.
<a href="#executer-commandes-matlab-personnalisees" class="ancre-titre after"></a></h2>

Vous pouvez spécifier la clé `script` dans votre `.travis.yml` pour construire sur les fonctionnalités fournies par `language: matlab`. Pour exécuter les commandes MATLAB personnalisées dans votre pipeline, utilisez la commande [`matlab`](https://www.mathworks.com/help/matlab/ref/matlablinux.html) avec l'option `-batch`. `matlab -batch` démarre MATLAB de manière non interactive et exécute le script, la fonction ou l'instruction spécifiés. Par exemple, appelez la fonction `disp` en utilisant la dernière version de MATLAB.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: matlab
script: matlab -batch 'disp("Hello World")'
```

Si vous devez spécifier plus d'une commande MATLAB, utilisez une virgule ou un point-virgule pour séparer les commandes.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: matlab
script: matlab -batch 'results = runtests, assertSuccess(results);'
```

Vous pouvez écrire un script ou une fonction MATLAB dans le cadre de votre référentiel et exécuter ce script ou cette fonction. Par exemple, utilisez MATLAB R2020A pour exécuter les commandes dans un fichier nommé `myscript.m` à la racine de votre référentiel. (Pour exécuter un script ou une fonction MATLAB, ne spécifiez pas l'extension de fichier).

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: matlab
matlab: R2020a
script: matlab -batch 'myscript'
```

MATLAB quitte avec le code de sortie 0 si le script, la fonction ou l'instruction spécifié(e) s'exécute correctement sans erreur. Sinon, MATLAB quitte avec un code de sortie différent de zéro, ce qui entraîne l'échec de la construction. Vous pouvez utiliser les fonctions [`assert`](https://www.mathworks.com/help/matlab/ref/assert.html) ou [`error`](https://www.mathworks.com/help/matlab/ref/error.html) dans votre code pour vous assurer que les constructions échouent lorsque cela est nécessaire.

<h2 id="lectures-complementaires">Lectures complémentaires.
<a href="#lectures-complementaires" class="ancre-titre after"></a></h2>

* [Intégration continue avec Matlab et Simulink](https://www.mathworks.com/solutions/continuous-integration.html) ;
* [Intégration continue (MATLAB)](https://www.mathworks.com/help/matlab/continuous-integration.html) ;
* [Intégration continue (test Simulink)](https://www.mathworks.com/help/sltest/continuous-integration.html).
<hr class="invisible">

