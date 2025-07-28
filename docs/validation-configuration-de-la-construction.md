# Validation de la configuration de la construction

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#option-opt-in2">L'option opt-in </a></li>
            <li><a href="#validation-de-la-configuration-de-build">Validation de la configuration de build </a></li>
            <li><a href="#messages-de-validation">Messages de validation</a></li>
            <li><a href="#types-de-messages-de-validation">Types de messages de validation</a></li>
        </ul>
    </div>
</div>
</p>

<div class="notice info">
<p>
<i class="fa fa-flag" aria-hidden="true"></i>&nbsp;
Cette page documente la nouvelle fonction de validation de configuration de construction qui est actuellement en version bêta.</p>
</div>

<h2 id="option-opt-in2">L'option opt-in.
<a href="#option-opt-in2" class="ancre-titre after"></a></h2>

Vous pouvez opter pour le paramètre de référentiel «_Build Config Validation_» (Validation de la configuration de construction) dans l'interface utilisateur Travis CI, ou en spécifiant la `version` dans votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
version: ~> 1.0
```

<h2 id="validation-de-la-configuration-de-build">Validation de la configuration de build.
<a href="#validation-de-la-configuration-de-build" class="ancre-titre after"></a></h2>

Lorsque la fonctionnalité de [validation de la configuration](https://github.com/travis-ci/travis-yml) de build est activée, elle valide et normalise toutes les sources de configuration de build entrantes (par exemple, votre fichier `.travis.yml` et les configurations provenant des demandes de build créées via l'API).

Il utilise une spécification de [format de configuration formelle](https://github.com/travis-ci/travis-yml/blob/master/schema.json) qui est disponible dans le [schéma JSON](https://json-schema.org/) dont le format est bien connu et soutenu. Cette fonctionnalité remplace tout outil de liaison et de validation précédent.

La documentation de référence autoogénérée à partir de cette spécification [peut être trouvée ici](https://config.travis-ci.com/).

Vous pouvez essayer et vérifier comment les sources de configuration de build YAML seront normalisées sur [l'explorateur de configuration de build Travis CI](https://config.travis-ci.com/explore)  (expérimental).

<h2 id="messages-de-validation">Messages de validation.
<a href="#messages-de-validation" class="ancre-titre after"></a></h2>

La validation des configurations de build produit des messages de validation que vous pouvez consulter sur l'onglet «_View Config_» (Afficher la Configuration) de la build correspondante dans l'interface utilisateur de Travis CI.

Cela vous donne un aperçu direct de la façon dont votre configuration de build a été traitée, des problèmes que Travis CI aurait pu trouver et comment les résoudre.

Les messages ont 4 niveaux de gravité : 

* `alert` (alerte) - La configuration de build peut faire des fuites de secrets. Vous devez accorder une attention particulière à ces messages ;
* `error` (erreur) - La configuration de build contient des erreurs sévères que notre système ne peut pas récupérer, il ignorera donc les sections de configuration respectives ;
* `warn` (avertissement) - La configuration de build contient des erreurs que notre système peut réparer. Il est certain d'ignorer ces messages. 
* `info` (information) - L'analyse de configuration de build a apporté des modifications à la configuration dont vous voudrez peut-être être informé. Il est certain d'ignorer ces messages.

<h2 id="types-de-messages-de-validation">Types de messages de validation.
<a href="#types-de-messages-de-validation" class="ancre-titre after"></a></h2>

Ce tableau répertorie les types de messages de validation de configuration et explique comment répondre à ces messages.

<p>
<table class="tableau">
    <thead>
        <tr>
            <th>Niveau</th><th>Clé</th><th>Signification</th><th>Action</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Alert</td><td><code>secure</code><td>La clé attend une valeur cryptée, mais une valeur non cryptée a été fournie. Cela pourrait entraîner une fuite d'informations d'identification.</td><td>Chiffrez le secret donné.</td>
        </tr>
        <tr>
            <td>Erreur</td><td><code>duplicate_key</code><td>La clé a été donnée plusieurs fois, seule la dernière valeur sera conservée, comme défini dans YAML.</td><td>Fusionnez les sections correspondant aux clés données en une seule.</td>
        </tr>
        <tr>
            <td>Erreur</td><td><code>invalid_condition</code><td>La condition n'a pas pu être analysée.</td><td>Corrigez la condition. Consultez la section <a href="./conditions-de-tests">conditions de test</a> pour savoir comment valider les conditions localement.</td>
        </tr>
        <tr>
            <td>Erreur</td><td><code>invalid_env_var</code><td>La variable d'environnement n'a pas pu être analysée.</td><td>Corrigez la variable d'environnement.</td>
        </tr>
        <tr>
            <td>Erreur</td><td><code>invalid_format</code><td>La valeur a un format invalide.</td><td>Corrigez le format utilisé.</td>
        </tr>
        <tr>
            <td>Erreur</td><td><code>invalid_type</code><td>La section a un type non valide et ne peut pas être utilisée.</td><td>Consultez la documentation relative à cette section de configuration et utilisez le type YAML approprié.</td>
        </tr>
        <tr>
            <td>Erreur</td><td><code>overwrite</code><td>Deux clés ont été fournies qui s'écrasent mutuellement.</td><td>N'utilisez qu'une seule des clés fournies et combinez leurs valeurs en une seule.</td>
        </tr>
        <tr>
            <td>Erreur</td><td><code>required</code><td>La clé requise n'a pas été fournie.</td><td>Spécifiez la clé.</td>
        </tr>
        <tr>
            <td>Erreur</td><td><code>unknown_value</code><td>La valeur est inconnue.</td><td>Supprimez la valeur ou utilisez la valeur équivalente connue.</td>
        </tr>
        <tr>
            <td>Avertissement</td><td><code>clean_key</code><td>La clé est inconnue et contient des caractères spéciaux. La suppression des caractères spéciaux permet d'obtenir une clé connue. La clé connue sera utilisée.</td><td>Supprimez les caractères spéciaux.<td>
        </tr>
        <tr>
            <td>Avertissement</td><td><code>clean_value</code><td>La clé est inconnue et contient des caractères spéciaux. La suppression des caractères spéciaux permet d'obtenir une clé connue. La clé connue sera utilisée.</td><td>Supprimez les caractères spéciaux.<td>
        </tr>
        <tr>
            <td>Avertissement</td><td><code>empty</code><td>La section était vide et sera supprimée.</td><td>Aucune action requise. Pour supprimer le message, supprimez la clé ou ajoutez du contenu.</td>
        </tr>
        <tr>
            <td>Avertissement</td><td><code>find_key</code><td>La clé n'est pas connue. Une autre clé similaire est connue, et celle-ci est supposée être une faute de frappe. L'autre clé sera utilisée.</td><td>Corrigez la faute de frappe ou supprimez la clé.</td>
        </tr>
        <tr>
            <td>Avertissement</td><td><code>strip_key</code><td>La clé contient des espaces. La suppression des caractères spéciaux donne une clé connue. La clé connue sera utilisée.</td><td>Supprimez les espaces.</td>
        </tr>
        <tr>
            <td>Avertissement</td><td><code>unexpected_seq</code><td>La clé ne s'attend pas à une séquence (tableau, liste). Il s'agit d'une erreur courante lors de l'utilisation de YAML, c'est pourquoi on suppose que la première entrée de la séquence représente la configuration souhaitée. La première valeur de la séquence sera utilisée.</td><td>Supprimez le tiret supplémentaire qui transforme la section en une séquence YAML.</td>
        </tr>
        <tr>
            <td>Avertissement</td><td><code>unknown_key</code><td>La clé est inconnue.</td><td>Supprimez la clé ou utilisez la clé équivalente connue.</td>
        </tr>
        <tr>
            <td>Avertissement</td><td><code>unknown_var</code><td>La variable du modèle n'est pas connue et peut entraîner une erreur ou une notification incorrecte.</td><td>Supprimez la variable ou utilisez une variable équivalente connue.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>alias_key</code><td>La clé est un alias pour une autre clé. La clé alias sera utilisée. Aucune action requise.</td><td>Pour supprimer le message, utilisez la clé alias.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>alias_value</code><td>La valeur est un alias pour une autre valeur. La valeur alias sera utilisée. Aucune action requise.</td><td>Pour supprimer le message, utilisez la valeur alias.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>default</code><td>La clé n'a pas été fournie, la valeur par défaut pour cette clé sera utilisée. Aucune valeur requise.</td><td>Pour supprimer le message, spécifiez une valeur.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>deprecated</code><td>Cette fonctionnalité est désormais obsolète.</td><td>Suivez les instructions indiquées dans le message.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>deprecated_key</code><td>La clé est désormais obsolète.</td><td>Utilisez la clé ou la solution mentionné dans le message.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>deprecated_value</code><td>La valeur est obsolète.</td><td>Utilisez la valeur ou la résolution mentionnée dans le message.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>downcase</code><td>La chaîne contient des lettres majuscules.</td><td>La version en minuscules de la chaîne sera utilisée.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>duplicate</code><td>La valeur est dupliquée. Aucune action requise. </td><td>Pour supprimer le message, utilisez des valeurs uniques.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>edge</code><td>Cette fonctionnalité est une fonctionnalité de bord. Aucune configuration n'est nécessaire.</td><td>Veuillez noter que le comportement de cette fonctionnalité peut changer ou disparaître.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>skip_allow_failure</code><td>La build a sauté une règle de d'autoriser les travaux car son état ne correspondait pas.</td><td>Ne necéssite aucune action.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>skip_exclude</code><td>La build a ignoré une règle d'exclusion de tâches car sa condition ne correspondait pas.</td><td>Ne necéssite aucune action.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>skip_import</code><td>La build a ignoré une importation car sa condition ne correspondait pas.</td><td>Ne necéssite aucune action.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>skip_job</code><td>La build a ignoré une tâche car sa condition n'était pas remplie.</td><td>Ne necéssite aucune action.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>skip_notification</code><td>La build a ignoré une cible de notification car sa condition n'était pas remplie.</td><td>Ne necéssite aucune action.</td>
        </tr>
        <tr>
            <td>Info</td><td><code>underscore_key</code><td>La clé contient des tirets. Le remplacement des tirets donne une clé connue. La clé connue sera utilisée.</td><td>Utilisez des traits de soulignement, pas des tirets.</td>
        </tr>
    </tbody>
</table>
</p>
<hr class="invisible">