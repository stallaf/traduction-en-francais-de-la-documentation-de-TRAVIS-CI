# Déploiement avec Atlas

<div class="notice attention">
<p>
<i class="fa fa-flag" aria-hidden="true"></i><br>
Cette page documente les déploiements utilisant la version dpl v1, qui est actuellement la version héritée. La version dpl v2 est désormais disponible, et nous vous recommandons de l'utiliser. Veuillez consulter <a href="https://travis-ci.com/blog/2019-08-27-deployment-tooling-dpl-v2-preview-release">notre article de blog</a> pour plus de détails. La documentation relative à la <a href="https://docs.travis-ci.com/user/deployment-v2/">version dpl v2 est disponible ici</a>.
</div>

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#inclure-ou-exclure-des-fichiers">Inclure ou exclure des fichiers</a></li>
            <li><a href="#autres-options-de-deploiement">Autres options de déploiement</a></li>
            <li><a href="#executer-des-commandes-avant-ou-apres-le-deploiement">Exécuter des commandes avant ou après le déploiement</a></li>
        </ul>
    </div>
</div>
</p>

Travis CI peut déployer automatiquement votre application sur [Atlas](https://atlas.hashicorp.com/) après une compilation réussie.

<div class="notice cadre">
Hashicorp <a href="https://www.hashicorp.com/blog/hashicorp-terraform-enterprise-general-availability#decommissioning-atlas">a annoncé</a> qu'Atlas était mis hors service d'ici le 30 mars 2017. Il est remplacé par Terraform Enterprise.
</div>

Pour déployer votre application sur Atlas : 

* Connectez-vous à votre compte Atlas ;
* Générez un jeton API Atlas pour Travis CI ;
* Ajoutez la configuration minimale suivante à votre `.travis.yml` : 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  provider: atlas
  token: "YOUR ATLAS API TOKEN"
  app: "YOUR ATLAS USERNAME/YOUR ATLAS APP NAME"
```

<h2 id="inclure-ou-exclure-des-fichiers">Inclure ou exclure des fichiers.
<a href="#inclure-ou-exclure-des-fichiers" class="ancre-titre after"></a></h2>

Vous pouvez inclure et exclure des fichiers en ajoutant l'inclusion et exclure les entrées à `.travis.yml`. Les deux sont des modèles globaux de fichiers ou de répertoires pour inclure ou exclure, et peuvent être spécifiés plusieurs fois. S'il y a un conflit, les exclusions ont la priorité.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  provider: atlas
  exclude: "*.log"
  include:
   - "build/*"
   - "bin/*"
```

<h2 id="utilisez-le-systeme-de-controle-de-version">Utilisez le système de contrôle de version.
<a href="#utilisez-le-systeme-de-controle-de-version" class="ancre-titre after"></a></h2>

Obtenez les listes de fichiers à exclure et inclure de votre système de contrôle de version (Git, Mercurial ou Subversion) :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  provider: atlas
  vcs: true
```

<h2 id="autres-options-de-deploiement">Autres options de déploiement.
<a href="#autres-options-de-deploiement" class="ancre-titre after"></a></h2>

La section suivante répertorie les autres options de déploiement disponibles.

<h3 id="specifier-adresse-du-serveur-atlas">Spécifier l'adresse du serveur Atlas.
<a href="#specifier-adresse-du-serveur-atlas" class="ancre-titre after"></a></h3>

Utilisez le code suivant pour spécifier l'adresse du serveur Atlas :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
   provider: atlas
   address: "URL OF THE ATLAS SERVER"
```

<h3 id="ajouter-des-metadonnees-personnalisees">Ajouter des métadonnées personnalisées.
<a href="#ajouter-des-metadonnees-personnalisees" class="ancre-titre after"></a></h3>

Ajoutez un ou plusieurs articles de métadonnées :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  provider: atlas
  metadata:
    - "custom_name=Jane"
    - "custom_surname=Doe"
```

<h3 id="deploiements-conditionnels">Déploiements conditionnels.
<a href="#deploiements-conditionnels" class="ancre-titre after"></a></h3>

Vous ne pouvez déployer que lorsque certaines conditions sont remplies. Voir les [versions conditionnelles avec `on:`](https://docs.travis-ci.com/user/deployment#conditional-releases-with-on).

<h2 id="executer-des-commandes-avant-ou-apres-le-deploiement">Exécuter des commandes avant ou après le déploiement.
<a href="#executer-des-commandes-avant-ou-apres-le-deploiement" class="ancre-titre after"></a></h2>

Parfois, vous souhaitez exécuter des commandes avant ou après le déploiement. Vous pouvez utiliser les étapes `before_deploy` et `after_deploy` pour cela. Celles-ci ne seront déclenchées que si Travis CI se déploie réellement.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_deploy: "echo 'ready?'"
deploy:
  # ⋮
after_deploy:
  - ./after_deploy_1.sh
  - ./after_deploy_2.sh
```
<hr class="invisible">