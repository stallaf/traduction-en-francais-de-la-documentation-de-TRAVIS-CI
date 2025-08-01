# Dépendances privées BitBucket

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#cle-de-deploiement-bitbucket">Clé de déploiement</a></li>
            <li><a href="#cle-de-depot-bitbucket">Clé de dépôt</a></li>
            <li><a href="#mot-de-passe-bitbucket">Mot de passe</a></li>
            <li><a href="#jeton-api-bitbucket">Jeton API</a></li>
            <li><a href="#compte-utilisateur-dedie-bitbucket">Compte utilisateur dédié</a></li>
        </ul>
        </ul>
    </div>
</div>
</p>

_Certaines des fonctionnalités décrites ici ne **sont actuellement disponibles que pour les référentiels privés sur [Travis-ci.com](https://travis-ci.com/)**_.

Lorsque vous testez un référentiel privé, vous devrez peut-être attirer d'autres référentiels privés sous forme de dépendances via des [sous-modules Git](http://git-scm.com/book/en/Git-Tools-Submodules), un script personnalisé ou un outil de gestion des dépendances comme [Bundler](http://bundler.io/) ou [Composer](https://getcomposer.org/).

Les sous-modules Git doivent être clonés dès le début du processus de construction, et doivent donc utiliser la [clé de déploiement](#cle-de-deploiement) ou la méthode de la [clé utilisateur](#cle-utilisateur).

Si la dépendance se trouve également sur Bitbucket, il existe plusieurs façons de récupérer le référentiel à partir d'une machine virtuelle Travis CI. Chacune présente des avantages et des inconvénients, alors lisez attentivement chaque méthode et choisissez celle qui correspond le mieux à votre situation.

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
            <td>git@bitbucket.com/…</td>
            <td>Référentiel unique.</td>
            <td>Utilisé par défaut pour le référentiel principal.</td>
        </tr>
        <tr>
            <td>Clé utilisateur</td>
            <td>SSH</td>
            <td>git@bitbucket.com/…</td>
            <td>Tous les référentiels auxquels l'utilisateur a accès. </td>
            <td><strong>Recommandé</strong> pour les dependances.</td>
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

<h2 id="cle-de-deploiement-bitbucket">Clé de déploiement.
<a href="#cle-de-deploiement-bitbucket" class="ancre-titre after"></a></h2>

BitBucket vous permet de configurer des clés SSH pour un référentiel. Ces clés de déploiement présentent de grands avantages : 

* Elles ne sont pas liées à un compte utilisateur, elles ne seront donc pas invalidées en supprimant les utilisateurs d'un référentiel ;
* Elles ne donnent pas accès à d'autres référentiels non liés ;
* La même clé peut être utilisée pour les dépendances qui ne sont pas stockées sur Bitbucket.

Cependant, l'utilisation de clés de déploiement est compliquée par le fait que Bitbucket ne vous permet pas de réutiliser les clés. Ainsi, une seule clé privée ne peut pas accéder à plusieurs référentiels Bitbucket.

Vous pouvez inclure une clé privée différente pour chaque dépendance dans le référentiel, éventuellement en les chiffrant. La gestion de graphiques de dépendances complexes de cette manière peut s'avérer difficile à maintenir. C'est pourquoi nous vous recommandons d'utiliser plutôt une clé de référentiel.

<h2 id="cle-de-depot-bitbucket">Clé de dépôt.
<a href="#cle-de-depot-bitbucket" class="ancre-titre after"></a></h2>

Travis CI ajoutera une nouvelle clé d'accès pour votre référentiel. Il vous permettra de lire le contenu du fichier travis.yml.

<h2 id="mot-de-passe-bitbucket">Mot de passe.
<a href="#mot-de-passe-bitbucket" class="ancre-titre after"></a></h2>

Hypothèses : 

* Le référentiel pour lequel vous exécutez les builds est appelé «myorg/main» et dépend de «myorg/lib1» et «myorg/lib2» ;
* Vous connaissez les informations d'identification d'un compte d'utilisateur qui a au moins lu l'accès aux trois référentiels.

Pour extraire les dépendances avec un mot de passe, vous devrez utiliser le nom d'utilisateur et le mot de passe dans l'URL HTTPS Git : `https: // ci-user: mypassword123@github.com/myorg/lib1.git`.

Alternativement, vous pouvez également écrire les informations d'identification dans le fichier `~/.netrc` :

<div class="titre-code">
    <div class="item gauche">&nbsp;~/.netrc</div><div class="item droit">Bash</div>
</div>
```bash
machine bitbucket.com
    login ci-user
    password mypassword123
```

Vous pouvez également crypter le mot de passe, puis l'écrire sur le netrc dans une étape `before-install` dans votre `.travis.yml` :

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
- echo -e "machine bitbucket.com\n  login ci-user\n  password $CI_USER_PASSWORD" > ~/.netrc
```

Il est également possible d'injecter les informations d'identification dnas l' URL, par exemple, dans un Gemfile, cela ressemblerait à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;exemple.rb</div><div class="item droit">Ruby</div>
</div>
```ruby
source 'https://rubygems.org'
gemspec
if ENV['CI']
    # use HTTPS with password on Travis CI
    git_source :bitbucket do |repo_name|
        repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
        "https://ci-user:#{ENV.fetch("CI_USER_PASSWORD")}@bitbucket.com/#{repo_name}.git"
    end
end
gem 'lib1', bitbucket: "myorg/lib1"
gem 'lib2', bitbucket: "myorg/lib2"
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
  - echo -e "machine bitbucket.com\n  login ci-user\n  password $CI_USER_PASSWORD" >~/.netrc
  - git submodule update --init --recursive
```
</p>
</div>

<h2 id="jeton-api-bitbucket">Jeton API.
<a href="#jeton-api-bitbucket" class="ancre-titre after"></a></h2>

Hypothèses : 

* Le référentiel pour lequel vous exécutez les builds est appelé «myorg/main» et dépend de «myorg/lib1» et «myorg/lib2» ;
* Vous connaissez les informations d'identification d'un compte d'utilisateur qui a au moins lu l'accès aux trois référentiels.

Cette approche fonctionne comme l'approche de [mot de passe](#mot-de-passe) décrite ci-dessus, sauf qu'au lieu de la paire de nom d'utilisateur/mot de passe, vous utilisez un jeton API Bitbucket.

Accédez aux paramètres utilisateur du compte Bitbucket que vous souhaitez utiliser, puis générez un «Personal access tokens».

Assurez-vous que le jeton a la portée «repo».

Votre `~/.netrc` devrait ressembler à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;~/.netrc</div><div class="item droit">Bash</div>
</div>
```bash
machine bitbucket.com
    login the-generated-token
```

Vous pouvez également l'utiliser directement dans les URLs : `https: //the-generated-token@bitbucket.com/myorg/lib1.git`.

Utilisez la commande `encrypt` pour ajouter le jeton à votre `.travis.yml`.

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
$ travis env set CI_USER_TOKEN the-generated-token --private -r myorg/main
```

Vous pouvez ensuite demander à Travis CI d'écrire dans le fichier `~/.netrc` à chaque construction.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
- echo -e "machine bitbucket.com\n  login $CI_USER_TOKEN" > ~/.netrc
```

Il est également possible d'injecter le jeton dans l' URL, par exemple, dans un Gemfile, cela ressemblerait à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;exemple.rb</div><div class="item droit">Ruby</div>
</div>
```ruby
source 'https://rubygems.org'
gemspec
if ENV['CI']
    # use HTTPS with token on Travis CI
    git_source :bitbucket do |repo_name|
        repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
        "https://#{ENV.fetch("CI_USER_TOKEN")}@bitbucket.com/#{repo_name}.git"
    end
end
gem 'lib1', bitbucket: "myorg/lib1"
gem 'lib2', bitbucket: "myorg/lib2"
```

<div class="notice cadre">
Dans le cas des sous-modules git privés, sachez que la commande <code>git submodule update --init --recursive</code> s'exécute avant la mise à jour des informations d'identification <code>~/.netrc</code>. Si vous écrivez des informations d'identification à <code>~/.netrc</code>, désactivez le chargement automatique des sous-modules, mettez à jour les informations d'identification et ajoutez une étape explicite pour mettre à jour les sous-modules:  
<p>
<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
git:
    submodules: false
before_install:
  - echo -e "\n\nmachine bitbucket.com\n  $CI_TOKEN\n" >~/.netrc
  - git submodule update --init --recursive
```
</p>
</div>

<h2 id="compte-utilisateur-dedie-bitbucket">Compte d'utilisateur dédié.
<a href="#compte-utilisateur-dedie-bitbucket" class="ancre-titre after"></a></h2>

Comme mentionné à plusieurs reprises, il pourrait être logique de créer un utilisateur CI dédié pour les raisons suivantes : 

* L'utilisateur CI n'aura accès qu'aux référentiels auxquels vous souhaitez qu'il ait accès. 
* Vous pouvez limiter l'accès à l'accès en lecture. 
* Moins de risques en ce qui concerne les clés ou les références. 
* L'utilisateur CI ne quittera pas l'organisation pour des raisons non techniques et ne cassera accidentellement toutes vos versions.

Pour ce faire, vous devez vous inscrire sur Bitbucket comme si vous vous inscririez à un utilisateur normal. L'enregistrement des utilisateurs ne peut pas être automatisé, car cela violerait les conditions d'utilisation de Bitbucket.
<hr class="invisible">
