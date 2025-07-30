# Installation des dépendances

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#installer-des-paquetages-sur-une-infrastructure-standard">Installer des paquetages sur une infrastructure standard</a></li>
            <li><a href="#installer-des-paquetages-sur-freebsd">Installer des paquetages sur FreeBSD</a></li>
            <li><a href="#installer-des-dependances-sur-plusieurs-systemes">Installer des dépendances sur plusieurs systèmes d'exploitation</a></li>
            <li><a href="#installer-des-projets-a-partir-du-code-source">Installer des projets à partir du code source</a></li>
        </ul>
        </ul>
    </div>
</div>
</p>

<h2 id="installer-des-paquetages-sur-une-infrastructure-standard">Installer des paquetages sur une infrastructure standard.
<a href="#installer-des-paquetages-sur-une-infrastructure-standard" class="ancre-titre after"></a></h2>

Pour installer des paquets Ubuntu qui ne sont pas inclus dans les distributions standard [precise](./environnement-de-construction-precise.md), [trusty](./environnement-de-construction-trusty.md), [xenial](./environnement-de-construction-xenial.md) ou [bionic](./environnement-de-construction-bionic.md), utilisez apt-get dans l'étape `before_install` de votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
    - sudo apt-get -y install libxml2-dev
```

Par défaut,  `apt-get update` n'est pas exécutée automatiquement. Si vous souhaitez mettre à jour automatiquement`apt-get update` sur chaque version, il existe deux façons de le faire. Le premier consiste à exécuter explicitement `apt-get update`dans l'étape `befor-install` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
    - sudo apt-get update
    - sudo apt-get -y install libxml2-dev
```

La deuxième façon est d'utiliser [l'addon APT](#installer-des-paquetages-avec-addon-apt) :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
    - sudo apt-get -y install libxml2-dev
addons:
    apt:
        update: true
```

<div class="notice cadre">
N'exécutez pas <code>apt-get upgrade</code> dans votre build, car cela télécharge jusqu'à 500 Mo de paquets et prolonge considérablement votre temps de construction. De plus, certains paquets peuvent ne pas être mis à jour, ce qui entraînera un échec de la construction.<br>
Utilisez le paramètre <code>-y</code> avec apt-get pour répondre oui à toutes les questions posées par les outils apt.
</div>

<h3 id="installer-depuis-un-referentiel-apt">Installer des paquetages à partir d'un référentiel APT personnalisé.
<a href="#installer-depuis-un-referentiel-apt" class="ancre-titre after"></a></h3>

Pour certains packages, vous pouvez trouver un référentiel existant, qui n'est pas encore configuré sur notre environnement de construction par défaut. Vous pouvez facilement ajouter des référentiels personnalisés et des PPAs de LaunchPad dans le cadre de votre version.

Par exemple, pour installer gcc à partir de ubuntu-toolchain ppa :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
    - sudo add-apt-repository -y ppa:ubuntu-toolchain-r/test
    - sudo apt-get -q update
    - sudo apt-get -y install gcc-4.8
```

Pour les référentiels non hébergés sur LaunchPad, vous devez également ajouter une clé GnuPG.

Si vous installez des paquetages de cette façon, assurez-vous de télécharger la version correcte pour votre environnement.

Cet exemple ajoute le référentiel APT pour Varnish 3.0 pour Ubuntu 12.04 à la liste localement disponible des sources APT, puis installe le paquetage `varnish`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
    - curl http://repo.varnish-cache.org/debian/GPG-key.txt | sudo apt-key add -
    - echo "deb http://repo.varnish-cache.org/ubuntu/ precise varnish-3.0" | sudo tee -a /etc/apt/sources.list
    - sudo apt-get -qq update
    - sudo apt-get -y install varnish
```

<h3 id="installer-des-paquetages-sans-referentiel-apt">Installer des paquetages sans référentiel APT.
<a href="#installer-des-paquetages-sans-referentiel-apt" class="ancre-titre after"></a></h3>

Pour certains projets, il peut y avoir un paquetage Debian/Ubuntu disponible, mais pas de référentiel APT correspondant. Ceux-ci sont toujours faciles à installer, mais nécessitent l'étape supplémentaire de téléchargement.

Si vous installez des paquetages de cette façon, assurez-vous de télécharger la version correcte pour votre environnement.

Disons que votre projet nécessite l'outil pngquant pour compresser les fichiers PNG, voici comment télécharger et installer le fichier .deb :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
    - wget http://pngquant.org/pngquant_1.7.1-1_i386.deb
    - sudo dpkg -i pngquant_1.7.1-1_i386.deb
```

<h3 id="installer-les-paquetages-avec-addon-apt">Installez les paquetages avec l'addon APT.
<a href="#installer-les-paquetages-avec-addon-apt" class="ancre-titre after"></a></h3>

Vous pouvez également installer des paquetages et des sources à l'aide de l'addon APT, sans exécuter des commandes `apt-get` dans votre script `before_install`.

Si vos exigences vont au-delà de l'installation normale, veuillez utiliser une autre méthode décrite ci-dessus.

**Ajouter des sources apt :**

Pour ajouter des sources APT, vous pouvez utiliser l'un des trois types d'entrées suivants : 

1. alias définis dans [source safelist](https://github.com/travis-ci/apt-source-safelist) ; 
2. paires clés/valeurs `sourceline` qui seront ajoutées à `/etc/apt/sources.list` ; 
3. lorsque les sources APT nécessitent des clés GPG, vous pouvez le spécifier avec des paires `key_url` en plus de `sourceline`.

L'extrait suivant montre ces trois types de sources APT.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    apt:
        sources:
         - deadsnakes
         - sourceline: 'ppa:ubuntu-toolchain-r/test'
         - sourceline: 'deb https://packagecloud.io/chef/stable/ubuntu/precise main'
            key_url: 'https://packagecloud.io/gpg.key'
```

**Ajouter des paquetages APT**

Liste des paquets APT sous la clé `addons.apt.packages` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    apt:
        packages:
         - cmake
         - time
```

<div class="notice cadre">
Remarque: Lorsque vous utilisez des sources et des paquetages APT, vous devez vous assurer qu'ils sont sous le même espace clé dans le fichier YAML, par exemple :
</div>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    apt:
        sources:
       - ubuntu-toolchain-r-test
        packages:
       - gcc-4.8
       - g++-4.8
```

<div class="notice cadre">
Remarque : si `apt-get install` échoue, la construction est marquée comme une erreur. 
</div>

<div class="notice cadre">
Vous pouvez également jeter un œil à la section <a href="https://config.travis-ci.com/ref/job/addons/apt">Apt</a> dans notre <a href="https://config.travis-ci.com/">référence de configuration Travis CI Build</a>.
</div>

<h3 id="installer-les-paquetages-snap">Installez les paquetages Snap avec l'Addon Snaps.
<a href="#installer-les-paquetages-snap" class="ancre-titre after"></a></h3>

Vous pouvez installer des paquetages [snap](http://snapcraft.io/) en utilisant nos images Xenial ou Bionic :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
dist: xenial
```

ou

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
dist: bionic
```

Le magasin Snap d'Ubuntu propose de nombreux paquetages directement entretenus par les développeurs en amont, souvent avec des versions plus récentes que celles disponibles dans les archives Apt.

Vous pouvez spécifier les snaps sous forme de tableau de spécifications de snap, chacune pouvant prendre l'une des deux formes suivantes :

1 . Le nom du snap, qui sera transmis à `snap install` sans indicateurs supplémentaires. Par exemple : 

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
dist: xenial
addons:
    snaps:
      - hugo
```

Il en résulte : 

```console
 $ sudo snap install hugo
```

La carte spécifie comment le snap doit être installé. Les clés possibles sont : `name`, `confinement` et `channel`. La clé `confinement` sera utilisée pour ajouter le drapeau `--classic` ou `--devmode`, et `channel` sera transmis au drapeau `--channel`. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
dist: xenial
addons:
    snaps:
      - name: aws-cli
        confinement: classic # or devmode
         channel: latest/edge # will be passed to --channel flag
```

Il en résulte : 

```console
$ sudo snap install aws-cli --classic --channel=latest/edge
```

`confinement` et `channel` sont facultatifs.

<h2 id="installer-des-paquetages-sur-freebsd">Installer des paquetages sur FreeBSD.
<a href="#installer-des-paquetages-sur-freebsd" class="ancre-titre after"></a></h2>

Pour installer des paquetages qui ne sont pas inclus dans l'environnement FreeBSD par défaut, utilisez `pkg`` dans l'étape `before_install` de votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
   - su -m root -c 'pkg install -y curl'
```

Pour plus de commodité, vous pouvez utiliser l'addon `pkg`` dans votre `.travis.yml`. Par exemple, pour installer go et curl :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons: 
 pkg: 
   - go 
   - curl
```

<h2 id="installer-des-dependances-sur-plusieurs-systemes">Installez des dépendances sur plusieurs systèmes d'exploitation.
<a href="#installer-des-dependances-sur-plusieurs-systemes" class="ancre-titre after"></a></h2>

Si vous testez à la fois Linux et macOS, vous pouvez utiliser à la fois l'addon APT et l'addon Homebrew. Chaque addon ne fonctionnera que sur la plate-forme appropriée :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    apt:
        packages: foo
    homebrew:
        packages: bar
```

Si vous installez des paquetages manuellement, utilisez la variable `$TRAVIS_OS_NAME` pour installer les dépendances séparément pour chaque système d'exploitation :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
   - if [ $TRAVIS_OS_NAME = linux ]; then sudo apt-get install foo; else brew install bar; fi
```

<h2 id="installer-des-projets-a-partir-du-code-source">Installer des projets à partir du code source.
<a href="#installer-des-projets-a-partir-du-code-source" class="ancre-titre after"></a></h2>

Certaines dépendances ne peuvent être installées qu'à partir d'un paquet source. La construction peut nécessiter une version plus récente ou un outil ou une bibliothèque qui n'est pas disponible sous forme de paquet Ubuntu.

Vous pouvez facilement inclure les étapes de build  dans votre fichier `.travis.yml` ou, ce qui est la méthode recommandée, en exécutant un script pour gérer le processus d'installation.
Voici un exemple simple qui installe CasperJS à partir d'un paquetage binaire :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_script:
   - wget https://github.com/n1k0/casperjs/archive/1.0.2.tar.gz -O /tmp/casper.tar.gz
   - tar -xvf /tmp/casper.tar.gz
   - export PATH=$PATH:$PWD/casperjs-1.0.2/bin/
```

<div class="notice cadre">
Notez que lorsque vous mettez à jour la variable d'environnement <code>$PATH</code>, cette partie ne peut pas être déplacée dans un script shell, car cela ne mettrait à jour la variable que pour le sous-processus qui exécute le script.
</div>

Pour installer quelque chose depuis les sources, vous pouvez suivre des étapes similaires. Voici un exemple pour télécharger, compiler et installer la bibliothèque protobufs.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
   - wget https://protobuf.googlecode.com/files/protobuf-2.4.1.tar.gz
   - tar -xzvf protobuf-2.4.1.tar.gz
   - pushd protobuf-2.4.1 && ./configure --prefix=/usr && make && sudo make install && popd
```

Ces trois commandes peuvent être extraites dans un script shell, nommez-le `install-protobuf.sh` :

<div class="titre-code">
    <div class="item gauche">&nbsp;install-protobuf.sh</div><div class="item droit">Bash</div>
</div>
```bash
#!/bin/sh
set -ex
wget https://protobuf.googlecode.com/files/protobuf-2.4.1.tar.gz
tar -xzvf protobuf-2.4.1.tar.gz
cd protobuf-2.4.1 && ./configure --prefix=/usr && make && sudo make install
```

Une fois qu'il est ajouté au référentiel, vous pouvez l'exécuter à partir de votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
   - ./install-protobuf.sh
```

Notez que la première version utilise `pushd` et `popd` pour s'assurer qu'à la fin de la section `install` le répertoire de travail est renvoyé à sa valeur d'origine. Ce n'est pas nécessaire dans le script shell, car il s'exécute dans un sous-shell et ne modifie donc pas le répertoire de travail original.
<hr class="invisible">