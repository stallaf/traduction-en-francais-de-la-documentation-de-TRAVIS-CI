# Construire un projet Perl 6

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#communaute-perl6">Avertissement soutenu par la communauté</a></li>
            <li><a href="#test-perl6">Test par rapport aux versions Perl 6</a></li>
            <li><a href="#stack-perl6">Stack Perl 6</a></li>
            <li><a href="#version-perl6">Version Perl 6 par défaut</a></li>
            <li><a href="#gestion-des-dependances-perl6">Gestion des dépendances</a></li>
            <li><a href="#variable-environnement-perl6">Variable d'environnement</a></li>
            <li><a href="#exemples-perl6">Exemples</a></li>
            <li><a href="#reference-de-configuration-de-construction-perl6">Référence de configuration de construction</a></li>
        </ul>
    </div>
</div>
</p>

<div class="deux-colonnes">
<p>
Ce guide couvre l'environnement de construction et les sujets de configuration spécifiques aux projets Perl 6.  Veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.
</p>
<p>
Les versions Perl 6 ne sont pas disponibles sur l'environnement macOS.
</p>

<h2 id="communaute-perl6">Avertissement soutenu par la communauté.
<a href="#communaute-perl6" class="ancre-titre after"></a></h2>

Le soutien de Travis CI pour Perl 6 est apporté par la communauté et peut être supprimé ou modifié à tout moment. Si vous rencontrez des problèmes, veuillez les signaler dans le <a href="https://github.com/travis-ci/travis-ci/issues/new?labels=community:perl6">Tracker Travis CI Issue</a> avec CC à <a href="https://github.com/paultcochrane">@paultcochrane</a>,  <a href="https://github.com/hoelzro">@hoelzro</a>,<a href="https://github.com/ugexe">@ugexe</a> et à <a href="(https://github.com/tony-o">@ tony-o</a>.

<p>
<table class="tableau">
    <thead>
        <tr><th>PERL 6</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="#gestion-des-dependances-perl6">Défaut<code>install</code></td>
            <td>N/A</td>
        </tr>
        <tr>
            <td><a href="#script-de-build-par-defaut-perl6">Défaut<code>script</code></td>
            <td><code>PERL6LIB=lib prove --ext .t --ext .t6 -v -r --exec=perl6 t/</code></td>
        </tr>
        <tr>
            <td>Cle de matrice</a></td>
            <td><code>env</code>,<code>perl6</code><td>
        </tr>
        <tr>
            <td>Support</td>
            <td><a href="https://travis-ci.community/c/languages/perl6">Soutien communautaire</a></td>
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
language: perl6
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>
</p>

<h2 id="test-perl6">Test par rapport aux versions Perl 6.
<a href="#test-perl6" class="ancre-titre after"></a></h2>

L'exécution de Perl 6 sur Travis CI utilise `rakudobrew` pour fournir plusieurs versions Perl 6 sur lesquelles vos projets peuvent être testés. Pour les spécifier, utilisez la clé `perl6:` dans votre fichier `.travis.yml`, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: perl6
perl6:
  - latest
  - '2017.05'
  - '2017.04'
```

Au fil du temps, de nouvelles versions sont publiées et nous mettons à jour à la fois rakudobrew et Perls. Les alias comme `2017.05` fluctuent et pointent vers différentes versions exactes, niveaux de correctifs, etc.

Pour les versions précises préinstallées sur la machine virtuelle, veuillez consulter les «informations de construction du système» dans le journal de construction.

<h2 id="stack-perl6">Stack Perl 6.
<a href="#stack-perl6" class="ancre-titre after"></a></h2>

À l'heure actuelle, le Perl 6 qui est construit est [Rakudo](http://rakudo.org/) sur [NQP](https://github.com/perl6/nqp/) avec le backend [MoarVM](http://moarvm.org/). La prise en charge future du backend [JVM]'http://en.wikipedia.org/wiki/Java_virtual_machine est prévue.

<h2 id="version-perl6">Version Perl 6 par défaut.
<a href="#version-perl6" class="ancre-titre after"></a></h2>

Si vous omettez la clé `perl6` dans votre fichier `.travis.yml `, Travis CI compilera Rakudo Perl 6 à partir du dernier commit de la branche `master` du projet.

<h2 id="gestion-des-dependances-perl6">Gestion des dépendances.
<a href="#gestion-des-dependances-perl6" class="ancre-titre after"></a></h2>

<h3 id="gestion-automatisee">Gestion automatisée des dépendances non disponible.
<a href="#gestion-automatisee" class="ancre-titre after"></a></h3>

À l'heure actuelle, Travis CI ne gère pas automatiquement les dépendances de votre projet par défaut. Vous pouvez gérer vous-même les dépendances en les téléchargeant et en les installant lors de l'étape `install`, ou en utilisant [zef](https://github.com/ugexe/zef) (le gestionnaire de paquets Perl 6) comme suit :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
    - rakudobrew build-zef
    - zef --debug --depsonly install .
```

Cela installera la dernière version `zef`.

Pour plus d'informations sur le remplacement des commandes d'installation des dépendances, consultez le guide général de [configuration de la compilation](./personnalisation-de-la-construction.md).

<h3 id="pas-sudo">Remplacer les commandes de compilation ; ne pas utiliser sudo.
<a href="#pas-sudo" class="ancre-titre after"></a></h3>

Lorsque vous remplacez la clé `install:` pour modifier les commandes d'installation des dépendances, n'utilisez pas sudo. L'environnement Travis CI dispose de versions Perl 6 installées via rakudobrew dans le répertoire `$HOME` d'un utilisateur non privilégié. L'utilisation de sudo entraînera l'installation des dépendances à des emplacements inattendus (pour le générateur Travis CI Perl 6) et elles ne se chargeront pas.

<h2 id="variable-environnement-perl6">Variable d'environnement.
<a href="#variable-environnement-perl6" class="ancre-titre after"></a></h2>

La version Perl 6 utilisée par une tâche est disponible via :

```console
TRAVIS_PERL6_VERSION
```

<h2 id="exemples-perl6">Exemples.
<a href="#exemples-perl6" class="ancre-titre after"></a></h2>

<h3 id="rakudo">Construire et tester avec le dernier Rakudo.
<a href="#rakudo" class="ancre-titre after"></a></h3>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: perl6
perl6:
    - latest
install:
    - rakudobrew build-zef
    - zef --debug --depsonly install .
```

<h3 id="version-rakudo">Construire et tester avec plusieurs versions Rakudo.
<a href="#version-rakudo" class="ancre-titre after"></a></h3>

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: perl6
perl6:
    - '2017.05'
    - '2017.04'
install:
    - rakudobrew build-zef
    - zef --debug --depsonly install .
```

<h3 id="test-non-standard">Compilez et testez avec la dernière version de Rakudo en utilisant des répertoires lib et test non standard.
<a href="#test-non-standard" class="ancre-titre after"></a></h3>

Utiliser par exemple, `src/` pour le code de la bibliothèque du module, et `tests/` comme répertoire de test. Veuillez noter qu'il est standard de mettre le code de bibliothèque de modules sous `lib/` et les tests sous `t/`.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: perl6
script:
    - PERL6LIB=src prove --ext .t --ext .t6 -v -r --exec=perl6 tests/
```

<h2 id="reference-de-configuration-de-construction-perl6">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-perl6" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Perl6](https://config.travis-ci.com/ref/language/perl6) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<hr class="invisible">