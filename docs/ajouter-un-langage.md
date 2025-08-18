# Langues soutenues par la communauté

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#communaute-generale">Que signifie « pris en charge par la communauté » ?</a></li>
            <li><a href="#ajout-nouvelle-langue">Ajouter une nouvelle langue prise en charge par la communauté</a></li>
            <li><a href="#details-techniques">Détails techniques</a></li>
            <li><a href="#liste-des-langages">Liste des langues prises en charge par la communauté</a></li>
        </ul>
    </div>
</div>
</p>

Il existe de nombreux langages de programmation, et Travis CI souhaite en soutenir autant que possible.

Cependant, l'équipe de Travis CI n'a souvent pas l'expertise pour en faire une réalité, c'est là que le soutien communautaire entre en jeu.

<h2 id="communaute-generale">Que signifie « pris en charge par la communauté » ?.
<a href="#communaute-generale" class="ancre-titre after"></a></h2>

Les langages soutenus par la communauté sont des langages de programmation dont le support est assuré par des experts auto-identifiés au sein de la communauté respective de ces langages.

<h2 id="ajout-nouvelle-langue">Ajouter une nouvelle langue prise en charge par la communauté.
<a href="#ajout-nouvelle-langue" class="ancre-titre after"></a></h2>

1. Rassemblez un groupe de 3 bénévoles ou plus qui soutiendront la nouvelle langue. 
2. Créez des _pull requests_ dans [travis-build](https://docs.travis-ci.com/user/languages/community-supported-languages/#adding-a-new-language) et si nécessaire [travis-web](https://docs.travis-ci.com/user/languages/community-supported-languages/#adding-matrix-support). 
4. [Testez vos modifications](#tester-les-modifications-apportees-a-travis-build). 
5. Collaborez avec l'équipe Travis CI pour que les _PRs_ soient prêts pour la production.
6. Fournir un soutien continu aux problèmes impliquant la langue.

Un groupe de 3 est un minimum pour soutenir un langage. Cela permet une redondance à fournir un soutien lorsqu'un membre de l'équipe d'assistance n'est pas disponible.

<h2 id="details-techniques">Détails techniques.
<a href="#details-techniques" class="ancre-titre after"></a></h2>

Il est important de noter que les langages sont configurés au moment de la construction, de sorte que les composants sont téléchargés chaque fois qu'une tâche s'exécute. Pour gagner du temps de construction, limitez votre utilisation des ressources linguistiques au minimum.

<h3 id="nouvelle-langue">Ajouter une nouvelle langue.
<a href="#nouvelle-langue" class="ancre-titre after"></a></h3>

Pour ajouter la prise en charge d'une nouvelle langue, modifiez [travis-build](https://github.com/travis-ci/travis-build) et créez une nouvelle classe, héritant de `Travis::Build::Script`, qui implémente des valeurs par défaut raisonnables pour les étapes de compilation de votre langue.

Au minimum, implémentez les étapes suivantes :

```console
configure → setup → announce → install → script
```

Il existe d'autres phases qui peuvent être personnalisées pour une langue particulière ; l'équipe Travis CI travaillera avec vous pour identifier et implémenter la personnalisation si vous pensez qu'il est approprié de le faire. 

<div class="notice cadre">
La phase <code>configure</code> s'exécute avant que <code>sudo</code> ne soit désactivé dans les versions de conteneurs, donc si vous avez besoin d'utiliser <code>sudo</code> pour configurer votre environnement linguistique (par exemple, installer les paquetages Ubuntu), vous devez le faire dans la phase <code>configure</code>.
</div>

Si vous souhaitez prendre en charge l'expansion de la matrice de construction basée sur diverses versions de langue (par exemple, Ruby 2.2, 2.1, etc.), et vous souhaitez ajouter un moyen pratique de restreindre les déploiements en fonction de la version linguistique, ajoutez votre langue à [`Travis::Build::Addons::Deploy::Script::VERSIONED_RUNTIMES`](https://github.com/travis-ci/travis-build/blob/master/lib/travis/build/addons/deploy/script.rb).

<h3 id="matrice">Ajouter la prise en charge de la Matrice.
<a href="#matrice" class="ancre-titre after"></a></h3>

Si la langue fournit une expansion de la matrice de construction, rendez ces informations visibles pour l'utilisateur final en modifiant [travis-web](https://github.com/travis-ci/travis-web).

Pour ce faire, vous devez demander à `travis-web` de récupérer la valeur dans les données de la tâche et de l'afficher. Clonez le référentiel [travis-web], ajoutez votre langue au fichier `app/utils/keys-map.coffee`, puis soumettez une _pull request_ pour cette modification.

<div class="notice cadre">
Si vous souhaitez prendre en charge l'expansion de la matrice de construction pour diverses versions de langage (par exemple, Ruby 2.2, 2.1, etc.), veuillez coordonner avec l'équipe Travis CI pour savoir exactement ce qui est nécessaire.
</div>

<h3 id="modifications">Tester les modifications apportées à Travis-Build.
<a href="#modifications" class="ancre-titre after"></a></h3>

Les tests sont effectués dans notre environnement de construction, qui est une ressource partagée. Tester les modifications proposées pourrait nécessiter une certaine coordination entre vous et l'équipe Travis CI.

**Tester le code localement**

En option, vous pouvez utiliser [`travis-build` comme module complémentaire](https://github.com/travis-ci/travis-build/blob/master/README.md#use-as-addon-for-cli) à l'utilitaire CLI. Cela vous permet de compiler le code `travis-build` sur lequel vous travaillez dans un script Bash, dont vous pouvez ensuite vérifier la syntaxe (`bash -n`) et l'exécuter (nous vous recommandons de le faire sur une machine virtuelle) pour faciliter votre développement.

<h2 id="liste-des-langages">Liste des langages soutenues par la communauté.
<a href="#liste-des-langages" class="ancre-titre after"></a></h2>

Les voici par ordre alphabétique :

1. [C#](./construire-un-projet-en-c-sharp.md) ;
2. [Crystal](./construire-un-projet-crystal.md) ;
3. [D](./construire-un-projet-en-d.md) ; 
4. [Dard](./construire-un-projet-dart.md) ; 
5. [Haxe](./construire-un-projet-haxe.md) ; 
6. [Julia](./construire-un-projet-julia.md) ; 
7. [Rust](./construire-un-projet-rust.md) ;
8. [Perl 6](./construire-un-projet-perl6.md) ;
9. [R](./construire-un-projet-en-r.md) ; 
10. [Smalltalk](./construire-un-projet-smalltalk.md).
<hr class="invisible">
