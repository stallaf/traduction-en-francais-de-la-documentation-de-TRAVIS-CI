# Construire un projet en C++

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#environnement-ci-pour-les-projets-c++">Environnement CI pour les projets C++</a></li>
            <li><a href="#gestion-des-dependances-c++">Gestion des dépendances</a></li>
            <li><a href="#tests-des-compilateurs-c++">Tests des compilateurs</a></li>
            <li><a href="#exemples-c++">Exemples</a></li>
            <li><a href="#openmp-c++">Projets OpenMP</a></li>
            <li><a href="#mpi-c++">Projets MPI</a></li>
            <li><a href="#c++11">C11/C++11 (et au-delà) et gestion des versions de la chaîne d'outils</a></li>
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
    <li><a href="environnement-noble.md">Noble</a> ;</li>
    <li><a href="environnement-macos">macOs</a> ;</li>
    <li><a href="environnement-windows.md">Windows</a> ;</li>
    <li><a href="environnement-freebsd">FreeBSD</a>.</li>
  </ul>
  </p>
  </div>
    <p>
<table class="tableau">
    <thead>
        <tr><th style="text-alin:left">C++</th><th style="text-align:left">Défaut</th></tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="#gestion-des-dependances">Défaut</a><code>install</code></td>
            <td>N/A</td>
        </tr>
        <tr>
            <td><a href="#script-de-build-par-defaut">Défaut</a><code>script</code></td>
            <td><code>./configure && make && make test</code></td>
        </tr>
        <tr>
            <td><a href="#matrice-de-construction">Cle de matrice</a></td>
            <td><code>env</code>,<code>compiler</code><td>
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
                language: cpp
                ```
        </th></tr>
     </tfoot>
</table>
</p>
</div>

Ce guide traite des sujets liés à l'environnement de construction et à la configuration spécifiques aux projets C++. Veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de la construction](./personnalisation-de-la-construction.md).

<h2 id="environnement-ci-pour-les-projets-c++">Environnement CI pour les projets C++.
<a href="#environnement-ci-pour-les-projets-c++" class="ancre-titre after"></a></h2>

Les VM Travis CI sont 64 bits et fournissent des versions de : 

* gcc ;
* clang ;
* core GNU build toolchain (autotools, make), cmake, scons.

Les projets C ++ sur Travis CI supposent que vous utilisez des auto-tools et Make par défaut.

Pour des versions précises sur machine virtuelle, veuillez consulter «Build System Information» dans le journal de construction.

<h2 id="gestion-des-dependances-c++">Gestion des dépendances.
<a href="#gestion-des-dependances-c++" class="ancre-titre after"></a></h2>

Parce qu'il n'y a pas de convention dominante dans la communauté concernant la gestion des dépendances, Travis CI saute l'installation de dépendance pour les projets C ++.

Si vous devez installer des dépendances avant que vos tests puissent s'exécuter, remplacez la clé `install:` dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install: make get-deps
```

Voir le [guide de configuration de construction](./personnalisation-de-la-construction.md) pour en savoir plus.

<h2 id="tests-des-compilateurs-c++">Tests des compilateurs.
<a href="#tests-des-compilateurs-c++" class="ancre-titre after"></a></h2>

Vous pouvez tester vos projets avec GCC ou Clang, ou les deux. Pour ce faire, spécifiez le compilateur à utiliser à l'aide de la clé `compiler:` dans le fichier `.travis.yml`. Par exemple, pour compiler avec Clang :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
compiler: clang
```

ou GCC et Clang :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
compiler:
  - clang
  - gcc
```

Le test avec deux compilateurs créera (au moins) deux lignes dans votre matrice de build. Pour chaque ligne, le générateur C++ de Travis CI exportera les variables d'environnement `CXX` et `CXX_FOR_BUILD` pour pointer vers `g++` ou `clang++`, et exportera en conséquence les variables d'environnement `CC` et `CC_FOR_BUILD` pour pointer vers `gcc` ou `clang`.

<h2 id="exemples-c++">Exemples.
<a href="#exemples-c++" class="ancre-titre after"></a></h2>

* [Rubinius](https://github.com/rubinius/rubinius/blob/master/.travis.yml).

<h2 id="openmp-c++">Projets OpenMP.
<a href="#openmp-c++" class="ancre-titre after"></a></h2>

Les projets OpenMP doivent définir la variable d'environnement `OMP_NUM_THERDS` sur une valeur raisonnablement faible (disons, 4). OpenMP détecte les cœurs du matériel d'hébergement, plutôt que la machine virtuelle sur laquelle vos tests s'exécutent.

<h2 id="mpi-c++">Projets MPI.
<a href="#mpi-c++" class="ancre-titre after"></a></h2>

Les variables d'environnement par défaut `$CC` et `$CXX` sont connues pour interférer avec les projets MPI. Dans ce cas, nous vous recommandons de les désactiver:

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
  - test -n $CC  && unset CC
  - test -n $CXX && unset CXX
```

<h2 id="c++11">C11/C++11 (et au-delà) et gestion des versions de la chaîne d'outils.
<a href="#c++11" class="ancre-titre after"></a></h2>

Si votre projet nécessite des outils compatibles avec C11, C++11 ou une norme de langage plus récente, il est probable que vous devriez mettre à niveau votre compilateur et/ou des outils de construction. Cette section couvre spécifiquement comment mettre à niveau GCC, clang et cmake ; Pour d'autres dépendances, veuillez consulter [l'installation des dépendances](./installation-des-dependances.md).

<h3 id="gcc-sur-linux-en-c++">GCC sur Linux.
<a href="#gcc-sur-linux-en-c++" class="ancre-titre after"></a></h3>

* [Precise](./environnement-precise.md) navigue avec GCC 4.6.3 ;
* [Trusty](./environnement-trusty) navigue avec GCC 4.8.2 ;
* [Xenial](./environnement-xenial) navigue avec GCC 5.4.0 ;
* [Bionic](./environnement-bionic) navigue avec GCC 7.4.0.

Notez que la prise en charge de[GCC pour ISO C11 a atteint un niveau d'exhaustivité similaire à celui de l'ISO C99 en 4.9](https://gcc.gnu.org/wiki/C11Status) et que C++11 est complet en fonctionnalité en 5.1 (la prise en charge du langage C ++ était complète des fonctionnalités en 4.8.1, mais la bibliothèque standard n'a pas pris en charge toutes les fonctionnalités C++11 que [plus tard](https://gcc.gnu.org/gcc-5/changes.html#libstdcxx), en particulier le [support pour <regex> n'existe que sur 4.9](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=53631)).

Pour mettre à niveau GCC vers une version plus récente, vous devrez installer la version appropriée à partir des sources `ubuntu-toolchain-r-test`; Voir ci-dessous pour des exemples :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
matrix:
  include:
    # works on Precise and Trusty
    - os: linux
      addons:
        apt:
          sources:
            - ubuntu-toolchain-r-test
          packages:
            - g++-4.9
      env:
         - MATRIX_EVAL="CC=gcc-4.9 && CXX=g++-4.9"
    # works on Precise and Trusty
    - os: linux
      addons:
        apt:
          sources:
            - ubuntu-toolchain-r-test
          packages:
            - g++-5
      env:
         - MATRIX_EVAL="CC=gcc-5 && CXX=g++-5"
    # works on Precise and Trusty
    - os: linux
      addons:
        apt:
          sources:
            - ubuntu-toolchain-r-test
          packages:
            - g++-6
      env:
        - MATRIX_EVAL="CC=gcc-6 && CXX=g++-6"
    # works on Precise and Trusty
    - os: linux
      addons:
        apt:
          sources:
            - ubuntu-toolchain-r-test
          packages:
            - g++-7
      env:
        - MATRIX_EVAL="CC=gcc-7 && CXX=g++-7"
before_install:
    - eval "${MATRIX_EVAL}"
```

<h3 id="gcc-sur-freebsd-en-c++">GCC sur FreeBSD.
<a href="#gcc-sur-freebsd-en-c++" class="ancre-titre after"></a></h3>

L'image Travis CI FreeBSD est livrée avec GCC 10.0.0 (il n'est pas dans le système de base par défaut).

Pour mettre à niveau GCC vers une version plus récente, installez la version appropriée à partir des paquetages ; Voir ci-dessous pour des exemples :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
os: freebsd
addons:
    pkg:
      - gcc10
    env:
      - CC=gcc10
      - CXX=g++10
```

<h3 id="clang-sur-linux-en-c++">Clang sur Linux.
<a href="#clang-sur-linux-en-c++" class="ancre-titre after"></a></h3>

* [Precise](./environnement-precise) est livré avec Clang 3.4 ;
* [Trusty](./environnement-trusty) est livré avec Clang 3.5.0 ;
* [Xenial](./environnement-xenial) est livré avec Clang 7 ;
* [Bionic](./environnement-bionic) est livré avec Clang 7.

Notez que le [support C++11 est complet à partir de Clang 3.3](http://clang.llvm.org/cxx_status.html).

Pour mettre à niveau Clang vers une version plus récente, vous devrez installer la version appropriée à partir d'une source `llvm-toolchain-*` (la source `ubuntu-toolchain-r-test` doit également être retirée pour une résolution de dépendance) ; Voir ci-dessous pour des exemples :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
matrix:
  include:
    # works on Precise and Trusty
    - os: linux
      addons:
        apt:
          sources:
            - ubuntu-toolchain-r-test
            - llvm-toolchain-precise-3.6
          packages:
            - clang-3.6
      env:
        - MATRIX_EVAL="CC=clang-3.6 && CXX=clang++-3.6"
    # works on Precise and Trusty
    - os: linux
      addons:
        apt:
          sources:
            - ubuntu-toolchain-r-test
            - llvm-toolchain-precise-3.7
          packages:
            - clang-3.7
      env:
        - MATRIX_EVAL="CC=clang-3.7 && CXX=clang++-3.7"
    # works on Precise and Trusty
    - os: linux
      addons:
        apt:
          sources:
            - ubuntu-toolchain-r-test
            - llvm-toolchain-precise-3.8
          packages:
            - clang-3.8
      env:
        - MATRIX_EVAL="CC=clang-3.8 && CXX=clang++-3.8"
    # works on Trusty
    - os: linux
      addons:
        apt:
          sources:
            - llvm-toolchain-trusty-3.9
          packages:
            - clang-3.9
      env:
        - MATRIX_EVAL="CC=clang-3.9 && CXX=clang++-3.9"
    # works on Trusty
    - os: linux
      addons:
        apt:
          sources:
            - llvm-toolchain-trusty-4.0
          packages:
            - clang-4.0
      env:
        - MATRIX_EVAL="CC=clang-4.0 && CXX=clang++-4.0"
    # works on Trusty
    - os: linux
      addons:
        apt:
          sources:
            - llvm-toolchain-trusty-5.0
          packages:
            - clang-5.0
      env:
        - MATRIX_EVAL="CC=clang-5.0 && CXX=clang++-5.0"
before_install:
    - eval "${MATRIX_EVAL}"
```

<h3 id="clang-sur-freebsd-en-c++">Clang sur FreeBSD.
<a href="#clang-sur-freebsd-en-c++" class="ancre-titre after"></a></h3>

<div class="notice cadre">
Clang est le compilateur par défaut sur FreeBSD.
</div>

FreeBSD est livré avec Clang 8.0.1

Pour mettre à niveau Clang vers une version plus récente, installez la version appropriée à partir des paquetages ; Voir ci-dessous pour des exemples :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
os: freebsd
addons:
    pkg:
      - llvm90
    env:
      - CC=/usr/local/bin/clang90    # llvm90 installs it to /usr/local/bin/clang90
      - CXX=/usr/local/bin/clang++90 # llvm90 installs it to /usr/local/bin/clang++90
```

**Cmake**

* [Precise](./environnement-precise) est livré avec CMake 2.8.7 ;
* [Trusty](./environnement-trusty) est livré avec CMake 3.9.2 ;
* [Xenial](./environnement-xenial) est livré avec CMake 3.12.4 ;
* [Bionic](./environnement-bionic) est livré avec CMake 3.12.4 ;
* [FreeBSD](./environnement-freebsd) est livré avec CMake 3.15.5.

Vous pouvez mettre à niveau cmake vers la version 3.2.3 sur Precise à partir de la source `george-edison55-precise-backports` (notez que le paquet `cmake-data` contient des dépendances que Aptitude ne résout pas automatiquement), cf :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake-data
      - cmake
```
<hr class="invisible">
