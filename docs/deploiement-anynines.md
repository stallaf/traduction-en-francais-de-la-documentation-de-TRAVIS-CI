# Déployer avec anynines

<div class="notice attention">
<p>
<i class="fa fa-flag" aria-hidden="true"></i><br>
Cette page documente les déploiements utilisant la version dpl v1, qui est actuellement la version héritée. La version dpl v2 est désormais disponible, et nous vous recommandons de l'utiliser. Veuillez consulter <a href="https://travis-ci.com/blog/2019-08-27-deployment-tooling-dpl-v2-preview-release">notre article de blog</a> pour plus de détails. La documentation relative à la <a href="https://docs.travis-ci.com/user/deployment-v2/">version dpl v2 est disponible ici</a>.
</div>

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#activer-la-version-edge">Activer la version Edge</a></li>
            <li><a href="#recuperer-le-code-et-le-deployer">Récupérer le code et le déployer</a></li>
            <li><a href="#ecrire-le-code-et-le-deployer">Écrire le code et le déployer</a></li>
        </ul>
    </div>
</div>
</p>

Vous avez maintenant la capacité incroyable de déployer directement sur [anynines](http://www.anynines.com/) après une construction réussie sur Travis CI.

<h2 id="activer-la-version-edge">Activer la version Edge.
<a href="#activer-la-version-edge" class="ancre-titre after"></a></h2>

La prise en charge de anynines appropriée est actuellement incluse uniquement dans la version edge de Travis. Voir comment l'activer via le `.travis.yml` ci-dessous.

<h2 id="recuperer-le-code-et-le-deployer">Récupérer le code et le déployer.
<a href="#recuperer-le-code-et-le-deployer" class="ancre-titre after"></a></h2>

Récupérez le gem Travis sur [GitHub](https://github.com/travis-ci/travis.rb) et exécutez cette commande :

```console
travis setup anynines
```

Il vous sera demandé de répondre à quelques questions simples sur votre configuration anynines et Travis s'occupera du reste !

Ouvrez votre `.travis.yml` nouvellement créé et ajoutez `edge: true` pour activer l'outil de déploiement. Voir le yml ci-dessous pour un exemple de la façon de procéder.

<h2 id="ecrire-le-code-et-le-deployer">Écrire le code et le déployer.
<a href="#ecrire-le-code-et-le-deployer" class="ancre-titre after"></a></h2>

Vous voulez donc écrire votre propre `.travis.yml`, très bien. Voici le minimum requis pour être opérationnel :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
 deploy:
   edge: true
   provider: anynines
   username: johndoe@example.com
   password: secretpassword
   organization: myorganization
   space: staging
   app_name: My app name                # (optional)
```

**_Assurez-vous de crypter votre mot de passe avant de pousser votre .travis.yml mis à jour vers GitHub._**

Cela peut être facilement réalisé à l'aide du gem Travis ci-dessus et en exécutant :

```console
travis encrypt --add deploy.password
```

<h3 id="versions-conditionnelles">Versions conditionnelles.
<a href="#versions-conditionnelles" class="ancre-titre after"></a></h3>

Vous ne pouvez déployer que lorsque certaines conditions sont remplies. Voir les [versions conditionnelles avec `on:`](./apercu-deploiements.md#versions).
<hr class="invisible">>
