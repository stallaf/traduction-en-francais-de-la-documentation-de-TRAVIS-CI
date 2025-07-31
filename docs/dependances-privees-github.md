# Dépendances privées GitHub

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#cle-de-deploiement">Clé de déploiement</a></li>
            <li><a href="#cle-utilisateur">Clé utilisateur</a></li>
            <li><a href="#mot-de-passe">Mot de passe</a></li>
            <li><a href="#jeton-api">Jeton API</a></li>
            <li><a href="#compte-utilisateur-dedie">Compte utilisateur dédié</a></li>
        </ul>
        </ul>
    </div>
</div>
</p>

_Certaines des fonctionnalités décrites ici ne **sont actuellement disponibles que pour les référentiels privés sur [Travis-ci.com](https://travis-ci.com/)**_.

Lorsque vous testez un référentiel privé, vous devrez peut-être attirer d'autres référentiels privés sous forme de dépendances via des [sous-modules Git](http://git-scm.com/book/en/Git-Tools-Submodules), un script personnalisé ou un outil de gestion des dépendances comme [Bundler](http://bundler.io/) ou [Composer](https://getcomposer.org/).

Les sous-modules Git doivent être clonés dès le début du processus de construction, et doivent donc utiliser la [clé de déploiement](#cle-de-deploiement) ou la méthode de la [clé utilisateur](#cle-utilisateur).

Si la dépendance est également sur GitHub, il existe quatre façons différentes de récupérer le référentiel à partir d'une VM Travis CI. Chacune d'entre elle présente des avantages et des inconvénients, alors lisez soigneusement chaque méthode et choisissez celle qui s'applique le mieux à votre situation.

<p>
<table class="tableau">
    <thead>
        <tr>
            <th>Authentification</th>
            <th>Protocole</th>
            <th>Format URL de dépendance</th>
            <th>Donne accès à</th>
            <th>Remarques</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Clé de déploiement</td>
            <td>SSH</td>
            <td>git@github.com/…</td>
            <td>Référentiel unique.</td>
            <td>Utilisé par défaut pour le référentiel principal.</td>
        </tr>
        <tr>
            <td>Clé utilisateur</td>
            <td>SSH</td>
            <td>git@github.com/…</td>
            <td>Tous les référentiels auxquels l'utilisateur a accès. </td>
            <td>Recommandé pourles <strong>dependances</strong>.</td>
        </tr>
        <tr>
            <td>Mot de passe</td>
            <td>HTTPS</td>
            <td>https: //…</td>
            <td>Tous les référentiels auxquels l'utilisateur a accès.</td>
            <td>Le mot de passe peut être crypté.</td>
        </tr>
        <tr>
            <td>Jeton API</td>
            <td>HTTPS</td>
            <td>https: //…</td>
            <td>Tous les référentiels auxquels l'utilisateur a accès.</td>
            <td>Le  jeton peut être crypté.</td>
        </tr>
    </tbody>
</table>
</p>

Vous pouvez utiliser un [compte utilisateur CI dédié](#compte-utilisateur-dedie) pour toutes les approches sauf celle utilisant la clé de déploiement. Cela vous permet de limiter l'accès à une liste bien définie de référentiels et de vous assurer que l'accès est en lecture seule.

<h2 id="cle-de-deploiement">Clé de déploiement.
<a href="#cle-de-deploiement" class="ancre-titre after"></a></h2>

GitHub vous permet de configurer des clés SSH pour un référentiel. Ces clés de déploiement présentent de grands avantages : 

* Ils ne sont pas liés à un compte utilisateur, ils ne seront donc pas invalidés en supprimant les utilisateurs d'un référentiel ;
* Ils ne donnent pas accès à d'autres référentiels non liés ;
* La même clé peut être utilisée pour les dépendances qui ne sont pas stockées sur GitHub.

Cependant, l'utilisation de clés de déploiement est compliquée car GitHub ne vous permet pas de réutiliser les clés. Ainsi, une seule clé privée ne peut pas accéder à plusieurs référentiels GitHub.

Vous pouvez inclure une clé privée différente pour chaque dépendance du référentiel, peut-être [les chiffrer](./fichiers-cryptes.md). Le maintien de graphiques de dépendance complexes de cette façon peut être difficile à maintenir. Pour cette raison, nous vous recommandons d'utiliser une [clé utilisateur](#cle-utilisateur) à la place.

<h2 id="cle-utilisateur">Clé utilisateur.
<a href="#cle-utilisateur" class="ancre-titre after"></a></h2>

_Les clés SSH personnalisées **ne sont actuellement disponibles que pour les référentiels privés sur [travis-ci.com](https://travis-ci.com/)**_.

Vous pouvez ajouter des clés SSH aux comptes utilisateurs sur GitHub. La plupart des utilisateurs ont probablement déjà fait cela pour pouvoir cloner les référentiels localement.

De cette façon, une seule clé peut accéder à plusieurs référentiels. Pour limiter la liste des référentiels et du type d'accès, il est recommandé de créer un [compte utilisateur CI dédié](#compte-utilisateur-dedie).

<h3 id="parametres-du-depot-fork">Paramètres du dépôt fork.
<a href="#parametres-du-depot-fork" class="ancre-titre after"></a></h3>

<div class="notice cadre">
Les paramètres de sécurité des dépôts pour les référentiels <i>forkés</i> sur Git sont disponibles depuis le 1er mars 2022.
</div>

Pour les dépôts Git, vous pouvez gérer pour chaque dépôt la manière dont les [variables d'environnement](./variables-environnement.md) et les (clés SSH personnalisées](#cle-utilisateur) seront traitées dans Travis CI lorsqu'une build est déclenchée à la suite d'une _Pull Request_ à partir d'un dépôt _forké_. Deux paramètres sont disponibles à cet effet, vous permettant de personnaliser votre configuration en matière de sécurité et de collaboration.

* _base repository_ - Un référentiel Git, qui est _forké_ par quelqu'un d'autre ;
* _fork_ ou _forked repository_ - Tout référentiel Git _forké_ à partir du référentiel de base ;
* _PR_ - _Pull Request_ (par exemple dans GitHub, BitBucket, GitLab) ou _Merge Request_ (en Assembla) .

<div class="notice cadre">
Veuillez noter : les référentiels activés dans <a href="https://app.travis-ci.com/">Travis CI</a> avant le 1er mars 2022 disposeront de <code>Share encrypted environment variables with forks (PRs)</code> réglé sur OFF. Veuillez vérifier votre modèle de collaboration si nécessaire (en particulier pour les référentiels publics). Les <code>Share SSH keys with forks (PRs)</code> seront définies sur ON pour que des référentiels privés ne rompent pas trop de configurations de collaboration. Les paramètres du référentiel seront définis par défaut sur OFF pour tout référentiel activé dans <a href="https://app.travis-ci.com/">Travis CI</a> après le <strong>1er mars 2022</strong>. Pour les référentiels activés dans Travis CI après le <strong>1er mars 2022</strong>, vous pouvez envisager de modifier les paramètres par défaut en fonction de votre modèle de collaboration.
</div>

<h3 id="paratager-cles-ssh-avec-fork">Partagez les clés SSH avec forks (PRS).
<a href="#paratager-cles-ssh-avec-fork" class="ancre-titre after"></a></h3>

<div class="notice cadre">
Veuillez noter : Le paramètre de référentiel «Share SSH Keys with Forks (PRS)» est applicable uniquement aux référentiels privés dans l'environnement <a href="https://app.travis-ci.com/">Travis-ci.com</a>.
</div>

Ce paramètre détermine si les clés SSH personnalisées du **référentiel de base** seront partagées avec le **référentiel _forké_** dans une _pull request_ d'un  _fork-to-base_ (les modifications sont fusionnées du référentiel _forké_ dans le référentiel de base). Dans le cas d'une _pull request_ de _base-to-base_ (les modifications sont fusionnées du référentiel de base en lui-même), les cles SSH personnalisées seront toujours disponibles.

Dans le cas d'une _pull request_ d'un _fork-to-fork_ (les modifications sont fusionnées du référentiel _forké_ en lui-même), les clés SSH personnalisées du référentiel de base ne seront jamais disponibles.

Dans le cas d'une _pull request_ d'un -fork-to-base_ : 

* Si ce paramètre est activé, les cles SSH personnalisées du référentiel de base seront disponibles pour le référentiel _forké_, ce qui signifie que la construction du référentiel _forké_ pourra utiliser les clés SSH personnalisées du référentiel de base. Envisagez de définir sur ON si votre modèle de collaboration nécessite de travailler avec des _pull requests_ (PRS) à partir de référentiels _forké_ ou il y a des dépendances définies, qui reposent sur la clé SSH du référentiel de base. 
* Si ce paramètre est réglé surt OFF et que la construction s'appuie sur des clés SSH personnalisées, c'est-à-dire pour récupérer certaines dépendances supplémentaires, elle échouera avec une erreur sans accès. 

<div class="notice cadre">
Veuillez noter : Dans <a href="https://app.travis-ci.com/">Travis-ci.com</a>, les secrets peuvent également être stockés dans des variables d'environnement cryptées, disponibles pour les référentiels publics et privés. En savoir plus [ici](./variables-environnement.md) sur les variables d'environnement cryptées.
</div>

<h3 id="utiliser-une-cle-existante">Utilisez une clé existante.
<a href="#utiliser-une-cle-existante" class="ancre-titre after"></a></h3>

![Ajout d'une clé SSH via l'interface Web](https://docs.travis-ci.com/images/2019-07-settings-ssh-key.png)

Hypothèses : 

* Le référentiel pour lequel vous exécutez les builds est appelé «myorg/main» et dépend de «myorg/lib1» et «myorg/lib2». 
* Vous avez déjà configuré une clé sur votre machine, par exemple sous `~/.ssh/id_rsa ` (par défaut sur les systèmes UNIX).

Vous pouvez ajouter une nouvelle clé à l'aide des paramètres du référentiel. Collez le contenu de `~/.ssh/id_rsa ` dans le champ de texte «Private Key» et donnez-lui une belle description.

Alternativement, vous pouvez utiliser la commande [CLI](https://github.com/travis-ci/travis.rb) suivante pour ajouter la clé à Travis CI :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
$ travis sshkey --upload ~/.ssh/id_rsa -r myorg/main
Key description: Key to clone myorg/lib1 and myorg/lib2
updating ssh key for myorg/main with key from ~/.ssh/id_rsa
Current SSH key: Key to clone myorg/lib1 and myorg/lib2
```

Vous pouvez omettre le `-r myorg/main` si votre répertoire de travail actuel est un clone du référentiel «myorg/main».

<h3 id="generer-une-nouvelle-cle">Générer une nouvelle clé.
<a href="#generer-une-nouvelle-cle" class="ancre-titre after"></a></h3>

Hypothèses : 

* Le référentiel pour lequel vous exécutez les builds est appelé «myorg/main» et dépend de «myorg/lib1» et «myorg/lib2» ;
* Vous connaissez les informations d'identification d'un compte d'utilisateur qui a au moins lu l'accès aux trois référentiels.

L'outil de ligne de commande `travis`` peut générer une nouvelle clé pour vous et la configurer sur Travis CI et GitHub. Pour ce faire, il vous demandera un nom d'utilisateur et un mot de passe GitHub, c'est très pratique si vous venez de créer un [utilisateur dédié](#compte-utilisateur-dedie) ou si vous n'avez pas de clé sur votre machine que vous souhaitez utiliser.

Les informations d'identification ne seront utilisées que pour accéder à GitHub et ne seront pas stockées ou partagées avec aucun autre service.

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
$ travis sshkey --generate -r myorg/main
We need the GitHub login for the account you want to add the key to.
This information will not be sent to Travis CI, only to api.github.com.
The password will not be displayed.
Username: ci-user
Password for ci-user: **************
Generating RSA key.
Uploading public key to GitHub.
Uploading private key to Travis CI.
You can store the private key to reuse it for other repositories (travis sshkey --upload FILE).
Store private key? |no|
Current SSH key: key for fetching dependencies for myorg/main
```

Vous pouvez omettre le `-r myorg/main` si votre répertoire de travail actuel est un clone du référentiel «myorg/main».

À la fin du processus, il vous demandera si vous souhaitez stocker la clé générée quelque part, il est généralement prudent de dire «non» ici. Après tout, vous pouvez simplement générer une nouvelle clé si nécessaire. Voir ci-dessous pour les instructions sur le stockage et la [réutilisation d'une clé générée](#reutiliser-une-cle-generee).

<h3 id="reutiliser-une-cle-generee">Réutiliser une clé générée.
<a href="#reutiliser-une-cle-generee" class="ancre-titre after"></a></h3>

Hypothèses : 

* Le référentiel pour lequel vous exécutez les builds est appelé «myorg/main» et dépend de «myorg/lib1» et «myorg/lib2» ;
* Vous connaissez les informations d'identification d'un compte d'utilisateur qui a au moins lu l'accès aux trois référentiels ;
* Vous souhaitez seulement générer une seule clé, vous pouvez donc la révoquer facilement ou l'utiliser pour accéder à d'autres dépendances d'origine ou de déploiement des cibles.

Ceci est absolument facultatif, rien ne vous empêche de générer de nouvelles clés pour tous les référentiels que vous testez.

Vous suivez les [étapes ci-dessus](#generer-une-nouvelle-cle), mais choisissez de stocker la clé. Il vous sera demander un chemin pour la stocker.

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
$ travis sshkey --generate -r myorg/main --description "CI dependencies"
We need the GitHub login for the account you want to add the key to.
This information will not be sent to Travis CI, only to api.github.com.
The password will not be displayed.
Username: ci-user
Password for ci-user: **************
Generating RSA key.
Uploading public key to GitHub.
Uploading private key to Travis CI.
You can store the private key to reuse it for other repositories (travis sshkey --upload FILE).
Store private key? |no| yes
Path: |id_travis_rsa| myorg_key
Current SSH key: CI dependencies
```

Et comme toujours, vous pouvez omettre le `-r myorg/main` si votre répertoire de travail actuel est un clone du référentiel «myorg/main».

Vous pouvez ensuite [télécharger](#utiliser-une-cle-existante) la clé pour myorg/main2 :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
$ travis sshkey --upload myorg_key -r myorg/main2 --description "CI dependencies"
updating ssh key for myorg/main with key from myorg_key
Current SSH key: CI dependencies
```

À partir de la version 1.7.0 de l'outil de ligne de commande `travis`, vous pouvez le combiner avec la commande `repos` pour configurer la clé non seulement pour «main» et «main2», mais tous les référentiels dependant de «myorg».

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
$ travis repos --active --owner myorg --com | xargs -I % travis sshkey --upload myorg_key -r % --description "CI dependencies"
updating ssh key for myorg/main with key from myorg_key
Current SSH key: CI dependencies
updating ssh key for myorg/main2 with key from myorg_key
Current SSH key: CI dependencies
updating ssh key for myorg/lib1 with key from myorg_key
Current SSH key: CI dependencies
updating ssh key for myorg/lib2 with key from myorg_key
Current SSH key: CI dependencies
```

<div class="notice cadre">
Notez que si vous utilisez toujours <a href="http://www.travis-ci.org/">travis-ci.org</a>, vous devez utiliser <code>--org</code> au lieu de <code>--com</code>.
</div>

<h2 id="mot-de-passe">Mot de passe.
<a href="#mot-de-passe" class="ancre-titre after"></a></h2>

Hypothèses : 

* Le référentiel pour lequel vous exécutez les builds est appelé «myorg/main» et dépend de «myorg/lib1» et «myorg/lib2» ;
* Vous connaissez les informations d'identification d'un compte d'utilisateur qui a au moins lu l'accès aux trois référentiels.

Pour extraire les dépendances avec un mot de passe, vous devrez utiliser le nom d'utilisateur et le mot de passe dans l'URL HTTPS Git : `https: // ci-user: mypassword123@github.com/myorg/lib1.git`.

Alternativement, vous pouvez également écrire les informations d'identification dans le fichier `~/.netrc` :

<div class="titre-code">
    <div class="item gauche">&nbsp;~/.netrc</div><div class="item droit">Bash</div>
</div>
```bash
machine github.com
    login ci-user
    password mypassword123
```

Vous pouvez également crypter le mot de passe, puis l'écrire  le netrc dans une étape `before_install` dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
$ travis env set CI_USER_PASSWORD mypassword123 --private -r myorg/main
```

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
- echo -e "machine github.com\n  login ci-user\n  password $CI_USER_PASSWORD" > ~/.netrc
```

Il est également possible d'injecter les informations d'identification dans l' URL, par exemple, dans un Gemfile, cela ressemblerait à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;exemple.rb</div><div class="item droit">Ruby</div>
</div>
```ruby
source 'https://rubygems.org'
gemspec
if ENV['CI']
    # use HTTPS with password on Travis CI
    git_source :github do |repo_name|
        repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
        "https://ci-user:#{ENV.fetch("CI_USER_PASSWORD")}@github.com/#{repo_name}.git"
    end
end
gem 'lib1', github: "myorg/lib1"
gem 'lib2', github: "myorg/lib2"
```

<div class="notice cadre">
Dans le cas de sous-modules git privés, sachez que la commande <code>git submodule update --init recursive</code> s'exécute avant la mise à jour des informations d'identification <code>~/.netrc</code>. Si vous écrivez des informations d'identification dans <code>~/.netrc</code>, désactivez le chargement automatique des sous-modules, mettez à jour les informations d'identification et ajoutez une étape explicite pour mettre à jour les sous-modules :
<p>
<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
git:
    submodules: false
before_install:
  - echo -e "machine github.com\n  login ci-user\n  password $CI_USER_PASSWORD" >~/.netrc
  - git submodule update --init --recursive
```
</p>
</div>

<h2 id="jeton-api">Jeton API.
<a href="#jeton-api" class="ancre-titre after"></a></h2>

Hypothèses : 

* Le référentiel pour lequel vous exécutez les builds est appelé «myor  main» et dépend de «myorg/lib1» et «myorg/lib2» ;
* Vous connaissez les informations d'identification d'un compte d'utilisateur qui a au moins lu l'accès aux trois référentiels.

Cette approche fonctionne comme l'approche du [mot de passe](#mot-de-passe) décrit ci-dessus, sauf qu'au lieu de la paire nom d'utilisateur/mot de passe, vous utiliserez un jeton API GitHub.

Sous les paramètres du compte GitHub pour l'utilisateur que vous souhaitez utiliser, accédez à [_Settings > Developer settings_](https://github.com/settings/developers), puis générez des «jetons d'accès personnels». Assurez-vous que le jeton a la portée «repo».

Votre `~/.netrc` devrait ressembler à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;~/.netrc</div><div class="item droit">Bash</div>
</div>
```bash
machine github.com
    login the-generated-token
```

Vous pouvez également l'utiliser directement dans les URL: `https: //the-generated-token@github.com/myorg/lib1.git`.

Utilisez la commande `encrypt` pour ajouter le jeton à votre `.travis.yml`.

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
$ travis env set CI_USER_TOKEN the-generated-token --private -r myorg/main
```

Vous pouvez ensuite faire écrire Travis CI au `~/.netrc` dans chaque build.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
- echo -e "machine github.com\n  login $CI_USER_TOKEN" > ~/.netrc

Il est également possible d'injecter le jeton dans l' URL, par exemple, dans un Gemfile, cela ressemblerait à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;exemple.rb</div><div class="item droit">Ruby</div>
</div>
```ruby
source 'https://rubygems.org'
gemspec
if ENV['CI']
    # use HTTPS with token on Travis CI
    git_source :github do |repo_name|
        repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
        "https://#{ENV.fetch("CI_USER_TOKEN")}@github.com/#{repo_name}.git"
    end
end
gem 'lib1', github: "myorg/lib1"
gem 'lib2', github: "myorg/lib2"
```

<div class="notice cadre">
Dans le cas des sous-modules Git privés, sachez que la commande <code>git submodule update --init --recursive</code> s'exécute avant la mise à jour des informations d'identification <code>~/.netrc</code>. Si vous écrivez des informations d'identification à <code>~/.netrc</code>, désactivez le chargement automatique des sous-modules, mettez à jour les informations d'identification et ajoutez une étape explicite pour mettre à jour les sous-modules : 
<p>
<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
git:
    submodules: false
before_install:
  - echo -e "\n\nmachine github.com\n login $CI_USER_TOKEN\n" >~/.netrc
  - git submodule update --init --recursive
```
</p>
</div>

<div class="notice cadre">
Le fichier <code>.netrc</code> est supprimé pour des raisons de sécurité immédiatement après avoir cloné le référentiel à partir duquel la build et ses sous-modules sont exécutés !
</div>

<h2 id="compte-utilisateur-dedie">Compte utilisateur dédié.
<a href="#compte-utilisateur-dedie" class="ancre-titre after"></a></h2>

Comme mentionné à plusieurs reprises, il peut être judicieux de créer un utilisateur CI dédié pour les raisons suivantes :

* L'utilisateur CI n'aura accès qu'aux référentiels auxquels vous souhaitez lui donner accès ;
* Vous pouvez limiter l'accès à un accès en lecture seule ;
* Moins de risques en cas de fuite de clés ou d'identifiants ;
* L'utilisateur CI ne quittera pas l'organisation pour des raisons non techniques et ne perturbera pas accidentellement toutes vos constructions.

Pour ce faire, vous devez vous inscrire sur GitHub comme si vous vous inscriviez en tant qu'utilisateur normal. L'inscription des utilisateurs ne peut pas être automatisée, car cela enfreindrait les conditions d'utilisation de GitHub.
<hr class="invisible">

