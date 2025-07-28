# Construction de _Pull Requests_ (_PR_)

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#comment-les-pr-sont-construites">Comment les <i>PR</i> sont construites ?</a></li>
            <li><a href="#pr-et-restrictions-de-securite"><i>PR</i> et restrictions de sécurité</a></li>
            <li><a href="#la-pr-ne-se-construit-pas">La <i>PR</i> ne se construit pas</a></li>
            <li><a href="#double-construction-sur-les-pr">Double construction sur les <i>PR</i></a></li>
            <li><a href="#pour-en-savoir-plus">Pour en savoir plus</a></li>
        </ul>
    </div>
</div>
</p>

Les builds de _PR_ sont une partie essentielle de Travis CI. Chaque fois qu'une _PR_ est ouverte sur GitHub, Travis CI la construit et met à jour l'icône d'état sur la page de _PR_.

Vous pouvez identifier si une _PR_ a été construite alors qu'elle était considérée comme brouillon par le contributeur en regardant la balise `DRAFT` dans l'interface web. Consultez le fonctionnement des [événements de _PR_ de brouillon](https://github.blog/2019-02-14-introducing-draft-pull-requests/) sur GitHub.

<h2 id="comment-les-pr-sont-construites">Comment les <i>PR</i> sont construites ?
<a href="#comment-les-pr-sont-construites" class="ancre-titre after"></a></h2>

Lorsqu'une _PR_ est ouverte sur GitHub, Travis CI reçoit une notification et lance un build. Pendant la construction, nous mettons à jour l'icône d'état de la demande d'extraction vers l'un des états suivants :

* un avertissement que la construction est toujours en cours ;
* une notification que la construction a échoué - la demande ne doit pas être fusionnée ;
* une notification que la construction a réussi - la demande peut être fusionnée.

Travis CI construit une _PR_ lorsqu'elle est ouverte pour la première fois, et à chaque fois que des _commits_ sont ajoutés à la _PR_. Plutôt que de construire les _commits_ qui ont été poussés sur la branche d'où provient la _PR_, nous construisons la fusion entre la branche source et la branche en amont.

Pour ne construire que sur les événements _push_ et non sur les _PR_, désactivez **_Build on Pull Requests_**  dans les paramètres de votre référentiel.

Pour construire uniquement les _PR_ ciblant les branches spécifiques, vous pouvez utiliser la [clé `branches: only:`](./personnalisation-de-la-construction.md#build-branches-specifiques), qui restreindra également les branches qui déclenchent les constructions.

<h2 id="pr-et-restrictions-de-securite"><i>PR</i> et restrictions de sécurité.
<a href="#pr-et-restrictions-de-securite" class="ancre-titre after"></a></h2>

La restriction la plus importante pour les _PR_ concerne les variables d'environnement sécurisées et les données chiffrées.

Une _PR_ envoyée à partir d'un _fork_ du référentiel en amont (nous l'appelons une «_PR_  externe») pourrait être manipulée pour exposer les variables d'environnement. Le responsable du référentiel en amont n'aurait aucune protection contre cette attaque, car les _PRs_ peuvent être envoyées par quiconque alimente le référentiel sur GitHub.

Travis CI fabrique des variables cryptées et des données disponibles uniquement pour les _PRs_ provenant du même référentiel («_PRs_ internes»). Celles-ci sont considérées comme dignes de confiance, car les seuls membres ayant un accès en écriture au référentiel peuvent les envoyer.

Les _PRs_ envoyées à partir de référentiels faisant l'objet d'un _fork_ n'ont pas accès à des variables ou des données cryptées, même si celles-ci sont définies dans le projet _fork_ source  à moins que certains paramètres de référentiel dans Travis CI ne soient définis.

<h3 id="parametres-fork-du-referentiel">Paramètres <i>fork</i> du référentiel.
<a href="#parametres-fork-du-referentiel" class="ancre-titre after"></a></h3>

<div class="notice cadre">
Les paramètres de sécurité des référentiels ayant fait l'objet d'un _fork_ sur Git sont disponibles à partir du 1er mars 2022.
</div>

Pour les référentiels GIT, vous pouvez gérer par référentiel comment les [variables d'environnement](./variables-environnement.md) et les [clés SSH personnalisées](./dependances-privees.md) seront traitées dans Travis CI lorsqu'une build s'est déclenchée comme un effet de dépôt d'une _PR_ d'un référentiel aynt fait l'objet d'un _fork_. Deux paramètres sont disponibles spécifiquement à cet effet, vous permettant de personnaliser votre configuration de sécurité et de collaboration. 

* **référentiel de base** - un référentiel Git, qui a fait l'objet d'un _fork_ par quelqu'un d'autre ; 
* **référentiel _forké_** - tout référentiel Git ayant fait l'objet d'un _fork_ à partir du référentiel de base ;
* **PR** - demande de traction (par exemple dans Github, Bitbucket, Gitlab) ou demande de fusion (en Assembla) .

<div class="notice cadre">
Veuillez noter: Les dépôts activés dans <a href="https://app.travis-ci.com/">Travis CI</a> avant le 1er mars 2022 auront le paramètre <code>Share encrypted environment variables with forks (PRs)</code> réglé sur OFF. Veuillez vérifier votre modèle de collaboration si nécessaire (en particulier pour les dépôts publics). Le paramètre <code>Share SSH keys with forks (PRs)</code>sera mis sur ON pour les dépôts privés afin de ne pas briser trop de configurations de collaboration. Les paramètres du dépôt seront réglés par défaut sur OFF pour tout dépôt activé dans <a href="https://app.travis-ci.com/">Travis CI</a> après le <strong>1er mars 2022</strong>. Pour les dépôts activés dans Travis CI après le <strong>1er mars 2022</strong>, vous pouvez envisager de changer les paramètres par défaut en fonction de votre modèle de collaboration.
</div>

<h3 id="partagez-variables-avec-des-forks">Partagez des variables d'environnement chiffrées avec des <i>forks</i> (PRS).
<a href="#partagez-variables-avec-des-forks" class="ancre-titre after"></a></h3>

Ce paramètre détermine si les variables d'environnement cryptées du référentiel de base seront partagées avec le référentiel forké dans le cadre d'une  _fork-to-base pull request_ (le référentiel forké fusionne les modifications dans le référentiel de base).

Dans le cas d'une _PR_ de base à base (les modifications sont fusionnées du **référentiel de base** en lui-même), les variables d'environnement chiffrées seront toujours disponibles. Cela permet aux collaborateurs de déposer des _PR_ à l'aide des variables d'environnement cryptées du référentiel et de préserver les vérifications existantes configurées, par exemple dans GitHub.

Dans le cas d'une _PR_ de _fork_ à _fork_ (les changements sont fusionnés à partir du **dépôt forké** dans lui-même), les variables d'environnement cryptées du **dépôt de base** ne seront jamais disponibles. Il faut donc les remplacer ou les contourner directement dans le **dépôt forké**.

Dans le cas d'un  _fork-to-base pull request_ :

* Si ce paramètre est activé, les variables d'environnement cryptées seront disponibles pour le dépôt forké, ce qui signifie que les builds du **dépôt forké** auront accès aux variables d'environnement cryptées du **dépôt de base**. Il s'agit d'une approche moins sûre, mais qui permet une collaboration à l'aide des forks et des Pull Requests (PRs).
* Si ce paramètre est désactivé (OFF) et que la buil s'appuie sur une variable d'environnement chiffrée, la _PR_ du _**fork**_ vers le **dépôt de base** échouera. Cela permet de sécuriser les variables d'environnement cryptées de votre **dépôt de base** en imposant une contrainte sur leur accès depuis les **forks**.

<h3 id="partagez-cles-ssh-avec-des-forks">Partagez les clés SSH avec les <i>forks</i> (PRs).
<a href="#partagez-cles-ssh-avec-des-forks" class="ancre-titre after"></a></h3>

<div class="notice cadre">
Veuillez noter : Le paramètre de référentiel «Share SSH Keys with Forks (PRS)» est applicable uniquement aux référentiels privés dans l'environnement <a href="https://app.travis-ci.com/">travis-ci.com</a>.
</div>

Ce paramètre détermine si les touches SSH personnalisées du **référentiel de base** seront partagées avec le **référentiel _forké_** dans une _fork-to-base pull request_ (les modifications sont fusionnées du référentiel _forké_ dans le référentiel de base). Dans le cas d'une _base-to-base pull request_  (les modifications sont fusionnées du référentiel de base en lui-même), les touches SSH personnalisées seront toujours disponibles.

Dans le cas d'une _fork-to-fork pull request_ (les modifications sont fusionnées du référentiel _forké__ en lui-même), les clés SSH personnalisées du référentiel de base ne seront jamais disponibles.

Dans le cas d'une _fork-to-base pull request_ :

* Si ce paramètre est activé, les touches SSH personnalisées du **référentiel de base** seront disponibles pour le **référentiel _forké_**, ce qui signifie que la construction de ce  référentiel pourra utiliser les clés SSH personnalisées du référentiel de base. Envisagez de définir le régalge sur ON si votre modèle de collaboration nécessite de travailler avec des _PRs_ à partir de référentiels _forké_ ou il y a des dépendances définies, qui reposent sur la clé SSH du référentiel de base. 
* Si ce paramètre est désactivé et que la construction s'appuie sur des clés SSH personnalisées, par exemple pour récupérer des dépendances supplémentaires, elle échouera avec une erreur d'impossibilité d'accès.

Si vous avez des paramètres concernant le partage des secrets avec des _forks_ désactivées et que votre build repose sur des variables cryptées à exécuter, par exemple pour exécuter les tests de Sélénium avec [BrowSertack](https://www.browserstack.com/) ou [Sauce Labs](https://saucelabs.com/), votre build doit en tenir compte. Vous ne pourrez pas exécuter ces tests pour les _PR_ des contributeurs externes.

Pour contourner cela, ne limitez ces tests qu'aux situations où les variables d'environnement sont disponibles, ou désactivez entièrement les _PRs_, comme le montre l'exemple suivant :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script:
   - 'if [ "$TRAVIS_PULL_REQUEST" != "false" ]; then bash ./travis/run_on_pull_requests; fi'
   - 'if [ "$TRAVIS_PULL_REQUEST" = "false" ]; then bash ./travis/run_on_non_pull_requests; fi'
```

<h2 id="la-pr-ne-se-construit-pas">La <i>PR</i> ne se construit pas.
<a href="#la-pr-ne-se-construit-pas" class="ancre-titre after"></a></h2>

Si une _PR_ n'est pas construite ou n'apparaît pas dans l'interface utilisateur de Travis CI, cela signifie généralement qu'elle ne peut pas être fusionnée. Nous comptons sur le _merge commit_ que GitHub crée de manière transparente entre les changements dans la branche source et la branche en amont contre laquelle la _PR_ est envoyée.

Ainsi, lorsque vous créez ou mettez à jour une _PR_ et que Travis CI ne crée pas de build pour cela, assurez-vous que la _PR_ est fusionable. Si ce n'est pas le cas, rebasez-la sur la branche amont et résolvez les conflits de fusion. Lorsque vous apporterez les correctifs à la _PR_, Travis CI les construira avec plaisir.

Travis CI ne construit pas non plus les _PR_ lorsque la branche en amont est mise à jour, car cela conduirait à un nombre excessif de nouvelles versions.

Si la _PR_ a déjà été fusionnée, vous ne pouvez pas relancer le travail. Vous obtiendrez une erreur comme :

```console
The command "eval git fetch origin +refs/pull/994/merge: " failed
```

La restauration de la branche d'une _PR_ fusionnée ne déclenchera pas de build, pas plus que l'ajout d'un nouveau commit à une branche qui a déjà été fusionnée.

<h2 id="double-construction-sur-les-pr">Double construction sur les <i>PRs</i>.
<a href="#double-construction-sur-les-pr" class="ancre-titre after"></a></h2>

Si vous voyez deux icônes d'état de build sur votre _PR_ GitHub, cela signifie qu'il y a une build pour la branche et une build pour la _PR_ elle-même (en fait la build pour la fusion de la branche _head_ avec la branche de base spécifiée dans la _PR_).

Construire les branches poussées et construire les _PRs_ poussées contrôlent ce comportement.

<h2 id="pour-en-savoir-plus">Pour en savoir plus.
<a href="#pour-en-savoir-plus" class="ancre-titre after"></a></h2>

* [Construire uniquement le dernier commit](./personnalisation-de-la-construction.md#build-du-dernier-commit-uniquement) ;
* [Construire des branches spécifiques](./personnalisation-de-la-construction.md#build-branches-specifiques).
<hr class="invisible">

