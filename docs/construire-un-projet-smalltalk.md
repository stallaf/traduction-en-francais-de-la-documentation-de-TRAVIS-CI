# Construire-un-projet-smalltalk.md
    
<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#communaute-smalltalk">Avertissement soutenu par la communauté</a></li>
            <li><a href="#configuration-de-base">Configurations de base</a></li>
            <li><a href="#configuration">Configuration</a></li>
            <li><a href="#reference-de-configuration-de-construction-smalltalk">Référence de configuration de construction</a></li>
        </ul>
    </div>
</div>
</p>

Ce guide traite de la configuration des projets Smalltalk sur Travis CI. Si vous découvrez Travis CI, veuillez vous assurer de lire d'abord nos guides <a href="./embarquement.md">d'intégration</a> et de <a href="./personnalisation-de-la-construction.md">configuration générale de la construction</a>.

<h2 id="communaute-smalltalk">Avertissement soutenu par la communauté.
<a href="#communaute-smalltalk" class="ancre-titre after"></a></h2>

Le support de Travis CI pour Smalltalk est fourni par [`SmalltalkCI`](https://github.com/hpi-swa/smalltalkCI) et apporté par la communauté et peut être supprimé ou modifié à tout moment. Si vous rencontrez des problèmes, veuillez les [signaler ici](https://github.com/hpi-swa/smalltalkCI/issues).

<h2 id="configuration-de-base">Configurations de base.
<a href="#configuration-de-base" class="ancre-titre after"></a></h2>

Pour configurer l'intégration continue pour votre projet Smalltalk, vous avez besoin d'un fichier de configuration `.travis.yml` et `.ston` pour SmalltalkCI.

Un exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: smalltalk
# Select operating system(s)
os:
  - linux
# Select virtual machine(s)
smalltalk_vm:
  - Squeak-5.0
  - Pharo-5.0
# Select compatible Smalltalk image(s)
smalltalk:
  - Squeak-trunk
  - Squeak-5.0
  - Squeak-4.6
  - Squeak-4.5
  - Pharo-alpha
  - Pharo-stable
  - Pharo-5.0
  - Pharo-4.0
  - Pharo-3.0
  - GemStone-3.3.0
  - GemStone-3.2.12
  - GemStone-3.1.0.6
```

Il s'agit d'un `.smalltalk.ston` minimal qui utilise [Metacello](https://github.com/dalehenrich/metacello-work) pour tester toutes les plates-formes prises en charge :

<div class="titre-code">
    <div class="item gauche">&nbsp;.smalltalk.ston</div><div class="item droit">Javascript</div>
</div>
```yml
SmalltalkCISpec {
  #loading : [
    SCIMetacelloLoadSpec {
      #baseline : 'MyProject',
      #directory : 'packages',
      #platforms : [ #squeak, #pharo, #gemstone ]
    }
  ]
}
```

<h2 id="configuration">Configuration.
<a href="#configuration" class="ancre-titre after"></a></h2>

Cette documentation n'est qu'un exemple minimal et n'est pas aussi exhaustive que le fichier [`README.md` de SmalltalkCI](https://github.com/hpi-swa/smalltalkCI#templates).

<h2 id="reference-de-configuration-de-construction-smalltalk">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction-smalltalk" class="ancre-titre after"></a></h2>

Vous pouvez trouver plus d'informations sur le format de configuration de build pour [Smalltalk](https://config.travis-ci.com/ref/language/smalltalk) dans notre [référence de configuration de construction Travis CI](https://config.travis-ci.com/).
<hr class="invisible">
