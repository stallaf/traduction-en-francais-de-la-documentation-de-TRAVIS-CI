# Configurer base de données et services

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#demarrer-les-services">Démarrer les services</a></li>
            <li><a href="#mysql">MySQL</a></li>
            <li><a href="#postgresql">PostgreSQL</a></li>
            <li><a href="#mariadb">MariaDB</a></li>
            <li><a href="#sqlite3">SQLite3</a></li>
            <li><a href="#mongodb">MongoDB</a></li>
            <li><a href="#couchdb">CouchDB</a></li>
            <li><a href="#rabbitmq">RabbitMQ</a></li>
            <li><a href="#riak">Riak</a></li>
            <li><a href="#memcached">Memcached</a></li>
            <li><a href="#redis">Redis</a></li>
            <li><a href="#cassandra">Cassandra</a></li>
            <li><a href="#neo4j">Neo4j</a></li>
            <li><a href="#elasticsearch">ElasticSearch</a></li>
            <li><a href="#rethinkdb">RethinkDB</a></li>
            <li><a href="#construction-bdd-multiples">Constructions de bases de données multiples</a></li>
        </ul>
        </ul>
    </div>
</div>
</p>

Ce guide couvre la mise en place des bases de données et d'autres services les plus populaires dans l'environnement Travis CI.

Vous pouvez vérifier la disponibilité des bases de données et des services dans l'environnement de construction que vous utilisez [ici](./environnement-ci.md).

Tous les services utilisent des paramètres par défaut, à l'exception de certains utilisateurs ajoutés et des paramètres de sécurité assouplis.

<h2 id="demarrer-les-services">Démarrer les services.
<a href="#demarrer-les-services" class="ancre-titre after"></a></h2>

Les environnements Travis CI ne démarrent pas les services par défaut afin de laisser plus de RAM disponible pour créer des scripts. Démarrez les services en les ajoutant à la section `services:` de votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services: mongodb
```

<div class="notice cadre">
Si vous installez un service dans la section <code>addons:</code> tel que MariADB, vous n'avez pas besoin de l'ajouter à la section <code>services:</code> également.
</div>

Pour commencer plusieurs services :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
    - riak
    - rabbitmq
    - memcached
```

<div class="notice cadre">
Si vous téléchargez et installez un service manuellement, vous devez également le démarrer dans une étape <code>before_install</code>. La clé de services ne fonctionne que pour les services que nous fournissons.
</div>

<h2 id="mysql">MySQL.
<a href="#mysql" class="ancre-titre after"></a></h2>

Démarrez MySQL dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
    - mysql
```

MySQL se connecte à `127.0.0.1` et à un socket défini dans `~travis/.my.cnf` et nécessite une authentification. Vous pouvez vous connecter en utilisant le nom d'utilisateur `travis` ou `root` et un mot de passe vide.

<div class="notice cadre">
Notez que l'utilisateur <code>travis</code> n'a pas les privilèges accrus de l'utilisateur <code>root</code>. 
</div>

<p>
<table class="tableau2">
    <thead>
        <tr><th>&nbsp;<th>Ubuntu Precise</th><th>Ubuntu Trusty</th><th>Ubuntu Xenial</th><th>Ubuntu Bionic</th><th>Ubuntu Focal</td><td>Ubuntu Jammy</th></tr>
    </thead>
    <tbody>
        <tr><td>Mysql</td><td>5.5.x</td><td>5.6.x</td><td>5.7.x</td><td>5.7.x</td><td>8.0.x</td><td>8.0.x</td></tr>
    </tbody>
</table>
</p>

Vous pouvez également installer [MySQL 5.7](#mysql5-7) sur Ubuntu Trusty.

<h3 id="mysql-avec-activerecord">Utilisez MySQL avec ActiveRecord.
<a href="#mysql-avec-activerecord" class="ancre-titre after"></a></h3> 

Exemple de fichier `config/database.yml` pour les projets Ruby utilisant ActiveRecord :

<div class="titre-code">
    <div class="item gauche">&nbsp;config/database.yml</div><div class="item droit">YAML</div>
</div>
```yml
test:
    adapter: mysql2
    database: myapp_test
    username: travis
    encoding: utf8
```

Vous devrez peut-être d'abord créer la base de données `myapp_test`, par exemple, dans l'étape `before_install` du `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
    - mysql -e 'CREATE DATABASE myapp_test;'
```

<h3 id="remarque-sur-la-bdd-de-test">Remarque sur la base de données de test.
<a href="#remarque-sur-la-bdd-de-test" class="ancre-titre after"></a></h3> 

Dans les anciennes versions de MySQL, le package Ubuntu a fourni la base de données `test` par défaut. Ce n'est plus le cas à partir de la version 5.5.37 en raison de problèmes de sécurité ([Voir le journal des modifications](http://changelogs.ubuntu.com/changelogs/pool/main/m/mysql-5.5/mysql-5.5_5.5.47-0ubuntu0.12.04.1/changelog)).

La base de données `test` peut être créée si nécessaire, par exemple, dans l'étape `before_install` dans `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
    - mysql -e 'CREATE DATABASE IF NOT EXISTS test;'
```

<h3 id="mysql5-7">Mysql 5.7.
<a href="#mysql5-7" class="ancre-titre after"></a></h3> 

MySQL 5.7 est la valeur par défaut des images Xenial (`dist: xenial`) et Bionic (`dist: bionic`). 

<div class="notice cadre">
Depuis le 21 juillet 2019, MySQL 5.7 n'est plus pris en charge sur Ubuntu Trusty (14.04). <a href="https://www.mysql.com/support/eol-notice.html">Consultez les annonces EOL de support de produit MySQL</a> et <a href="https://forums.mysql.com/read.php?11,677237,677268#msg-677268">ce post</a> dans les forums MySQL.
</div>

<h2 id="postgresql">PostgreSQL.
<a href="#postgresql" class="ancre-titre after"></a></h2> 

Démarrez PostgreSQL dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
    - postgresql
```

<h3 id="utiliser-postgresql">Utilisez PostgreSQL.
<a href="#utiliser-postgresql" class="ancre-titre after"></a></h3> 

L'utilisateur par défaut pour accéder au serveur PostgreSQL local est Postgres avec un mot de passe vide.

Créez une base de données pour votre application en ajoutant une ligne à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
    - psql -c 'create database travis_ci_test;' -U postgres
```

Pour une application Rails, vous pouvez désormais utiliser la configuration `database.yml` suivante pour accéder localement à la base de données :

<div class="titre-code">
    <div class="item gauche">&nbsp;database.yml</div><div class="item droit">YAML</div>
</div>
```yml
test:
    adapter: postgresql
    database: travis_ci_test
```

Si votre configuration locale de test utilise des informations d'identification ou des paramètres différents pour accéder à la base de données de test locale, nous vous recommandons d'enregistrer ces paramètres dans un fichier `database.yml.travis` dans votre référentiel et de le copier dans le cadre de votre build :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
    - cp config/database.yml.travis config/database.yml
```

<h3 id="utiliser-autre-version-postgresql">Utilisez une autre version PostgreSQL.
<a href="#utiliser-autre-version-postgresql" class="ancre-titre after"></a></h3> 

Les environnements de construction Travis CI utilisent la version 9.2 par défaut des images Trusty, mais d'autres versions du [référentiel officiel de PostgreSQL APT](http://apt.postgresql.org/) sont également disponibles. Pour utiliser une version autre que la valeur par défaut, spécifiez uniquement la version `major.minor` dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    postgresql: "9.4"
```

De nombreuses versions PostgreSQL ont été préinstallées dans votre environnement de build, et d'autres peuvent être ajoutées et activées au moment de la construction en utilisant une combinaison de modules complémentaires  `postgresql` et `apt` ainsi qu'une variable d'environnement globale pour `PGPORT` et pour `PGUSER` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    postgresql: "11"
    apt:
    packages:
    - postgresql-11
    - postgresql-client-11
env:
    global:
    - PGPORT=5433
    - PGUSER=travis
```

Dans les images Xenial, Postgres 9.4 à 9.6 nécessitent uniquement la version spécifiée et utilisent par défaut l'utilisateur `postgres` et le port 5432.

Pour PostgreSQL 10, vous devez spécifier les paquets à installer, l'utilisateur est `postgres` et le port est 5432. Pour PostgreSQL 11 et 12, vous devez spécifier les paquets, mais l'utilisateur est `travis` et le port est 5433. Vous devez donc spécifier le PGPORT.

<h3 id="utiliser-postgis">Utiliser PostGIS.
<a href="#utiliser-postgis" class="ancre-titre after"></a></h3> 

Installez la version de PostGIS qui correspond à votre version PostgreSQL et activez l'extension PostGIS à l'aide de :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    postgresql: 9.6
    apt:
        packages:
        - postgresql-9.6-postgis-2.3
before_script:
    - psql -U postgres -c "create extension postgis"
```

<h3 id="postgres-et-les-locales">PostgreSQL et les paramètres régionaux.
<a href="#postgres-et-les-locales" class="ancre-titre after"></a></h3>

L'environnement de build Travis CI est livré avec un certain nombre de paramètres régionaux préinstallés, mais vous pouvez également en installer d'autres si vous en avez besoin.

**Installer les paramètres régionaux**

L'exemple suivant montre les lignes que vous devez ajouter à votre `.travis.yml` pour installer le pack de langue espagnole. 

<div class="notice cadre">
Notez que vous devez supprimer la version postgresql de la section <code>addons</code> de votre .travis.yml.
</div>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
    - sudo apt-get update
    - sudo apt-get install language-pack-es
    - sudo /etc/init.d/postgresql stop
    - sudo /etc/init.d/postgresql start 9.3
```

**Utiliser pg_config**

Si vos versions reposent sur la commande `pg_config`, vous devez installer un package apt supplémentaire `postgresql-server-dev-X.Y`, où `X.Y` correspond à la version de PostgreSQL que vous utilisez.

Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    postgresql: '9.4'
    apt:
        packages:
          - postgresql-server-dev-9.4
```

Voir [ce problème GitHub](https://github.com/travis-ci/travis-ci/issues/9011) pour plus de détails.

<h2 id="mariadb">MariaDB.
<a href="#mariadb" class="ancre-titre after"></a></h2>

MariaDB est un _fork_ développé par la communauté de MySQL. Il est disponible en tant que service complémentaire sur Travis CI.

Pour utiliser MariaDB, spécifiez la version «major.minor» que vous souhaitez utiliser dans votre `.travis.yml`. Les versions sont répertoriées sur la [page web MariaDB](https://downloads.mariadb.org/).

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    mariadb: '10.0'
```

Le numéro de version est exporté sous forme de variable d'environment `TRAVIS_MARIADB_VERSION`.

<h2 id="sqlite3">Sqlite3.
<a href="#sqlite3" class="ancre-titre after"></a></h2>

La base de données relationnelle la plus simple et la plus facile à utiliser.

<h3 id="sqlite3-dans-ruby">Sqlite3 dans les projects Ruby.
<a href="#sqlite3-dans-ruby" class="ancre-titre after"></a></h3>

Ajoutez les liaisons Ruby SQLite3 à votre paquet :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Ruby</div>
</div>
```ruby
# Gemfile
# for CRuby, Rubinius, including Windows and RubyInstaller
gem "sqlite3", :platform => [:ruby, :mswin, :mingw]
# for JRuby
gem "jdbc-sqlite3", :platform => :jruby
```

Si vous utilisez ActiveRecord, ajoutez ce qui suit à votre `config/database.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;config/database.yml</div><div class="item droit">YAML</div>
</div>
```yml
test:
    adapter: sqlite3
    database: ":memory:"
    timeout: 500
```

ou si vous n'utilisez pas de `config/database.yml`, connectez manuellement à la base de données:

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Ruby</div>
</div>
```ruby
ActiveRecord::Base.establish_connection :adapter => 'sqlite3',
                                        :database => ':memory:'
```

<h2 id="mongodb">MongoDB.
<a href="#mongodb" class="ancre-titre after"></a></h2>

Démarrez MongoDB dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
    - mongodb
```

MongoDB se connecte au port `127.0.0.1` et ne nécessite aucune authentification ou création de base de données à l'avance.  Si vous ajoutez un utilisateur `admin`, l'authentification est activée, car `mongod` est lancé avec l'argument `--auth`.

<div class="notice cadre">
Remarque : les utilisateurs administrateur sont des utilisateurs créés dans la base de données admin.
</div>

Pour créer des utilisateurs pour votre base de données, ajoutez une section `before_script` à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
    - mongo mydb_test --eval 'db.createUser({user:"travis",pwd:"test",roles:["readWrite"]});'
```

<h3 id="depannage-de-mongodb">Dépannage de MongoDB acceptant les connexions.
<a href="#depannage-de-mongodb" class="ancre-titre after"></a></h3>

Quelques utilisateurs ont signalé que MongoDB n'acceptait pas les connexions à partir du script de construction.

Le problème est intermittent, et le seul moyen fiable de l'éviter est d'injecter une attente artificielle avant de faire la première connexion :

Ajoutez ce qui suit `before_script` à votre `.travis.yml` pour attendre avant de vous connecter à MongoDB :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
    - sleep 15
    - mongo mydb_test --eval 'db.createUser({user:"travis",pwd:"test",roles:["readWrite"]});'
```

<h2 id="couchdb">CouchDB.
<a href="#couchdb" class="ancre-titre after"></a></h2>

Démarrez CouchDB dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
    - couchdb
```

CouchDB se connecte au port 127.0.0.1, utilise la configuration par défaut sur `dist:xenial` et les distributions Linux antérieures et ne nécessite pas d'authentification (en termes CouchDB, il s'exécute dans _admin party_).

Cependant, pour `bionic`, l'authentification est requise avec le nom d'utilisateur `admin` et le mot de passe `travis`, par exemple `curl -X PUT http://admin:travis@localhost:5984/<db_name>`.

Avant d'utiliser CouchDB, vous devez créer la base de données dans le cadre de votre processus de construction :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
    - curl -X PUT localhost:5984/myapp_test
```

<h2 id="rabbitmq">RabbitMQ.
<a href="#rabbitmq" class="ancre-titre after"></a></h2>

RabbitMQ nécessite des drapeaux `setuid`, vous ne pouvez donc exécuter RabbitMQ que comme service sur l'infrastructure Ubuntu Trusty.

Commencez RabbitMQ dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
    - rabbitmq
```

RabbitMQ utilise la configuration par défaut : 

* vhost : `/` ; 
* Nom d'utilisateur : `guest` ; 
* Mot de passe : `guest`.

Vous pouvez configurer plus de _vhosts_ et de rôles dans la section `before_script` de votre `.travis.yml`.

RabbitMQ peut être lancé sur [Ubuntu Xenial](./environnement-xenial.md) en utilisant l'addon APT dans `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    apt:
        packages:
          - rabbitmq-server 
```

<h2 id="riak">Riak.
<a href="#riak" class="ancre-titre after"></a></h2>

<div class="notice cadre">
Riak n'est disponible que dans l'environnement <a href="./environnement-trusty.md">Ubuntu Trusty</a>.
</div>

Commencez Riak dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
  - riak
```

Riak utilise la configuration par défaut avec Bitcask comme _backend_ de stockage.

La recherche Riak est désactivée par défaut.

<h2 id="memcached">Memcached.
<a href="#memcached" class="ancre-titre after"></a></h2>

Démarrez le service Memcached dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
  - memcached
```

Memcached utilise la configuration par défaut et se connecte à _localhost_.

<h2 id="redis">Redis.
<a href="#redis" class="ancre-titre after"></a></h2>

Commencez Redis dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
  - redis-server
```

Redis utilise la configuration par défaut et est disponible sur localhost.

<h2 id="cassandra">Cassandra.
<a href="#cassandra" class="ancre-titre after"></a></h2>

Commencez Cassandra dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
  - cassandra
```

Cassandra est téléchargé à partir du [référentiel Apache Apt](http://www.apache.org/dist/cassandra/debian) et utilise la configuration par défaut. Il est disponible sur 127.0.0.1.

<h3 id="cassandra-anciennes-versions">Installez les anciennes versions de Cassandra.
<a href="#cassandra-anciennes-versions" class="ancre-titre after"></a></h3>

Utilisez l'exemple suivant pour installer une ancienne version spécifique de Cassandra dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - sudo rm -rf /var/lib/cassandra/*
  - wget http://www.us.apache.org/dist/cassandra/1.2.18/apache-cassandra-1.2.18-bin.tar.gz && tar -xvzf apache-cassandra-1.2.18-bin.tar.gz && sudo sh apache-cassandra-1.2.18/bin/cassandra
```

<h2 id="neo4j">Neo4j.
<a href="#neo4j" class="ancre-titre after"></a></h2>

Démarrez Neo4j dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
  - neo4j
```

Le serveur Neo4j utilise la configuration par défaut et se connecte à localhost sur le port 7474.

<h2 id="elasticsearch">ElasticSearch .
<a href="#elasticsearch" class="ancre-titre after"></a></h2>

Démarrez ElasticSearch dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
services:
  - elasticsearch
```

ElasticSearch prend quelques secondes pour démarrer. Pour vous assurer qu'il est disponible lorsque le script de build s'exécute, ajoutez-y un petit délai :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
  - sleep 10
```

ElasticSearch utilise la configuration par défaut et est disponible sur 127.0.0.1.

<h3 id="elasticsearch-version-specifique">Installer une version spécifique d'ElasticSearch.
<a href="#elasticsearch-version-specifique" class="ancre-titre after"></a></h3>

Vous pouvez écraser l'ElastiSsearch installé avec la version dont vous avez besoin (par exemple, 7.6.2) avec ce qui suit :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - curl https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-amd64.deb -o elasticsearch.deb
  - sudo dpkg -i --force-confnew elasticsearch.deb
  - sudo chown -R elasticsearch:elasticsearch /etc/default/elasticsearch
  - sudo service elasticsearch restart
```

Nous vous conseillons de vérifier la validité de l'URL de téléchargement sur le site Web d'ElastiSsearch.

<h3 id="sortie-tronquee-dans-le-journal">Sortie tronquée dans le journal de construction.
<a href="#sortie-tronquee-dans-le-journal" class="ancre-titre after"></a></h3>

Lorsque ElasticSearch commence, vous pouvez voir un message d'erreur tronqué tel que :

```console
$ sudo service elasticsearch start
    * Starting ElasticSearch Server       ission denied on key 'vm.max_map_count'
```

Cela est dû à un [changement récent dans ElasticSearch](https://github.com/elasticsearch/elasticsearch/issues/4397), comme indiqué [ici](https://github.com/elasticsearch/elasticsearch/issues/4978). Le message est inoffensif et le service est fonctionnel.

<h2 id="rethinkdb">RethinkDB.
<a href="#rethinkdb" class="ancre-titre after"></a></h2>

Pour utiliser RethinkDB avec Travis CI, ajoutez-le en tant qu'addon dans le fichier de configuration `.travis.yml`, en spécifiant le numéro de version sous forme de chaîne de caractères.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    rethinkdb: '2.3.4'
```

Si vous spécifiez un numéro de version partiel, l'addon installera et exécutera la dernière version qui correspond. Par exemple, `'2.3'` correspondra à la dernière version RethinkDB dans la ligne `2.3.x`.

Deux variables d'environnement sont exportées : 

* `TRAVIS_RETHINKDB_VERSION` est la version spécifiée dans la configuration (par exemple, «2.3.4» ou «2.3») ;
* `TRAVIS_RETHINKDB_PACKAGE_VERSION` est la version complète du package qui a été installé (par exemple,` '2.3.4 + 1 ~ 0précise'`).

Lorsqu'il est activé, RethinkDB commencera sur localhost au port par défaut (28015).

<h2 id="construction-bdd-multiples">Construction de base de données multiples.
<a href="#construction-bdd-multiples" class="ancre-titre after"></a></h2>

Si vous devez exécuter plusieurs builds à l'aide de différentes bases de données, vous pouvez configurer des variables d'environnement et une ligne `before_script` ou `before_install` pour créer une matrice de construction.

<h3 id="etape-before-script">Utilisez des variables d'environnement et une étape before_script.
<a href="#etape-before-script" class="ancre-titre after"></a></h3>

Utilisez la variable d'environnement `DB` pour spécifier le nom de la configuration de la base de données. Localement, vous exécuterez :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
DB=postgres [commands to run your tests]
```

Sur Travis CI, vous souhaitez créer une [matrice de construction](./personnalisation-de-la-construction.md#matrice-de-construction) de trois versions chacune ayant la variable `DB` exportée avec une valeur différente, et pour cela, vous pouvez utiliser l'option `env` dans `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
env:
  - DB=sqlite
  - DB=mysql
  - DB=postgres
```

Ensuite, vous pouvez utiliser ces valeurs dans une étape `before_install` (ou `before_script`) pour configurer chaque base de données. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
  - sh -c "if [ '$DB' = 'postgres' ]; then psql -c 'DROP DATABASE IF EXISTS tests;' -U postgres; fi"
  - sh -c "if [ '$DB' = 'postgres' ]; then psql -c 'DROP DATABASE IF EXISTS tests_tmp;' -U postgres; fi"
  - sh -c "if [ '$DB' = 'postgres' ]; then psql -c 'CREATE DATABASE tests;' -U postgres; fi"
  - sh -c "if [ '$DB' = 'postgres' ]; then psql -c 'CREATE DATABASE tests_tmp;' -U postgres; fi"
  - sh -c "if [ '$DB' = 'mysql' ]; then mysql -e 'CREATE DATABASE IF NOT EXISTS tests_tmp; CREATE DATABASE IF NOT EXISTS tests;'; fi"
```

<div class="notice cadre">
Travis CI n'a pas de support spécial pour ces variables, il crée simplement trois versions avec différentes valeurs exportées. Il appartient à votre script de construction et à des étapes <code>before_install</code> ou <code>before_script</code> pour les utiliser.
</div>

Pour un vrai exemple, voir [doctrine/doctrine2 .travis.yml](https://github.com/doctrine/doctrine2/blob/master/.travis.yml).

<h3 id="utiliser-ruby">Utilisez Ruby.
<a href="#utiliser-ruby" class="ancre-titre after"></a></h3>

Une autre approche est de mettre toute la configuration de la base de données dans un fichier YAML (`test/database.yml` par exemple), comme ActiveRecord :

<div class="titre-code">
    <div class="item gauche">&nbsp;test/database.yml</div><div class="item droit">YAML</div>
</div>
```yml
sqlite:
    adapter: sqlite3
    database: ":memory:"
    timeout: 500
mysql:
    adapter: mysql2
    database: myapp_test
    username:
    encoding: utf8
postgres:
    adapter: postgresql
    database: myapp_test
    username: postgres
```

Ensuite, dans votre suite de tests, lisez ces données dans un hachage de configurations :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Ruby</div>
</div>
```ruby
configs = YAML.load_file('test/database.yml')
ActiveRecord::Base.configurations = configs
db_name = ENV['DB'] || 'sqlite'
ActiveRecord::Base.establish_connection(db_name)
ActiveRecord::Base.default_timezone = :utc
```
<hr class="invisible">
