# Importer une configuration de build partagée

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#option-opt-in">L'option opt-in </a></li>
            <li><a href="#import-exemple">Importer l'exemple de configuration de build partagée </a></li>
            <li><a href="#sources-importation">Sources d'importation </a></li>
            <li><a href="#import-configurations-depuis-le-meme-referentiel">Importer des configurations à partir du même référentiel</a></li>
            <li><a href="#import-configurations-specifiques">Importer des versions de configuration spécifiques </a></li>
            <li><a href="#import-configurations-referentiel-prive">Importer des configurations de référentiel privé </a></li>
            <li><a href="#paratager-des-secrets-cryptes">Partager des secrets cryptés </a></li>
            <li><a href="#importations-conditionnelles">Importations conditionnelles </a></li>
            <li><a href="#modes-de-fusion">Modes de fusion </a></li>
            <li><a href="#priorites-importations">Priorités d'importations</a></li>
            <li><a href="#faq">FAQ</a></li>
        </ul>
    </div>
</div>
</p>

La principale source de configuration pour votre build est le fichier `.travis.yml` stocké dans votre référentiel. Vous pouvez importer des extraits de configuration partagés dans votre fichier `.travis.yml` ou dans la [charge utile de la requête de build API](./construction-de-declencheur-avec-api-3.md) afin de mettre à jour la configuration de votre build dans plusieurs référentiels en effectuant une seule modification.

Les configurations importées peuvent elles-mêmes inclure d'autres configurations, ce qui rend cette fonctionnalité très personnalisables (les importations cycliques seront ignorées). Vous pouvez importer jusqu'à 25 extraits de configuration de construction au total. 

<div class="notice info">
<p>
<i class="fa fa-flag" aria-hidden="true"></i>&nbsp;
BETA La fonction de construction Config Imports est actuellement en version bêta. Veuillez laisser des commentaires sur le <a href="https://travis-ci.community/c/early-releases">forum communautaire</a>.</p>
</div>

<h2 id="option-opt-in">L'option opt-in.
<a href="#option-opt-in" class="ancre-titre after"></a></h2>

Pour que cette fonctionnalité soit active, vous devez activer la fonctionnalité [Build Config Validation](./validation-configuration-de-la-construction.md) (Validation de la Configuration de Build), qui sera bientôt mise à la disposition de tous les utilisateurs.

Vous pouvez activer la validation de la configuration de build dans les paramètres de votre référentiel, ou en ajoutant `version: ~> 1.0` à votre fichier `.travis.yml`.

<h2 id="import-exemple">Importer l'exemple de configuration de build partagée.
<a href="#import-exemple" class="ancre-titre after"></a></h2>

Au lieu de spécifier les versions de Ruby à tester dans plusieurs fichiers dans de nombreux référentiels, vous pouvez les définir dans un extrait partagé :

<div class="titre-code">
    <div class="item gauche">&nbsp;rubies.yml</div><div class="item droit">YAML</div>
</div>
```yml
rvm:
    - 2.5
    - 2.6
```

Vous pouvez ensuite `import` cet extrait dans votre `.travis.yml`. La configuration suivante importe le fichier `rubies.yml` à partir de la branche `main` du référentiel `shared-configs` du compte `travis-ci` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
import: travis-ci/shared-configs:rubies.yml@main
script: bundle exec rake
```

résultant en la configuration suivante :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
rvm:
    - 2.5
    - 2.6
script: bundle exec rake
```

<h2 id="sources-importation">Sources d'importation.
<a href="#sources-importation" class="ancre-titre after"></a></h2>

Le format de la source `import` est `<account>/<repository>:<path>@<ref>` dans lequel `<ref>` peut être n'importe quelle référence Git valide, comme un commit sha, un nom de branche ou un nom de balise. 

<div class="notice cadre">
Les référentiels publics peuvent importer des sources à partir de référentiels publics, mais pas de référentiels privés. Ces derniers peuvent importer des sources à partir de référentiels publics et privés. Voir les référentiels privés pour plus d'informations.
</div>

Importer une seule source :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
import: travis-ci/build-configs:rubies.yml@main
#or
import:
    source: travis-ci/build-configs:rubies.yml@main
```

Importer plusieurs sources :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
import:
    - travis-ci/build-configs:rubies.yml@adf1235
    - travis-ci/build-configs:other.yml@v1
#or
import:
    - source: travis-ci/build-configs:rubies.yml@adf1235
    - source: travis-ci/build-configs:other.yml@v1
```

Par défaut, le mode de fusion `deep_merge_append` est utilisé pour combiner les clés présentes dans la configuration importée et dans une ou plusieurs configurations importées. Vous pouvez personnaliser ce paramètre en spécifiant le mode de fusion utilisé pour chaque importation. Voir ci-dessous pour plus d'informations sur les [modes de fusion](#modes-de-fusions).

<h2 id="import-configurations-depuis-le-meme-referentiel">Importez des configurations à partir du même référentiel.
<a href="#import-configurations-depuis-le-meme-referentiel" class="ancre-titre after"></a></h2>

Lors de l'importation de configurations stockées dans le même référentiel que votre `.travis.yml`, vous pouvez omettre la pièce `<account>/<repository>` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
# local imports fetch the same git commit ref
import:
    - one.yml  
    - path/to/other.yml
```

Le chemin est relatif à la racine du référentiel.

<h2 id="import-configurations-specifiques">Importer des versions de configuration spécifiques.
<a href="#import-configurations-specifiques" class="ancre-titre after"></a></h2>

Pour les configurations importées d'un autre référentiel, la dernière version de la branche par défaut du référentiel sera utilisée par défaut.

Pour les configurations importées du même référentiel, le commit que vous créez actuellement sera utilisé par défaut. Ceci est destiné à aider pendant que vous créez et testez les configurations partagées.

Vous pouvez spécifier la version exacte d'un extrait de configuration en utilisant n'importe quelle référence Git valide :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
import:
- one.yml@production
- travis-ci/build-configs/other.yml@v1.0.0
```

<h2 id="import-configurations-referentiel-prive">Importer des configurations de référentiel privé.
<a href="#import-configurations-referentiel-prive" class="ancre-titre after"></a></h2>

Pour partager des configurations **depuis** un référentiel privé, cela doit être autorisé sur ce référentiel en activant le paramètre _Allow importing config files from this repository_ (Autoriser l'importation de fichiers de configuration à partir de ce référentiel) dans `More options > Settings > Config Import`.

<div class="notice cadre">
Seuls des référentiels privés appartenant à la même organisation ou compte utilisateur pourront importer des extraits de configuration à partir de référentiels privés. Les configurations à partir de référentiels privés ne peuvent pas être importés dans des configurations à partir de référentiels publics.
</div>

<h2 id="paratager-des-secrets-cryptes">Partagez des secrets cryptés.
<a href="#paratager-des-secrets-cryptes" class="ancre-titre after"></a></h2>

Les secrets cryptés contenus dans les extraits de configuration importés peuvent être partagés et déchiffrés avec des référentiels appartenant au même compte d'organisation ou d'utilisateur.

Les configurations depuis des référentiels publics peuvent être importées dans des configurations à partir d'autres référentiels publics appartenant à une autre organisation ou compte d'utilisateur, mais les secrets cryptés contenus dans les configurations importées ne seront pas accessibles.

<h2 id="importations-conditionnelles">Importations conditionnelles.
<a href="#importations-conditionnelles" class="ancre-titre after"></a></h2>

Les importations de configuration peuvent être assorties d'une condition qui spécifie dans quelles circonstances la configuration importée doit être incluse.

Par exemple, avec cette configuration, le fichier local `.travis/master.yml` sera importé pour les builds sur la branche `master`, tandis que `.travis/other.yml` sera importé pour toutes les autres versions.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
import:
- source: .travis/master.yml
    if: branch = master
- source: .travis/other.yml
    if: branch != master
```

Veuillez consulter [Conditions](./conditions-v1.md) pour une spécification complète de la syntaxe des conditions.

<h2 id="modes-de-fusion">Modes de fusion.
<a href="#modes-de-fusion" class="ancre-titre after"></a></h2>

Le mode _merge_ (fusion) contrôle la manière dont les configurations importées sont fusionnées (combinées) dans la configuration d'importation. Un mode de fusion différent peut être spécifié pour chaque source de configuration importée.

Voici les modes de fusion : 

* `deep_merge_append` (par défaut) ;
* `deep_merge_prepennd` ;
* `deep_merge` ;
* `merge`.

Le mode de fusion par défaut est `deep_merge_append`.

<h3 id="deep-merge-append-prepend">Deep merge : append and prepend.
<a href="#deep-merge-append-prepend" class="ancre-titre after"></a></h3>

Les modes de fusion `deep_merge_append` et `deep_merge_prepennd` fusionnent de manière récursives les sections (clés) qui contiennent des cartes (hachages), et écrase les séquences (tableaux) en les ajoutant à la fin ou au début de la séquence dans la configuration d'importation.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
import:
- source: one.yml
    mode: deep_merge_append
- source: other.yml
    mode: deep_merge_prepend
```

<h3 id="deep-merge">Deep merge.
<a href="#deep-merge" class="ancre-titre after"></a></h3>

Le mode `deep_merge` fusionne récursivement des sections (cles) qui contiennent des cartes (hachages), mais écrasent les séquences (tableaux).

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
import:
- source: one.yml
    mode: deep_merge # deep merge
```

Ce mode fusionne d'abord le contenu de votre fichier `.travis.yml` dans le fichier `one.yml` (c'est-à-dire que les éléments du fichier .travis.yml « gagnent », si le mode de fusion deep_merge était utilisé, et écrasent les clés aux niveaux respectifs dans `one.yml`).

Respectivement :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
import:
    - source: one.yml
    mode: deep_merge # deep merge
    - source: two.yml
    mode: deep_merge # deep merge
```

Ce mode fusionne d'abord le contenu de votre fichier `.travis.yml` dans le fichier `one.yml` (en écrasant, si nécessaire, les sections du fichier `one.yml` avec le contenu du fichier `.travis.yml`). Les résultats sont fusionnés dans le fichier `two.yml` (là encore, les éléments du résultat de la fusion précédente l'emportent sur ceux de cette fusion, car le mode `deep_merge` est spécifié ici).

La raison derrière cela est que, dans de nombreux cas, lorsque vous importez quelque chose dans votre fichier `.travis.yml`, vous souhaitez pouvoir remplacer ou personnaliser cette configuration importée avec la configuration de votre fichier `.travis.yml`.

<h3 id="merge">Merge.
<a href="#merge" class="ancre-titre after"></a></h3>

Le mode de fusion `merge` effectue une fusion superficielle.

Cela signifie que les sections de niveau racine (clés) définies dans votre `.travis.yml` écraseront les sections (cles) de niveau racine qui sont également présentes dans le fichier importé.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
import:
- source: one.yml
    mode: merge # shallow merge
```

<h2 id="priorites-importations">Priorités d'importations.
<a href="#priorites-importations" class="ancre-titre after"></a></h2>

Lorsque vous déclenchez une build via l'API Travis ou l'interface utilisateur Web, l'ordre de priorité ascendante est : 

* Config à partir de la charge utile de la demande de build API, si elle est donnée ;
* Configs importées à partir de la charge utile de la demande de build de l'API, si elles sont données, dans la commande répertoriée (en suivant un modèle de recherche en profondeur d'abord au cas où les configurations importées importent d'autres configurations) ;
* Config de `.travis.yml` ; 
* Confits importées à partir de `.travis.yml`, dans l'ordre indiqué (en suivant un modèle de recherche en profondeur d'abord au cas où ces configurations importées importent d'autres configurations).

<h2 id="faq">FAQ.
<a href="#faq" class="ancre-titre after"></a></h2>

<h3 id="import-de-build-partagee">Puis-je importer une configuration de build partagée à un niveau de travail spécifique ?
<a href="#import-de-build-partagee" class="ancre-titre after"></a></h3>

Non. Les arborescences Yaml analysées doivent être fusionnées. Ainsi, le mot-clé `import` n'est accepté qu'au niveau racine. Si cela convient à votre scénario, vous pouvez spécifier votre modèle de travail dans, par exemple, `job.yml` et l'importer dans votre `.travis.yml` avec le `mode: deep_merge`, en ajoutant les spécificités `.travis.yml` à remplacer dans le modèle importé.

<h3 id="ancres-dans-configs-partagees">Puis-je créer et utiliser des ancres via le mécanisme des configurations partagées ?
<a href="#ancres-dans-configs-partagees" class="ancre-titre after"></a></h3>

Malheureusement, cela n'est pas pris en charge. Même si nous encourageons [l'utilisation de YAML comme langage de configuration de build](./configuration-de-build-avec-yaml.md
), les ancres et les alias, qui font référence à ces ancres, doivent être définis et utilisés dans un seul fichier `.yml` et seront développés avant toute action _d'importation_ (fusion des arbres d'analyse). Pour la même raison, les tentatives d'attribution d'une ancre dans `.travis.yml` dnas une clé _importée_ ne fonctionneront pas - `.travis.yml` et `imported.yml` doivent tous deux être analysés avant que l'action de fusion puisse avoir lieu.

Voir aussi les explications sur _native-API_ [dans le forum communautaire](https://travis-ci.community/t/imported-anchors-not-working/10035/2).
<hr class="invisible">
