# Construire un projet Python

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#specifier-versions-python">Spécifier les versions Python</a></li>
            <li><a href="#script-par-defaut-python">Script de construction par défaut</a></li>
            <li><a href="#tox">Utiliser Tox comme script de compilation</a></li>
            <li><a href="#tests-python">Exécuter des tests Python sur plusieurs systèmes d'exploitation</a></li>
            <li><a href="#gestion-des-dependances-python">Gestion des dépendances</a></li>
            <li><a href="#reference-de-configuration-de-construction-python">Référence de configuration de construction</a></li>
            <li><a href="#exemples-python">Exemples</a></li>
            <li><a href="#tableau-versions-python">Versions Python</a></li>
        </ul>
    </div>
</div>
</p>

<div class="deux-colonnes">
  <div class="notice cadre">
  Pour les versions de langage et d'autres informations spécifiques à l'environnement de compilation, consultez nos pages de référence :
  <p>
  <ul>
    <li><a href="environnement-precise.md">Precise</a> ;</li>
    <li><a href="environnement-trusty.md">Trusty</a> ;</li>
    <li><a href="environnement-xenial.md">Xenial</a> ;</li>
    <li><a href="environnement-bionic.md">Bionic</a> ;</li>
    <li><a href="environnement-focal.md">Focal</a> ;</li>
    <li><a href="environnement-jammy.md">Jammy</a> ;</li>
    <li><a href="environnement-noble.md">Noble</a>;</li>
    <li><a href="environnement-macos.md">macOs</a> ;</li>
    <li><a href="environnement-freebsd.md">FreeBSD</a>.</li>
  </ul>
  </p>
  </div>

  <div class="notice cadre">
  Les versions Python ne sont pas disponibles sur les environnements Windows.
  </div>

<p>
Le reste de ce guide traite de la configuration des projets Python sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.
</p>

<p>
<table class="tableau">
    <thead>
        <tr><th>PHP</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="#gestion-des-dependances-python">Défaut<code>install</code></td>
            <td><code>pip install -r requirements.txt</code></td>
        </tr>
        <tr>
            <td><a href="#script-de-build-par-defaut-python">Défaut<code>script</code></td>
            <td>N/A</td>
        </tr>
        <tr>
            <td>Cle de matrice</a></td>
            <td><code>python</code>,<code>env</code><td>
        </tr>
        <tr>
            <td>Support</td>
            <td><a href="mailtosupport@travis-ci.com">Travis CI</a></td>
        </tr>
    </tbody>
    <tfoot>
        <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
        <tr><th colspan="2" text-align="left">Exemple minimal</th></tr>
        <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
        <tr><th colspan="2">
            <div class="titre-code">
                <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
            </div>
```yml
  language: python
  script:
    - pytest
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>

<h2 id="specifier-versions-python">Spécifiez les versions Python
<a href="#specifier-versions-python" class="ancre-titre after"></a></h2>

Spécifiez les versions Python à l'aide de la clé `python`. Au fur et à mesure que nous mettons à jour les images de construction Python, des alias comme `3.10` pointeront vers différentes versions exactes ou niveaux de correctifs.

Un exemple de fichier `.travis.yml` qui teste diverses versions PHP :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: python
python:
  - "2.7"
  - "3.4"
  - "3.5"
  - "3.6"
  - "3.7"
  - "3.8"
  - "3.9"
  - "3.9-dev"  # 3.9 development branch. similarly 3.10-dev, 3.11-dev, etc.
  - "3.10" # current default Python in Travis CI, if `language: python` is specified
  - "3.11"
  - "3.12"
  - "3.13"
  - "nightly"  # nightly build
# command to install dependencies
install:
  - pip install -r requirements.txt
# command to run tests
script:
  - pytest
```

Si la version spécifiée de Python n'est pas disponible sur l'image de construction actuelle, la tâche tentera de télécharger les archives distantes appropriées et de la rendre disponible. Vous pouvez trouver la liste de ces versions dans le [tableau ci-dessous](#tableau-versions-python).

<h3 id="virtualenvs">Utilisez un virtualenvs isolé.
<a href="#virtualenvs" class="ancre-titre after"></a></h3>

L'environnement CI utilise des instances virtualenv distinctes pour chaque version Python. Cela signifie que dès que vous spécifiez `language: python` dans `.travis.yml`, vos tests s'exécuteront dans un virtualenv (sans que vous ayez à le créer explicitement). Le Système Python n'est pas utilisé et ne doit pas être invoqué. Si vous devez installer des paquetages Python, faites-le via pip et non apt.

Si vous décidez tout de même d'utiliser apt, notez que pour des raisons de compatibilité, vous ne pourrez utiliser que les versions Python par défaut disponibles dans Ubuntu (par exemple, pour Xenial, cela signifie 2.7.12 et 3.5.2). Pour accéder aux paquets dans virtualenv, vous devrez spécifier qu'il doit être créé avec l'option `--system-site-packages`. Pour ce faire, ajoutez ce qui suit dans votre fichier `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: python
python:
  - "2.7"
  - "3.5"
virtualenv:
  system_site_packages: true
```

<h3 id="pypy">Support PyPy.
<a href="#pypy" class="ancre-titre after"></a></h3>

Travis CI prend en charge PyPy et Pypy3.

Pour tester votre projet contre Pypy, ajoutez «Pypy» et/ou «PYPY3» à la liste des pythons dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: python
python:
  - "2.7"
  - "3.8"
  # PyPy versions
  - "pypy"   # currently Python 2.7.18, PyPy 7.3.19
  - "pypy3"  # currently Python 3.10.16,  PyPy 7.3.19
# command to install dependencies
install:
  - pip install -r requirements.txt
  - pip install .
# command to run tests
script: pytest
```

<h3 id="support-construction-nocturne">Support de construction nocturne.
<a href="#support-construction-nocturne" class="ancre-titre after"></a></h3>

Travis CI prend en charge un nom de version spéciale `nightly`, qui pointe vers une version de développement récente de build [CPython](https://github.com/python/cpython).

<h3 id="support-developpement">Support des développements de versions.
<a href="#support-developpement" class="ancre-titre aftter"></a></h3>

À partir de Python 3.5, les versions Python en develmoppement sont disponibles.

Vous pouvez les spécifier dans vos builds avec `3.5-dev`, `3.6-dev`, `3.7-dev`, `3.8-dev`, `3.9-dev`, `3.10-dev`, `3.11-dev`, `3.12-dev`, `3.13-dev` ou `3.14-dev`.

<h2 id="script-par-defaut-python">Script de construction par défaut.
<a href="#script-par-defaut-python" class="ancre-titre aftter"></a></h2>

Les projets Python doivent fournir la clé `script` dans leur `.travis.yml` afin de spécifier la commande à utiliser pour exécuter les tests.
Par exemple, si votre projet utilise PyTest:

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
# command to run tests
script: pytest
```

S'il utilise `make test` à la place :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script: make test
```

Si vous ne fournissez pas de clé `script` dans un projet Python, Travis CI imprime un message («_Veuillez remplacer la clé script: dans votre .travis.yml pour exécuter des test_.») et la construction échouera.

<h2 id="tox">Utilisez TOX comme script de construction.
<a href="#tox" class="ancre-titre aftter"></a></h2>

En raison de la façon dont Travis est conçu, l'interaction avec [tox](https://tox.readthedocs.io/en/latest/) n'est pas simple. Comme décrit ci-dessus, Travis exécute déjà des tests à l'intérieur d'un virtualenv isolé chaque fois que `language: python` est spécifié. Veuillez donc garder cela à l'esprit lorsque vous créez d'autres environnements avec tox. Si vous préférez exécuter tox en dehors du virtualenv créé par Travis, il peut être préférable d'utiliser `language: generic` au lieu de` language: python`.

Si vous utilisez tox pour tester votre code sur plusieurs versions de Python, vous avez deux options : 

* Utilisez `language: generic` et installer manuellement les versions Python qui vous intéressent avant d'exécuter tox (sans l'installation manuelle, tox n'aura accès qu'aux versions Ubuntu Python par défaut dans une distribution choisie)  ;
* Utilisez `language: python` et une matrice de construction qui utilise une version différente de Python pour chaque branche (vous pouvez spécifier la version Python en utilisant la clé `python`). Cela garantira que les versions qui vous intéressent sont installées et paralléliseront votre charge de travail.

<h2 id="tests-python">Exécutez des tests Python sur plusieurs systèmes d'exploitation.
<a href="#tests-python" class="ancre-titre aftter"></a></h2>

Il est parfois nécessaire de s'assurer que les logiciels fonctionnent de la même manière sur plusieurs systèmes d'exploitation. Le fichier `.travis.yml` suivant exécutera des tests parallèles sous Linux et Windows.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: python            # this works for Linux but is an error on Windows
jobs:
  include:
    - name: "Python 3.8.0 on Xenial Linux"
      python: 3.8           # this works for Linux but is ignored on Windows
    - name: "Python 3.6.10 on FreeBSD"
      os: freebsd
      language: python
    - name: "Python 3.8.0 on Windows"
      os: windows           # Windows 10.0.17134 N/A Build 17134
      language: shell       # 'language: python' is an error on Travis CI Windows
      before_install:
        - choco install python --version 3.8.0
        - python -m pip install --upgrade pip
      env: PATH=/c/Python38:/c/Python38/Scripts:$PATH
install: pip3 install --upgrade pip  # all three OSes agree about 'pip3'
# 'python' points to Python 3.8 on Linux and Windows
# 'python3' is a 'command not found' error on Windows, but 'py' works on Windows only
script: python3 my_app.py || python my_app.py
```

<h2 id="gestion-des-dependances-python">Gestion des dépendances.
<a href="#gestion-des-dependances-python" class="ancre-titre aftter"></a></h2>

<h3 id="pip">pip.
<a href="#pip" class="ancre-titre aftter"></a></h3>

Par défaut, Travis CI utilise `pip` pour gérer les dépendances Python. Si vous avez un fichier `requirements.txt`, Travis CI exécute `pip install -r requirements.txt` pendant la phase d'installation de la construction.

Vous pouvez remplacer manuellement cette phase `install` par défaut, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install: pip install --user -r requirements.txt
```

Veuillez noter que l'option `--user` est obligatoire si vous n'utilisez pas le `language: python`, car aucun virtualenv ne sera créé dans ce cas.

**Nouveau résolveur de dépendance dans PIP 20.3**

Comme décrit dans l'annonce [PyPa](https://pip.pypa.io/en/latest/user_guide/#changes-to-the-pip-dependency-resolver-in-20-2-2020), `pipè` version 20.3 sera livrée avec un nouveau résolveur de dépendance. Cela peut avoir des changements inattendus dans votre logiciel ; lorsque nous déployons de nouvelles images de build avec cette version à une date future, vos constructions peuvent se casser en raison des modifications liées à cette version.

Pour tester les effets du nouveau résolveur de dépendance sur votre logiciel, nous vous conseillons de le tester avec `pip` version 20.2. Pour ce faire, modifiez votre version pour mettre à jour `pip` vers la version 20.2 et pour invoquer `pip` avec l'indicateur `--use-feature=2020-resolver`. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - python -m pip install --upgrade pip
install:
  - pip install --user -r requirements.txt --use-feature=2020-resolver
```

<h3 id="dependances-personnalisees">Gestion des dépendances personnalisées.
<a href="#dependances-personnalisees" class="ancre-titre aftter"></a></h3>

Pour remplacer la gestion de la dépendance `pip` par défaut, modifiez l'étape `before_install` comme décrit dans le [guide de configuration de build général](./personnalisation-de-la-construction.md#personnaliser-la-phase-installation).

<h3 id="plusieurs-versions-dependances">Tester avec plusieurs versions de dépendances.
<a href="#plusieurs-versions-dependances" class="ancre-titre aftter"></a></h3>

Si vous devez effectuer des tests sur plusieurs versions, par exemple de Django ou Flask, vous pouvez demander à Travis CI d'effectuer plusieurs exécutions avec différents ensembles ou valeurs de variables d'environnement.

Utilisez une clé `env` dans votre fichier `.travis.yml`, ci-dessous est un exemple de Django :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
env:
  - DJANGO_VERSION=2.1.13
  - DJANGO_VERSION=2.2.6
```

puis utilisez des valeurs de variables ENV dans les scripts d'installation de vos dépendances, les cas de test ou les valeurs de paramètres de script de test. Ici, nous utilisons une valeur de variable ENV pour demander à PIP d'installer une version exacte :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
  - pip install -q Django==$DJANGO_VERSION
  - python setup.py -q install
```

La même technique est souvent utilisée pour tester des projets dans plusieurs bases de données, etc.

Pour des exemples du monde réel, voir [`getsentry/sentry`](https://github.com/getsentry/sentry/blob/master/.travis.yml) et [`jpvanhal/flask-split`](https://github.com/jpvanhal/flask-split/blob/master/.travis.yml).

<h2 id="reference-de-configuration-de-construction-python">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-python" class="ancre-titre aftter"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Python](https://config.travis-ci.com/ref/language/python) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="exemples-python">Exemples.
<a href="#exemples-python" class="ancre-titre aftter"></a></h2>

* [tornadoweb/tornado](https://github.com/tornadoweb/tornado/blob/master/.travis.yml) ;
* [simplejson/simplejson](https://github.com/simplejson/simplejson/blob/master/.travis.yml) ;
* [fabric/fabric](http://github.com/fabric/fabric/blob/master/.travis.yml) ;
* [dstufft/slumber](https://github.com/dstufft/slumber/blob/master/.travis.yml) ;
* [dreid/cotools](https://github.com/dreid/cotools/blob/master/.travis.yml) ;
* [twisted/klein](https://github.com/twisted/klein/blob/master/.travis.yml).

<h2 id="tableau-versions-python">Versions Python.
<a href="#tableau-versions-python" class="ancre-titre aftter"></a></h2>

[Ces archives sont disponibles pour une installation à la demande](https://docs.travis-ci.com/user/languages/python/#python-versions).
<div class="invisible">