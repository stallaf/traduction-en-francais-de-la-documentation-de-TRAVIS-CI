#  Vue d'ensemble

<div class="notice attention">
<p>
<i class="fa fa-flag" aria-hidden="true"></i><br>
Cette page documente les déploiements utilisant la version dpl v1, qui est actuellement la version héritée. La version dpl v2 est désormais disponible, et nous vous recommandons de l'utiliser. Veuillez consulter <a href="https://travis-ci.com/blog/2019-08-27-deployment-tooling-dpl-v2-preview-release">notre article de blog</a> pour plus de détails. La documentation relative à la <a href="https://docs.travis-ci.com/user/deployment-v2/">version dpl v2 est disponible ici</a>.
</div>

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#fournisseurs">Fournisseurs pris en charge</a></li>
            <li><a href="#telechargement">Téléchargement de fichiers et méthode skip_cleanup</a></li>
            <li><a href="#deploiement-fournisseurs">Déploiement vers plusieurs fournisseurs</a></li>
            <li><a href="#versions">Versions conditionnelles</a></li>
            <li><a href="#pr"><i>Pull Requests</i></a></li>
        </ul>
    </div>
</div>
</p>

<h2 id="fournisseurs">Fournisseurs pris en charge.
<a href="#fournisseurs" class="ancre-titre after"></a></h2>

Le déploiement continu vers les fournisseurs suivants est pris en charge :

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#anynines">anynines</a></li>
            <li><a href="#atlas">Atlas</a></li>
            <li><a href="#codedeploy">AWS CodeDeploy</a></li>
            <li><a href="#elastic">AWS Elastic Beanstalk</a></li>
            <li><a href="#lambda">AWS Lambda</a></li>
            <li><a href="#opsworks">AWS OpsWorks</a></li>
            <li><a href="#s3">AWS S3</a></li>
            <li><a href="#azure">Azure Web Apps</a></li>
            <li><a href="#bintray">bintray</a></li>
            <li><a href="#bitballon">BitBalloon</a></li>
            <li><a href="#bluemix">Bluemix CloudFoundry</a></li>
            <li><a href="#boxfuse">Boxfuse</a></li>
            <li><a href="#catalyze">Catalyze</a></li>
            <li><a href="#chef">Chef Supermarket</a></li>
            <li><a href="#cloud">Cloud 66</a></li>
            <li><a href="#cloundfoundry">CloudFoundry</a></li>
            <li><a href="#cargo">Cargo</a></li>
            <li><a href="#engine">Engine Yard</a></li>
            <li><a href="#github-pages">GitHub Pages</a></li>
            <li><a href="#github-releases">GitHub Releases</a></li>
            <li><a href="#google-app">Google App Engine</a></li>
            <li><a href="#google-cloud">Google Cloud Storage</a></li>
            <li><a href="#google-firebase">Google Firebase</a></li>
            <li><a href="#hackage">Hackage</a></li>
            <li><a href="#hephy">Hephy</a></li>
            <li><a href="#heroku">Heroku</a></li>
            <li><a href="#launchpad">Launchpad</a></li>
            <li><a href="#npm">npm</a></li>
            <li><a href="#openshift">OpenShift</a></li>
            <li><a href="#cloud-io">packagecloud.io</a></li>
            <li><a href="#puppet">Puppet Forge</a></li>
            <li><a href="#pypi">PyPI</a></li>
            <li><a href="#rackspace">Rackspace Cloud Files</a></li>
            <li><a href="#ruby">RubyGems</a></li>
            <li><a href="#scalingo">Scalingo</a></li>
            <li><a href="#script">Script</a></li>
            <li><a href="#snap">Snap Store</a></li>
            <li><a href="#surge">Surge.sh</a></li>
            <li><a href="#fairy">TestFairy</a></li>
            <li><a href="#transifex">Transifex</a></li>
        </ul>
    </div>
</div>
</p>

Pour déployer vers un fournisseur personnalisé ou non pris en charge, utilisez [l'étape de compilation après réussite](./deploiement-personnalise.md) ou le [fournisseur de script](./deploiement-script.md).

<h2 id="telechargement">Téléchargement de fichiers et méthode skip_cleanup.
<a href="#telechargement" class="ancre-titre after"></a></h2>

Lors du déploiement de fichiers chez un fournisseur, empêchez Travis CI de réinitialiser votre répertoire de travail et de supprimer toutes les modifications apportées lors de la construction (`git stash --all`) en ajoutant `skip_cleanup` à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  skip_cleanup: true
```

<h2 id="deploiement-fournisseurs">Déploiement vers plusieurs fournisseurs.
<a href="#deploiement-fournisseurs" class="ancre-titre after"></a></h2>

Le déploiement vers plusieurs fournisseurs est possible en les ajoutant à la section `deploy` en tant que liste. Par exemple, si vous souhaitez déployer sur cloudControl et Heroku, votre section `deploy` ressemblerait à ceci:

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  - provider: cloudcontrol
    email: "YOUR CLOUDCONTROL EMAIL"
    password: "YOUR CLOUDCONTROL PASSWORD"
    deployment: "APP_NAME/DEP_NAME"
  - provider: heroku
    api_key: "YOUR HEROKU API KEY"
```

<h2 id="versions">Versions conditionnelles.
<a href="#versions" class="ancre-titre after"></a></h2>

Définissez votre build sur _deploy_ uniquement dans des circonstances spécifiques en configurant la clé `on:` pour tout fournisseur de déploiement.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  provider: s3
  access_key_id: "YOUR AWS ACCESS KEY"
  secret_access_key: "YOUR AWS SECRET KEY"
  bucket: "S3 Bucket"
  skip_cleanup: true
  on:
    branch: release
    condition: $MY_ENV = super_awesome
```

Lorsque _toutes_ les conditions spécifiées dans la section `on:` sont remplies, votre build se déploiera.

Utilisez les options suivantes pour configurer le déploiement conditionnel : 

* `repo` : sous la forme `owner_name/repo_name`. Déploiement uniquement lorsque la version se produit sur un référentiel particulier. Par exemple : 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
 deploy:
   provider: s3
   on:
     repo: travis-ci/dpl
```

* `branch`: nom de la branche. Si ce paramètre est omis, la valeur par défaut est la branche spécifique à `app` ou `master`. Si le nom de la branche n'est pas connu à l'avance, vous pouvez spécifier `all_branches: true` _à la place de_ `branch:` et utiliser d'autres conditions pour contrôler votre déploiement.

* `jdk`, `node`, `perl`, `php`, `python`, `ruby`, `scala`, `go` : pour les environnements d'exécution de langage qui prennent en charge plusieurs versions, vous pouvez limiter le déploiement afin qu'il ne s'effectue que sur la tâche correspondant à une version spécifique.

* `condition`: déployer lorsqu'une _seule_ condition bash est évaluée comme `true`. Il doit s'agir d'une valeur de type chaîne de caractères, équivalente à `if [[ <condition> ]]; then <déployer>; fi`. Par exemple, `$CC = gcc`.

* `tag` peut être `true`, `false` ou toute autre chaîne : 
    * `tag: true`: le déploiement est déclenché si et seulement si `$TRAVIS_TAG` est défini. En fonction de votre flux de travail, vous pouvez définir `$TRAVIS_TAG` explicitement, même s'il s'agit d'une compilation sans balise lors de son lancement. Cela entraîne l'ignorance de la condition de `branch`.
    * `tags: false` : le déploiement est déclenché si et seulement si `$TRAVIS_TAG` est vide. Cela entraîne également l'ignorance de la condition de `branch`.
    * Lorsque `tag` n'est pas défini ou défini sur une autre valeur, `$TRAVIS_TAG` est ignoré et la condition de `branch` est prise en compte, si elle est définie.

<h3 id="conditionnel">Exemple de déploiement conditionnel.
<a href="#conditionnel" class="ancre-titre after"></a></h3>

Cet exemple se déplace sur Appfog uniquement à partir de la branche `staging` lorsque le test s'est exécuté sur Node.js version 0.11.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: node_js
deploy:
  provider: appfog
  user: ...
  api_key: ...
  on:
    branch: staging
    node_js: '0.11' # this should be quoted; otherwise, 0.10 would not work
```

L'exemple suivant se déploie à l'aide d'un script personnalisé `deploy.sh`, uniquement pour les builds des branches `staging` et `production`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  provider: script
  script: deploy.sh
  on:
    all_branches: true
    condition: $TRAVIS_BRANCH =~ ^(staging|production)$
```

L'exemple suivant se déploie à l'aide de scripts personnalisés `deploy_production.sh` et `deploy_staging.sh` en fonction de la branche qui a déclenché la tâche.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  - provider: script
    script: deploy_production.sh
    on:
      branch: production
  - provider: script
    script: deploy_staging.sh
    on:
      branch: staging
```

L'exemple suivant se déploie sur S3 uniquement lorsque `$` est défini sur `gcc`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  provider: s3
  access_key_id: "YOUR AWS ACCESS KEY"
  secret_access_key: "YOUR AWS SECRET KEY"
  skip_cleanup: true
  bucket: "S3 Bucket"
  on:
    condition: "$CC = gcc"
```

Cet exemple se déploie vers GitHub Releases lorsqu'une balise est définie et que la version Ruby est 2.0.0.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  provider: releases
  api_key: "GITHUB OAUTH TOKEN"
  file: "FILE TO UPLOAD"
  skip_cleanup: true
  on:
    tags: true
    rvm: 2.0.0
```

<h3 id="ajout-fournisseur">Ajouter un fournisseur de déploiement.
<a href="#ajout-fournisseur" class="ancre-titre after"></a></h3>

Nous travaillons sur l'ajout de support pour d'autres fournisseurs de PaaS. Si vous hébergez votre application chez un fournisseur non répertorié ici et que vous souhaitez que Travis CI déploie automatiquement votre demande, veuillez [nous contacter](mailto:support@travis-ci.com).

Si vous contribuez ou expérimentez [l'outil de déploiement](https://github.com/travis-ci/dpl), assurez-vous d'utiliser la version edge de GitHub :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
deploy:
  provider: awesome-experimental-provider
  edge: true
```

<h2 id="pr"><i>Pull Requests</i>.
<a href="#pr" class="ancre-titre after"></a></h2>

Notez que les builds de _pull request_ ignorent complètement l'étape de déploiement.
<hr class="invisible">