# Utilisation de Docker dans votre build

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#utiliser-docker-depuis-un-referentiel">Utilisez une image Docker à partir d'un référentiel</a></li>
            <li><a href="#image-docker-a-partir-du-dockerfile">Construire une image docker à partir d'un dockerfile </a></li>
            <li><a href="#pousser-une-image-docker-vers-un-registre">Poussez une image Docker vers un registre </a></li>
            <li><a href="#utilisez-docker-compose">Utilisez Docker Compose</a></li>
            <li><a href="#installer-version-docker-recente">Installer une version docker plus récente</a></li>
            <li><a href="#exemples">Exemples</a></li>
        </ul>
    </div>
</div>
</p>

<div class="notice cadre">
Veuillez noter qu'en raison de <a href="https://docs.docker.com/docker-hub/download-rate-limit/">l'annonce de la limite de taux Docker</a> à venir, les utilisateurs devront ajouter leurs propres informations d'authentification à leurs paramètres de build ou la configuration de build comme documenté ci-dessous.
</div>

La build Travis CI peut exécuter et construire des images Docker, et peut également pousser des images vers des référentiels Docker ou un autre stockage distant.

Pour utiliser Docker, ajoutez les paramètres suivants à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
services:
    - docker
```

Ensuite, vous pouvez ajouter la commande  ` - docker ` à votre build comme indiqué dans les exemples ci-dessous. 

<div class="notice cadre">
Nous ne prenons pas actuellement en charge l'utilisation de Docker sur macOS. 
</div>

<div class="notice cadre">
Pour plus d'informations sur la façon d'utiliser Docker sur Travis CI Enterprise, consultez <a href="https://docs.travis-ci.com/user/enterprise/build-images/#enabling-docker-builds">l'activation des builds Docker</a>.
</div>

<h2 id="utiliser-docker-depuis-un-referentiel">Utilisez une image Docker à partir d'un référentiel.
<a href="#utiliser-docker-depuis-un-referentiel" class="ancre-titre after"></a></h2>

Cet [exemple de référentiel](https://github.com/travis-ci/docker-sinatra) exécute deux conteneurs Docker construits à partir de la même image : 

* une application Sinatra ; 
* la suite de test d'application Sinatra.

Après avoir précisé dans le `.travis.yml` d'utiliser Docker (avec `services: - docker`) et Ruby (avec `langage: ruby`), l'étape de build `before_install` tire une image docker de [carlad/sinatra ](https://registry.hub.docker.com/u/carlad/sinatra/) puis exécute :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
cd /root/sinatra; bundle exec foreman start;
```

dans un conteneur construit à partir de cette image après avoir cartographiés certains ports et chemins. Lisez le [guide de l'utilisateur Docker](https://docs.docker.com/) si vous avez besoin d'un rafraîchissement sur la façon d'utiliser Docker.

Le `.travis.yml` complet ressemble à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
language: ruby
services:
  - docker
before_install:
- docker pull carlad/sinatra
- docker run -d -p 127.0.0.1:80:4567 carlad/sinatra /bin/sh -c "cd /root/sinatra; bundle exec foreman start;"
- docker ps -a
- docker run carlad/sinatra /bin/sh -c "cd /root/sinatra; bundle exec rake test"
script:
- bundle exec rake test
```

et produit la [sortie de build](https://travis-ci.org/travis-ci/docker-sinatra) suivante :

```console
$ docker ps -a
CONTAINER ID        IMAGE                   COMMAND                CREATED                  STATUS                  PORTS                    NAMES
e376792bce99        carlad/sinatra:latest   "/bin/sh -c 'cd /roo   Less than a second ago   Up Less than a second   127.0.0.1:80->4567/tcp   condescending_galileo
$ docker run carlad/sinatra /bin/sh -c "cd /root/sinatra; bundle exec rake test"
/usr/local/bin/ruby -I"lib:test" -I"/usr/local/lib/ruby/2.2.0" "/usr/local/lib/ruby/2.2.0/rake/rake_test_loader.rb" "test/test_app.rb"
Loaded suite /usr/local/lib/ruby/2.2.0/rake/rake_test_loader
Started
.
Finished in 0.022952763 seconds.
------
1 tests, 1 assertions, 0 failures, 0 errors, 0 pendings, 0 omissions, 0 notifications
100% passed
------
43.57 tests/s, 43.57 assertions/s
```

<h2 id="image-docker-a-partir-du-dockerfile">Construire une image docker à partir d'un dockerfile.
<a href="#image-docker-a-partir-du-dockerfile" class="ancre-titre after"></a></h2>

Au lieu de télécharger l'image Docker à partir de [Carlad/Sinatra](https://registry.hub.docker.com/u/carlad/sinatra/), vous pouvez la construire directement à partir du Dockerfile dans le [référentiel GitHub](https://github.com/travis-ci/docker-sinatra/blob/master/Dockerfile).

Pour construire le dockerfile dans le répertoire actuel et lui donner la même étiquette `carlad/sinatra`, modifiez la ligne `docker pull` en :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
docker build -t carlad/sinatra
```

Le `.travis.yml` complet ressemble à ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
language: ruby
services:
  - docker
before_install:
  - docker build -t carlad/sinatra .
  - docker run -d -p 127.0.0.1:80:4567 carlad/sinatra /bin/sh -c "cd /root/sinatra; bundle exec foreman start;"
  - docker ps -a
  - docker run carlad/sinatra /bin/sh -c "cd /root/sinatra; bundle exec rake test"
script:
  - bundle exec rake test
```

<h2 id="pousser-une-image-docker-vers-un-registre">Poussez une image Docker vers un registre.
<a href="#pousser-une-image-docker-vers-un-registre" class="ancre-titre after"></a></h2>

Pour pousser une image vers un registre Docker, il faut d'abord s'authentifier via `docker login`. L'e-mail, le nom d'utilisateur et le mot de passe utilisés pour la connexion doivent être stockés dans les variables d'environnement des paramètres du référentiel, qui peuvent être configurées via la page web des paramètres du référentiel ou localement via le CLI Travis, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
travis env set DOCKER_USERNAME myusername
travis env set DOCKER_PASSWORD secretsecret
```

Assurez-vous de [crypter les variables d'environnement](./variable-environnement.md
) à l'aide du _gem_ travis
dans votre `.travis.yml` et avant de tenter un `docker push` ou peut-être avant `docker pull` d'une image privée, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
```

<h3 id="registres-bases-sur-les-branches">Les <i>pushes</i> de registre basé sur les branches.
<a href="#registres-bases-sur-les-branches" class="ancre-titre after"></a></h3>

Pour pousser une branche particulière de votre référentiel vers un registre distant, utilisez la section _deploy_ personnalisé de votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
deploy:
    provider: script
    script: bash docker_push
    on:
        branch: master
```

où `docker_push` est un script dans votre référentiel contenan t:

<div class="titre-code">
    <div class="item gauche">&nbsp;docker_push</div><div class="item droit">Bash</div>
</div>
```bash
#!/bin/bash
echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
docker push USER/REPO
```

<h3 id="connexion-au-registre-prive">Connexion au registre privé.
<a href="#connexion-au-registre-prive" class="ancre-titre after"></a></h3>

Lorsque vous poussez un registre privé, assurez-vous de spécifier le nom d'hôte dans la commande `docker login`, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin registry.example.com
```

<h2 id="utilisez-docker-compose">Utilisez Docker Compose.
<a href="#utilisez-docker-compose" class="ancre-titre after"></a></h2>

L'outil [Docker Compose](https://docs.docker.com/compose/) est également [installé dans l'environnement compatible Docker](./fiable.md).

Si nécessaire, vous pouvez facilement remplacer cette version préinstallée de `docker-compose` en ajoutant l'étape suivante `before_install` sur votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
env:
  - DOCKER_COMPOSE_VERSION=v2.17.3
before_install:
  - sudo rm /usr/local/bin/docker-compose
  - curl -L https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-`uname -s`-`uname -m` > docker-compose
  - chmod +x docker-compose
  - sudo mv docker-compose /usr/local/bin
```

<h2 id="installer-version-docker-recente">Installer une version docker plus récente.
<a href="#installer-version-docker-recente" class="ancre-titre after"></a></h2>

Vous pouvez mettre à jour la dernière version et utiliser les nouvelles fonctionnalités de Docker en le mettant à jour manuellement dans l'étape `before_install` de votre fichier `.travis.yml` :

**Mise à jour à partir de download.docker.com**

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
before_install:
  - sudo systemctl stop docker.service && sudo systemctl stop docker.socket
  - curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  - yes | sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  - sudo apt-get update
  - sudo apt-get -y -o Dpkg::Options::="--force-confnew" install docker-ce
```

Alternativement, vous pouvez utiliser `addons` au lieu de `before_install` pour mettre à jour via `apt` également :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yaml
addons:
    apt:
        packages:
          - docker-ce
```

<div class="notice cadre">
Vérifiez quelle version de Docker vous utilisez avec <code>docker --version</code>
</div>

<h2 id="exemples">Exemples.
<a href="#exemples" class="ancre-titre after"></a></h2>

* [heroku/logplex](https://github.com/heroku/logplex/blob/master/.travis.yml) (routeur de logs Heroku) ;
* [kartorza/docker-pg-backup](https://github.com/kartoza/docker-pg-backup/blob/master/.travis.yml)  (un job cron qui sauvegardera les bases de données fonctionnant dans un conteneur PostgreSQL docker).
<hr class="invisible">
