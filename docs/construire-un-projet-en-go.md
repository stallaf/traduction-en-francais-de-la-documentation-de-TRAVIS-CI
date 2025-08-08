# Construire un projet en Go

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#specifier-une-version-de-go">Spécifier une version de Go</a></li>
            <li><a href="#modules-go">Modules Go</a></li>
            <li><a href="#chemin-importation-go">Chemin d'importation Go</a></li>
            <li><a href="#gestion-des-dependances-go">Gestion des dépendances</a></li>
            <li><a href="#script-de-construction-par-defaut-go">Script de construction par défaut</a></li>
            <li><a href="#variable-environnement-go">Variable d'environnement</a></li>
            <li><a href="#reference-de-configuration-de-construction-go">Référence de configuration de construction</a></li>
            <li><a href="#exemples-go">Exemples</a></li>
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
    <li><a href="environnement-windows.md">Windows</a> ;</li>
    <li><a href="environnement-freebsd">FreeBSD</a>.</li>
  </ul>
  </p>
  </div>

<p>
<table class="tableau">
    <thead>
        <tr><th>GO</th><th>DÉFAUT</th></tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="#gestion-des-dependances-go">Défaut</a><code>install</code></td>
            <td><code>travis_install_go_dependencies</code><br>
                     <code><go-version> [gobuild-args]</code></td>
        </tr>
        <tr>
            <td><a href="#script-de-construction-par-defaut-go">Défaut</a><code>script</code></td>
            <td><code>travis_script_go {gobuild-args}</code></td>
        </tr>
        <tr>
            <td><a href="#matrice-de-construction">Cle de matrice</a></td>
            <td><code>go</code>,<code>env</code><td>
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
language: go
```
        </th></tr>
    </tfoot>
</table>
</p>
</div>

Le reste de ce guide traite de la configuration des projets Go dans Travis CI.  Veuillez vous assurer de lire d'abord nos guides [d'intégration](./embarquement.md) et de [configuration générale de la construction](./personnalisation-de-la-construction.md).

<h2 id="specifier-une-version-de-go">Spécifiez une version de Go.
<a href="#specifier-une-version-de-go" class="ancre-titre after"></a></h2>

Vous pouvez utiliser n'importe quelle version taguée de Go à partir de https://go.dev/dl/, une version avec `x` à la place du niveau mineur ou du correctif pour utiliser le dernier pour une version majeure ou mineure donnée, ou utiliser `master` ou `stable` pour obtenir la dernière version depuis les sources.

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: go
go:
- "1.18"
- "1.22.4"
- "1.18.x"
- master
- stable
```

<h2 id="modules-go">Modules Go.
<a href="#modules-go" class="ancre-titre after"></a></h2>

Toute valeur définie pour `GO111MODULE` via `.travis.yml` ou les paramètres de référentiel est laissée tel quel. Si vous êtes absent, une valeur par défaut de `GO111MODULE=auto` est définie.

<h2 id="chemin-importation-go">Chemin d'importation Go.
<a href="#chemin-importation-go" class="ancre-titre after"></a></h2>

Le code source du projet sera placé dans `GOPATH/src/{repo-source}`,  mais si des [importations vanity](https://golang.org/cmd/go/#hdr-Remote_import_paths) sont nécessaires (en particulier pour les [importations de paquets `internal`](https://golang.org/cmd/go/#hdr-Internal_Directories)), `go_import_path:` peut être spécifié au niveau supérieur de la configuration, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
go_import_path: example.org/pkg/foo
```

<h2 id="gestion-des-dependances-go">Gestion des dépendances.
<a href="#gestion-des-dependances-go" class="ancre-titre after"></a></h2>

L'étape `install`  par défaut de `travis_install_go_dependencies <go-version> [gobuild-args]` se comportera différemment en fonction de la version Go spécifiée, ainsi que la présence de certaines variables d'environnement et de chemins de fichier.

Si un Makefile est présent par l'un des noms suivants, alors aucune autre action n'est prise à l'étape `install` : 

* `GNUMakefile` ;
* `Makefile` ;
* `BSDmakefile` ;
* `makefile`.

Dans tous les autres cas, la commande `go get ${gobuild_args} ./...` est exécutée.

<h3 id="support-godep">Support godep.
<a href="#support-godep" class="ancre-titre after"></a></h3>

Il y a un soutien inclus pour [godep](https://github.com/tools/godep) lorsqu'il est utilisé avec des dépendances vendues, de sorte que le `GOPATH` sera préfixé avec `${TRAVIS_BUILD_DIR}/Godeps/_workspace` et le `PATH` sera préfixé avec `${TRAVIS_BUILD_DIR}/Godeps/_workspace/bin`. De plus, si le répertoire `Godeps/_workspace/src` n'existe pas, `godep` sera installé et `godep restore` sera exécutée.

Il est important de noter que l'utilisation du style plus ancien, `Godeps.json ` au plus haut niveau n'est pas pris en charge.

Toutes les étapes d'intégration `godep` sont effectuées avant les étapes distinctes ` go get` and makefile énumérées ci-dessus.

Notez que le support `godep` n'est activé que si une étape `install` personnalisée n'est pas spécifiée.

<h3 id="installer-des-dependances-privees">Installer des dépendances privées.
<a href="#installer-des-dependances-privees" class="ancre-titre after"></a></h3>

Comme `go get` utilise HTTPS pour cloner les dépendances de GitHub plutôt que SSH,  cela nécessite une [solution différente de celle que nous recommandons pour gérer les dépendances privées](./dependances-privees.md).

Lors du clonage via HTTPS, git utilise curl en arrière-plan, ce qui vous permet de spécifier un fichier [.netrc](http://manpages.ubuntu.com/manpages/precise/man5/netrc.5.html) dans lequel vous pouvez stocker des informations d'authentification personnalisées pour des domaines spécifiques, comme github.com par exemple.

Accédez à votre [compte GitHub](https://github.com/settings/applications) et créez un jeton d'accès personnel.

![Capture d'écran du jeton personnel GitHub](https://docs.travis-ci.com/images/personal-token.jpg)

Assurez-vous de lui donner la portée du `repo`, qui permet d'accéder aux référentiels privés.

Pour réduire les droits d'accès du jeton, vous pouvez également créer un compte d'utilisateur distinct avec l'accès uniquement aux référentiels dont vous avez besoin pour un projet particulier.

Copiez le jeton et stockez-le dans un .netrc dans votre référentiel, avec les données suivantes :

```console
machine github.com
  login <username>
  password <token>
```

Ajoutez ceci à votre référentiel et ajoutez les étapes suivantes à votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
before_install:
- cp .netrc ~/.netrc
- chmod 600 ~/.netrc
```

<h2 id="script-de-construction-par-defaut-go">Script de construction par défaut.
<a href="#script-de-construction-par-defaut-go" class="ancre-titre after"></a></h2>

Les projets Go supposent que l'outil de compilation Make ou Go soit utilisé par défaut. Si un fichier Makefile est présent à la racine du référentiel, la commande par défaut utilisée par Travis CI pour exécuter la suite de tests de votre projet est :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
make
```

Dans le cas où il n'y a pas de makefile, ce sera :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
go test ${gobuild_args} ./...
```

à la place.

Ces commandes par défaut peuvent être remplacées comme décrit dans le guide de [configuration de build général](./personnalisation-de-la-construction.md). Par exemple, pour ajouter l'indicateur `-v`, remplacer la clé `script:` dans `.travis.yml` comme ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script: go test -v ./...
```

Les arguments transmis à la commande de `go test` par défaut peuvent être remplacés en spécifiant `gobuild_args:` au niveau supérieur de la configuration, par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
gobuild_args: -x -ldflags "-X main.VersionString v1.2.3"
```

ce qui entraînera l'étape de script :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
go test -x -ldflags "-X main.VersionString v1.2.3" ./...
```

Pour compiler en exécutant Scons sans arguments, utilisez ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
script: scons
```

<h2 id="variable-environnement-go">Variable d'environnement.
<a href="#variable-environnement-go" class="ancre-titre after"></a></h2>

La version de Go utilisée par la tâche est disponible sous :

```console
TRAVIS_GO_VERSION
```

Cela peut contenir `.x` à la fin, comme décrit ci-dessus. L'utilisation de cette variable dans la condition de déploiement devrait prendre en considération cette possibilité. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
go:
  - 1.7.x
⋮
deploy:
  ...
  on:
    condition: $TRAVIS_GO_VERSION =~ ^1\.7
```

<h2 id="reference-de-configuration-de-construction-go">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-go" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Go](https://config.travis-ci.com/ref/language/go) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).

<h2 id="exemples-go">Exemples.
<a href="#exemples-go" class="ancre-titre after"></a></h2>

* [Go](https://github.com/streadway/amqp/blob/master/.travis.yml) AMQP client ;
* [mrb/hob](https://github.com/mrb/hob/blob/master/.travis.yml).
<hr class="invisible">
