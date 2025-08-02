# Ajouter SSH aux hôtes connus

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#implication-en-matiere-de-securite">Implications en matière de sécurité</a></li>
            <li><a href="#mesures-pour-attenuer-et-solutions-de-contournement">Mesures d'atténuation et solutions de contournement</a></li>
        </ul>
        </ul>
    </div>
</div>
</p>

Travis CI peut ajouter des entrées à `~/.ssh/known_hosts` avant de cloner votre dépôt Git, ce qui est nécessaire s'il existe des sous-modules Git provenant de domaines autres que `github.com`, `gist.github.com` ou `ssh.github.com`.

Les noms d'hôte et les adresses IP sont pris en charge, car les clés sont ajoutées via `ssh-keyscan`. Un seul hôte peut être spécifié comme ceci :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    ssh_known_hosts: git.example.com
```

Plusieurs hôtes ou IP peuvent être ajoutés en tant que liste :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    ssh_known_hosts:
    - git.example.com
    - 111.22.33.44
```

Les hôtes avec des ports peuvent également être spécifiés :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
addons:
    ssh_known_hosts: git.example.com:1234
```

<h2 id="implication-en-matiere-de-securite">Implications de matière de sécurité.
<a href="#implication-en-matiere-de-securite" class="ancre-titre after"></a></h2>

Notez que l'option `ssh_known_hosts` peut présenter un risque d'attaques de type « man-in-the-middle » pour vos builds. (Voir également la section _Sécurité_ de la [page de manuel ssh-keyscan](https://linux.die.net/man/1/ssh-keyscan).) Par exemple, elle peut empêcher un build de détecter qu'un tiers illégitime tente d'injecter un dépôt git ou un sous-module modifié dans le build. Cette possibilité peut être particulièrement pertinente lorsque les résultats des builds Travis CI sont utilisés pour des paquetages de publication ou des déploiements en production.

<h2 id="mesures-pour-attenuer-et-solutions-de-contournement">Mesures d'atténuation et solutions de contournement.
<a href="#mesures-pour-attenuer-et-solutions-de-contournement" class="ancre-titre after"></a></h2

Actuellement, Travis CI détecte uniquement les attaques ci-dessus à l'extérieur des référentiels sur `github.com`, `gist.github.com` ou `ssh.github.com`. Si vous hébergez votre code sur d'autres domaines, il n'y a actuellement aucune alternative simple à l'utilisation de l'option `ssh_known_hosts` et ses implications de sécurité.

Cependant, vous pouvez protéger les autres connexions SSH qui se produisent après la phase de clonage dans votre build, par exemple lors du déploiement des résultats du build. Pour que vos constructions rejettent les serveurs SSH usurpés pour ces connexions, vous devez les configurer avec des clés SSH connues pour être fiables. Supposons que votre instance de build se connecte au serveur SSH _ssh.example.com_ :

1. Supprimez l'option `ssh_known_hosts` pour _ssh.example.com_ ; 
2. Obtenez la clé publique du serveur SSH sur _ssh.example.com_ : 
    - Idéalement (mais rarement), le propriétaire de _ssh.example.com_ peut vous fournir la clé SSH publique du serveur par e-mail ou un autre canal de confiance ;
    - Si vous vous êtes déjà connecté à _ssh.example.com_ à partir d'un ordinateur local de confiance, exécutez `ssh-keygen -F ssh.example.com` pour afficher sa clé publique ;
    - Si vous ne vous êtes pas encore connecté à _ssh.example.com_, exécutez `ssh-keyscan ssh.example.com ` pour le récupérer et `ssh-keygen -F ssh.example.com` pour l'afficher.  Idéalement, vous devriez vérifier auprès du propriétaire de _ssh.example.com_ qu'il s'agit bien de la clé publique du serveur et non de la clé d'une instance usurpée de _ssh.example.com_.
3. Configurez Travis CI pour utiliser la clé publique du serveur SSH : ajoutez la clé publique du serveur de _KEY__ au fichier SSH `known_hosts`, par exemple, avec l'ajout suivant à la phase d'installation :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
install:
   - echo 'KEY' >> $HOME/.ssh/known_hosts
```

Assurez-vous de remplacer _KEY_ par la ligne de texte complète contenant la clé publique du serveur SSH tel que obtenu à l'étape précédente.
<hr class="invisible">