# Images minimales et génériques

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#ce-que-couvre-ce-guide">Ce que couvre ce guide</a></li>
            <li><a href="#valeurs-par-defaut">Valeurs par défaut</a></li>
            <li><a href="#minimal">Minimal</a></li>
            <li><a href="#generique">générique</a></li>
            <li><a href="#alias">Alias</a></li>
        </ul>
    </div>
</div>
</p>

<h2 id="ce-que-couvre-ce-guide">Ce que couvre ce guide.
<a href="#ce-que-couvre-ce-guide" class="ancre-titre after"></a></h2>

Travis CI prend en charge de nombreux langages de programmation populaires, mais ne peut jamais espérer les soutenir tous. Ce guide couvre comment utiliser des images minimales et génériques.

`language: minimal` et `language: generic` sont des images disponibles dans Ubuntu Noble `dist: noble`, Ubuntu Jammy `dist: jammy`, Ubuntu focal `dist: focal`, Ubuntu Bionic `dist: bionic`, Ubuntu Xenial `dist: xenial` et Ubuntu Trusty `dist:trusty`, qui ne sont pas adaptés à un langage de programmation particulier. Comme leurs noms le suggèrent, `language: minimal` est optimisé pour être plus rapide et utiliser moins d'espace disque, et la `language: generic` a plus de langues et de services disponibles. 

<div class="notice cadre">
Notez que <code>language: minimal</code> n'est pas la même chose que l'omission de la clé <code>language</code>, si vous le faites, la langue par défaut est définie sur Ruby.
</div>

<div class="tableau-accueil">
    <table class="tableau">
        <thead>
             <tr><th>RUBY</th><th>DÉFAUT</th></tr>
        </thead>
        <tbody>
            <tr>
                <td>Défaut<code>install</code></td>
                <td>N/A</td>
            </tr>
            <tr>
                <td>Défaut<code>script</code></td>
                <td>N/A</td>
            </tr>
            <tr>
                <td>Clé de matrice</td>
                <td>N/A<td>
            </tr>
            <tr>
                <td>Support</td>
                <td><a href="mailto:support@travis-ci.com?Subject=Minimal image">Travis CI</a></td>
            </tr>
        </tbody>
        <tfoot>
            <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
            <tr><th colspan="2" text-align="left">Exemple :</th></tr>
            <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
            <tr><th colspan="2">
            <div class="titre-code">
                <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
            </div>
```yml
language: minimal
```
            </th></tr>
                        <tr><th colspan="2">
            <div class="titre-code">
                <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
            </div>
```yml
language: generic
```
            </th></tr>
        </tfoot>
    </table>
</div>

<h2 id="valeurs-par-defaut">Valeurs par défaut.
<a href="#valeurs-par-defaut" class="ancre-titre after"></a></h2>

Comme ni `minimal` ni `generic` ne sont adaptés à une langue particulière, il n'y a pas de commandes `install` ou `script` par défaut, alors n'oubliez pas de les configurer dans votre `.travis.yml`.

<h2 id="minimal">Minimal.
<a href="#minimal" class="ancre-titre after"></a></h2>

L'image `minimal` contient :  

* [Outils de contrôle de version](./environnement-trusty.md#controle-de-version) ; 
* [outils de construction essentiels tels que GCC et faire](./environnement-trusty.md#Compilateurs et chaîne d'outils de compilation) ;
* [Outils réseau tels que Curl](./environnement-trusty.md#outils-reseaux) ; 
* [Docker](./environnement-trusty.md#docker) ; 
* [python](./environnement-trusty.md#image-python).

<h2 id="generique">Générique.
<a href="#generique" class="ancre-titre after"></a></h2>

L'image `generic` contient tout à partir de `minimal`, jusqu'aux bases de données, services et environnements d'exécution linguistiques habituels :

* [Outils de contrôle de version](./environnement-trusty.md#controle-de-version) ; 
* [outils de construction essentiels tels que GCC et faire](./environnement-trusty.md#Compilateurs et chaîne d'outils de compilation) ;
* [Outils réseau tels que Curl](./environnement-trusty.md#outils-reseaux) ; 
* [Docker](./environnement-trusty.md#docker) ; 
* [bases de données et services](./environnement-trusty.md#bdd-et-services) ; 
* [go](./environnement-trusty.md#images-go) ; 
 * [jvm](./environnement-trusty.md#jm-clojure) ; 
 * [node_js](./environnement-trusty.md#javascript) ; 
* [php](./environnement-trusty.md#images-php) ; 
* [rubis](./environnement-trusty.md#images-ruby).

Pour plus de détails sur le contenu de l'image, consultez la [mise à jour de l'environnement de construction](./historique-des-maj-environnement.md).

<h2 id="alias">Alias.
<a href="#alias" class="ancre-titre after"></a></h2>

Définir la clé `language` sur `bash`, `sh` ou `shell` équivaut à définir `language: minimal`.
<hr class="invisible">
