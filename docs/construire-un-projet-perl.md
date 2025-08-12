# Construire un projet Perl

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#specifier-versions-perl">Spécifier les versions Perl</a></li>
            <li><a href="#script-de-build-par-defaut-perl">Script de construction par défaut</a></li>
            <li><a href="#gestion-des-dependances-perl">Gestion des dépendances</a></li>
            <li><a href="#matrice-de-construction-perl">Matrice de consttruction</a></li>
            <li><a href="#variable-environnement-perl">Variable d'environnement</a></li>
            <li><a href="#reference-de-compilation-de-construction-perl">Référence de configuration de construction<a></li>
            <li><a href="#exemples-perl">Exemples</a></li>
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
    <li><a href="environnement-noble.md">Noble</a>.</li>
  </ul>
  </p>
  </div>

<p>
Les versions Perl ne sont pas disponibles sur l'environnement OS X.
</p>
<p>
Ce guide traite de la configuration des projets Perl sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.
</p>

<p>
<table class="tableau">
    <thead>
        <tr><th>PERL</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="#gestion-des-dependances-perl">Défaut<code>install</code></td>
            <td><code>cpanm --quiet --installdeps --notest .</code></td>
        </tr>
        <tr>
            <td><a href="#script-de-build-par-defaut-perl">Défaut<code>script</code></td>
            <td>Variable</td>
        </tr>
        <tr>
            <td>Cle de matrice</a></td>
            <td><code>perl</code>,<code>env</code><td>
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
language: perl
perl:
  - "5.28"
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>
</p>

<h2 id="specifier-versions-perl">Spécifier les versions Perl.
<a href="#specifier-versions-perl" class="ancre-titre after"></a></h2>

Travis CI utilise <a href="http://perlbrew.pl/">PerlBrew</a> pour fournir plusieurs versions Perl avec lesquelles vous pouvez tester l'ensemble de vos projets :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: perl
perl:
  - "5.30"
  - "5.28"
  - "5.26"
```

Ces versions spécifiées par des numéros `major.minor` sont des alias vers des niveaux de correctifs exacts, qui sont susceptibles d'être modifiés. Pour connaître les versions précises préinstallées sur la VM, veuillez consulter la section « Informations sur le système de compilation » dans le journal de construction. 

<div class="notice cadre">
Les versions Perl antérieures à 5,8 ne sont pas prises en charge.
</div>

<h3 id="perl-et-les-threads">Environnements d'exécution Perl avec prise en charge des threads.
<a href="#perl-et-les-threads" class="ancre-titre after"></a></h3>

Certaines versions de Perl ont été compilées avec le support des threads. Elles ont été construites avec les drapeaux de compilation supplémentaires `-Duseshrplib` et `-Duseithreads` :

```console
5.26-shrplib
5.24-shrplib
5.22-shrplib
5.20-shrplib
5.18-shrplib
```

<h2 id="script-de-build-par-defaut-perl">Script de construction par défaut.
<a href="#script-de-build-par-defaut-perl" class="ancre-titre after"></a></h2>

Le script de construction par défaut varie selon votre projet : 

* Si votre référentiel possède `Build.PL` à sa racine : 

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
perl Build.PL && ./Build test
```

* Si votre référentiel possède `makefile.pl`à sa racine : 

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
perl Makefile.PL && make test
```

* Si aucun n'est trouvé : 

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
make test
```

<h2 id="gestion-des-dependances-perl">Gestion des dépendances.
<a href="#gestion-des-dependances-perl" class="ancre-titre after"></a></h2>

Par défaut, Travis CI utilise `cpanm`` pour gérer les dépendances de votre projet.

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
cpanm --quiet --installdeps --notest .
```

<h3 id="remplacer-commandes-de-build">Remplacer les commandes de compilation ; ne pas utiliser sudo.
<a href="#remplacer-commandes-de-build" class="ancre-titre after"></a></h3>

Lorsque vous remplacez la clé `install:` pour modifier la commande d'installation des dépendances (par exemple, pour exécuter cpanm avec des indicateurs de verbosité), n'utilisez pas `sudo`. L'environnement Travis CI dispose de Perl installé via Perlbrew dans le répertoire `$HOME` d'un utilisateur non privilégié. L'utilisation de `sudo` entraînera l'installation des dépendances à des emplacements inattendus (pour le générateur Perl de Travis CI) et elles ne se chargeront pas.

<h2 id="matrice-de-construction-perl">Matrice de consttruction.
<a href="#matrice-de-construction-perl" class="ancre-titre after"></a></h2>

Pour les projets Perl, `env` et `perl` peuvent être donnés en tant que tableaux pour construire une matrice de construction.

<h2 id="variable-environnement-perl">Variables d'environnement.
<a href="#variable-environnement-perl" class="ancre-titre after"></a></h2>

La version de Perl utilisée par une tâche est disponible sous `TRAVIS_PERL_VERSION`.

<h2 id="reference-de-compilation-de-construction-perl">Référence de configuration de construction.
<a href="#reference-de-compilation-de-construction-perl" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Perl](https://config.travis-ci.com/ref/language/perl) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="exemples-perl">Exemples.
<a href="#exemples-perl" class="ancre-titre after"></a></h2>

*  [leto/math–primality](https://github.com/leto/math--primality/blob/master/.travis.yml) ;
* [fxn/algorithm-combinatorics](https://github.com/fxn/algorithm-combinatorics/blob/master/.travis.yml) ;
* [fxn/net-fluidinfo](https://github.com/fxn/net-fluidinfo/blob/master/.travis.yml) ;
* [fxn/acme-pythonic](https://github.com/fxn/acme-pythonic/blob/master/.travis.yml) ;
* [judofyr/parallol](https://github.com/judofyr/parallol/blob/travis-ci/.travis.yml) ;
* [mjgardner/SVN-Tree](https://github.com/mjgardner/SVN-Tree/blob/master/.travis.yml) ;
* [mjgardner/svn-simple-hook](https://github.com/mjgardner/svn-simple-hook/blob/master/.travis.yml).
<hr class="invisible">

