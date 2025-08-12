# Construire un projet Perl

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#specifier-versions-nodejs">Spécifier les versions Node.js</a></li>
            <li><a href="#versions-avec-nvmrc">Spécifier les versions Node.js à l'aide de .nvmrc</a></li>
            <li><a href="#script-de-build-par-defaut">Script de construction par défaut</a></li>
            <li><a href="#utiliser-gulp">Utiliser Gulp</a></li>
            <li><a href="#gestion-des-dependances-javascript">Gestion des dépendances</a></li>
            <li><a href="#ember">Applications Ember</a></li>
            <li><a href="#meteor">Applications Meteor</a></li>
            <li><a href="#paquetages-meteor"> Paquetages Meteor</a></li>
            <li><a href="#exigences-du-compilateur">Exigences du compilateur Node.js v4 (ou io.js v3)</a></li>
            <li><a href="#reference-de-compilation-de-construction-javascript">Référence de configuration de construction<a></li>
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
    <li><a href="environnement-macos.md">macOS</a> ;</li>
   <li><a href="environnement-windows.md">Windows</a> ;</li>
    <li><a href="environnement-freebsd.md">FreeBSD</a> ;</li>
  </ul>
  </p>
  </div>

Ce guide traite de la configuration des projets JavaScript et Node.js sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.
<p>
<table class="tableau">
    <thead>
        <tr><th>JAVASCRIPT et NODE.JS</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="#gestion-des-dependances-javascript">Défaut<code>install</code></td>
            <td><code>npm install</code>ou<code>npm ci</code></td>
        </tr>
        <tr>
            <td><a href="#script-de-buld-par-defaut">Défaut<code>script</code></td>
            <td><code>npm test</code></td>
        </tr>
        <tr>
            <td>Cle de matrice</a></td>
            <td><code>env</code>,<code>node_js</code><td>
        </tr>
        <tr>
            <td>Support</td>
            <td><a href="mailtosupport@travis-ci.com">Travis CI</a></td>
        </tr>
    </tbody>
    <tfoot>
        <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
        <tr><th colspan="2" text-align="left" style="font-weight:normal";>(Si le fichier <code>package-lock.json</code>ou <code>npm-shrinkwrap.json</code> existe et que votre version de npm le prend en charge, Travis CI utilisera <code>npm ci</code> à la place de <code>npm install</code>.)</th></tr>
        <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
        <tr><th colspan="2" text-align="left">Exemple minimal</th></tr>
        <tr><th colspan="2" text-align="left">&nbsp;</th></tr>
        <tr><th colspan="2">
            <div class="titre-code">
                <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
            </div>
```yml
language: node_js
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>

<h2 id="specifier-versions-nodejs">Spécifier les versions Node.js.
<a href="#specifier-versions-nodejs" class="ancre-titre after"></a></h2>