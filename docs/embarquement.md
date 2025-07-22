# Embarquement pour Travis CI

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#prerequis">Prérequis</a></li>
            <li><a href="#demarrer-avec-travis-ci">Démarrer avec Travis CI</a></li>
            <li><a href="#ajouter-un-fichier-travis-yaml">Ajouter un fichier .travis.yml</a></li>
            <li><a href="#changer-de-compte">Changer de compte</a></li>
            <li><a href="#selectionner-un-autre-langage-de-programmation">Sélectionner un autre langage de programmation</a></li>
            <li><a href="#pour-en-savoir-plus">Pour en savoir plus</a></li>
        </ul>
    </div>
</div>
</p>

<p>
Il s'agit d'un court guide pour utiliser Travis CI avec le référentiel de code hébergé par votre cloud. Si vous êtes nouveau dans une intégration continue ou si vous souhaitez plus d'informations sur ce que fait Travis CI, commencez par des concepts de base pour les débutants à la place.</p>

<h2 id="prerequis">Prérequis.
<a href="#prerequis" class="ancre-titre after" ></a></h2>

Pour commencer à utiliser Travis CI, assurez-vous d'avoir ce qui suit : 

* Un compte [GitHub](https://github.com/), [Assembla](https://www.assembla.com/), [Bitbucket](https://bitbucket.org/) ou [Gitlab](https://about.gitlab.com/). 
* Autorisations du propriétaire pour un projet hébergé sur [GitHub](https://help.github.com/categories/importing-your-projects-to-github/), [Assembla](https://articles.assembla.com/en/articles/1665737-advanced-user-permissions-controls), [Bitbucket](https://confluence.atlassian.com/bitbucket/transfer-repository-ownership-289964397.html) ou [GitLab](https://www.tutorialspoint.com/gitlab/gitlab_user_permissions.htm).

<h2 id="demarrer-avec-travis-ci">Démarrer avec Travis CI.
<a href="#demarrer-avec-travis-ci" class="ancre-titre after"></a></h2>

Commencez avec Travis CI, un service d'intégration continue utilisé pour tester et créer des projets logiciels hébergés sur [GitHub](https://github.com/), [Assembla](https://www.assembla.com/), [Bitbucket](https://bitbucket.org/) ou [GitLab](https://about.gitlab.com/). 

<p id="inscrivez-vous-a-travis-ci"><strong>1. Inscrivez-vous à Travis CI.</strong>
<a href="#inscrivez-vous-a-travis-ci" class="ancre-titre after"></a></p>
Allez sur [Travis-ci.com](https://app.travis-ci.com/) et [_connectez-vous avec votre compte préféré_](https://app.travis-ci.com/signin).

![Travis CI Connexion](https://docs.travis-ci.com/user/images/onboarding-travis-sign-in.png)

<p id="accepter-autorisation-de-travis-ci"><strong>2. Acceptez l'autorisation de Travis CI.</strong>
<a href="#accepter-autorisation-de-travis-ci" class="ancre-titre after"></a></p>
Une fois redirigé vers la plate-forme choisie, connectez-vous à votre compte et acceptez la demande d'autorisation de Travis CI.

Si vous avez des doutes sur les droits d'accès à Travis CI à votre plate-forme choisie, lisez plus de détails ici : 

* [L'application OAuth autorisée GitHub](https://docs.travis-ci.com/user/github-oauth-scopes/#travis-ci-github-oauth-app-access-rights) ;
* [Permissions GitHub utilisées par Travis CI](https://docs.travis-ci.com/user/github-oauth-scopes) ;
* [Autorisations d'assembla utilisées par Travis CI](https://docs.travis-ci.com/user/assembla-oauth-scopes/) ;
* [Permissions Bitbucket utilisées par Travis CI](https://docs.travis-ci.com/user/bb-oauth-scopes/) ;
* [Autorisations Gitlab utilisées par Travis CI](https://docs.travis-ci.com/user/gl-oauth-scopes/).

<p id="selectionner-un-plan"><strong>3. Sélectionnez un plan.</strong>
<a href="#selectionner-un-plan" class="ancre-titre after"></a></p>
Choisissez le meilleur plan pour vous ou commencez avec notre plan d'essai.

![Sélection de planification](https://docs.travis-ci.com/user/images/onboarding-select-plan.png)

<p id="validation-carte-de-credit"><strong>4. Validation de la carte de crédit.</strong>
<a href="#validation-carte-de-credit" class="ancre-titre after"></a></p>
Pour commencer le plan sélectionné, vous devez d'abord insérer vos informations de facturation et une carte de crédit valide.

![Écran de paiement ](https://docs.travis-ci.com/user/images/onboarding-payment.png)

<div class="note">
NOTE : <i>Pour les plans d'essai, l'autorisation des cartes de crédit peut entraîner une somme modique sur la carte pendant une courte durée. Les utilisateurs du plan d'essai se voient accorder une petite attribution de crédits d'essai à utiliser dans les 14 prochains jours</i>.
</div> 

<p id="configurations-de-parametres"><strong>5 . Configurations de paramètres.</strong>
<a href="#configurations-de-parametres" class="ancre-titre after"></a></p>
Dans votre tableau de bord Travis, cliquez sur votre photo de profil en haut à droite et sélectionnez l'option Paramètres pour voir une liste de vos référentiels disponibles. 

<p id="selectionner-des-referentiels"><strong>6 . Sélectionnez des référentiels.</strong>
<a href="#selectionner-des-referentiels" class="ancre-titre after"></a></p>
Cliquez sur le bouton **Gérer les référentiels**. Une fois dirigée vers la plate-forme choisie, sélectionnez les référentiels que vous souhaitez utiliser avec Travis CI.

![Gérer les référentiels](https://docs.travis-ci.com/user/images/onboarding-manage-repositories.png)

<div class="notice cadre">
<p>Selon votre plate-forme choisie, vous pouvez également effectuer cette sélection avec le message d'autorisation en cliquant sur le bouton Activer tous les référentiels de la page de démarrage pour activer tous vos références.</p>
</div>

<h3 id="utilisateurs-du-student-developper-pack-de-github">Utilisateurs du Student Developer Pack de GitHub.
<a href="#utilisateurs-du-student-developper-pack-de-github" class="ancre-titre after"></a></h3>

Si vous utilisez un compte GitHub et que vous êtes éligible pour le pack de développeurs Student GitHub, le plan Travis CI pourrait ne pas être attribué automatiquement. Cela se produit parce que l'utilisateur a déjà un plan attribué dans Travis CI.

Si vous souhaitez utiliser l'allocation gratuite pour le pack de développeurs Student GitHub à la place, veuillez [contacter notre équipe d'assistance](mailto:support@travis-ci.com), fournir votre enregistrement GitHub et demander l'affectation du plan respectif.

<h2 id="ajouter-un-fichier-travis-yaml">Ajouter un fichier .travis.yml.
<a href="#ajouter-un-fichier-travis-yaml" class="ancre-titre after"></a></h2>

L'étape suivante consiste à ajouter un fichier `.travis.yml` à votre référentiel pour dire à Travis CI quoi faire. L'exemple suivant spécifie un projet Ruby construit avec Ruby 2.2 et la dernière version de JRuby. 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
   language: ruby
   rvm:
    - 2.2
    - jruby
```

<div class="notice cadre">
<p>Les défauts des projets Ruby sont<code>bundle install</code><a href="https://docs.travis-ci.com/user/job-lifecycle/#customizing-the-installation-phase">pour installer les dépendances et</a><code>rake</code>pour construire le projet.</p>
</div>

Ajoutez le `.travis.yml` à un emplacement spécifique dans votre référentiel. 

<div class="notice cadre">
<p>Travis exécute uniquement les constructions sur les commits que vous poussez après avoir ajouté un fichier <code>.travis.yml</code>. Enfin, visitez <a href="https://app.travis-ci.com/">Travis CI</a> et sélectionnez votre référentiel pour vérifier la page d'état de construction pour voir si votre version <a href="https://docs.travis-ci.com/user/job-lifecycle/#breaking-the-build">passe ou échoue</a> en fonction de l'état de retour de la commande build.
</div>

<h3 id="configuration-du-fichier-assembla-travis-yaml">Configuration du fichier Assembla .travis.yml.
<a href="#configuration-du-fichier-assembla-travis-yaml" class="ancre-titre after"></a></h3>

<div class="notice info">
<p>
<i class="fa fa-flag" aria-hidden="true"></i><br>
Cette section présente la nouvelle option Assembla qui est actuellement en version bêta.</p>
</div>

Pour les utilisateurs d'Assembla, configurez le .travis.yml comme suit: 

1. Référentiel GIT: dans la racine des référentiels (`main` et branches). 
2. Référentiel SVN: 
      1. Dans le `/ trunk /` (par défaut c'est `/trunk/.travis.yml`) pour que les builds fonctionnent après les commits dans `trunk`. 
      2. Dans  `/branches/<branch name>/` (par exemple, `/branches/abc/.travis.yml` pour la branche nommée `abc`) pour que les builds s'exécutent après les commits d'une branche spécifique. 
3. P4 (Perforce Helix Core) Repository : 
      1. Dans le `/<potname>/main/` (par défaut c'est `/depot/main/.travis.yml`) pour que les constructions s'exécutent après avoir été soumises à `/<depotname>/main`. 
      2. Dans le répertoire correspondant au flux spécifique `/depot/<stream name>/`.travis.yml (par exemple, `/depot/abc/.travis.yml` pour le flux `abc`) pour les constructions à exécuter après les soumissions à un flux spécifique.

**IMPORTANT**

Le depot/repository Perforce peut être très lourd, de sorte que son téléchargement complet pour la construction (par exemple, des téraoctets de données) est souvent indésirable, car le code source à construire/tester n'est qu'une fraction de la taille totale du dépôt. Pour le télécharger partiellement, un utilisateur de Travis CI doit définir un sous-chemin spécifique, qui est ensuite téléchargé par la tâche de construction de Travis CI. De tels sous-chemins peuvent être définis en utilisant la balise `perforce_test_path` dans un fichier `.travis.yml`. Si la propriété n'est pas fournie, le comportement par défaut est le téléchargement de l'ensemble du depot/repository.

Voir l'exemple ci-dessous pour référence. 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
   dist: focal
   language: ruby
   rvm:
    - 2.2
    - jruby
   perforce_test_path: /your/subpath/within/repository/which/will/be/downloaded
```

Les constructions Performe et SVN sont actuellement **disponibles uniquement pour les environnements de construction d'architecture CPU Linux Ubuntu** et standard AMD64, à partir de la distribution Bionic Beaver Ubuntu.

<h2 id="changer-de-compte">Changer de compte.
<a href="#changer-de-compte" class="ancre-titre after"></a></h2>

Vous pouvez facilement passer d'un compte de fournisseur de plateforme cloud à un autre :

1. Cliquez sur l'icône de votre compte dans le coin supérieur droit de [Travis-ci.com](https://app.travis-ci.com/).
2. Sélectionnez le compte désiré et amusez-vous en utilisant Travis CI.
<hr class="invisible">

![Commuter les comptes](https://docs.travis-ci.com/user/images/onboarding-settings.png)

<h2 id="selectionner-un-autre-langage-de-programmation">Sélectionnez un autre langage de programmation.
<a href="#selectionner-un-autre-langage-de-programmation" class="ancre-titre after"></a></h2>

Utilisez l'une de ces langues communes :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
    language: ruby
```

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
    language: yaml
```

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
    language: java
```

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
    language: node_js
```

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
    language: python
```

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
    language: php
```

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
    language: go
```

Travis CI prend en charge de nombreux [langages de programmation](./langages.md).

<h2 id="pour-en-savoir-plus">Pour en savoir plus.
<a href="#pour-en-savoir-plus" class="ancre-titre after"></a></h2>

Vous trouverez plus d'informations utiles dans nos pages de tutoriels :

* [S'inscrire à Travis](https://youtu.be/IZJJxl9BkmA) ;
* [Démarrer avec Travis CI](https://youtu.be/_Og2kydTLWk) ;
* [Concepts de base pour les débutants](https://youtu.be/EER3AWu4sqM) ;
* [Tutoriels Travis CI](./tutoriels.md).
<hr class="invisible">

