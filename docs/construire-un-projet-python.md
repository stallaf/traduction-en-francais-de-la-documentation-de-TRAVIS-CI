# Construire un projet Python

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#specifier-versions-python">Spécifier les versions Python</a></li>
            <li><a href="#script-par-defaut-python">Script de construction par défaut</a></li>
            <li><a href="#tox">Utiliser Tox comme script de compilation</a></li>
            <li><a href="#tests-python">Exécuter les tests Python sur plusieurs systèmes d'exploitation</a></li>
            <li><a href="#gestion-des-dependances-python">Gestion des dépendances</a></li>
            <li><a href="#reference-de-configuration-de-construction-python">Référence de configuration de construction</a></li>
            <li><a href="#exemples-php">Exemples</a></li>
            <li><a href="#tableau-versions-php">Versions Python</a></li>
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

