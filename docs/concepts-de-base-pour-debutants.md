# Concepts de base pour les débutants

<p>
<div class="deux-colonnes">
    <div class="tdm">
        <ul>
            <li><a href="#integration-continue">Qu'est-ce que l'intégration continue ?</a></li>
            <li><a href="#construire-tester-et-deployer">Constructions et automatisation IC : Construire, tester et déployer</a></li>
            <li><a href="#construction-etapes-taches-et-phases">Constructions, étapes, tâches et phases</a></li>
            <li><a href="#constructions-cassees">Constructions Cassées</a></li>
            <li><a href="#notes-sur-infrastructure-et-environnement">Notes sur l'infrastructure et l'environnement</a></li>
        </ul>
    </div>
</div>
</p>

Bienvenue à Travis CI ! Cette page fournit certains contextes et terminologies utilisés sur la plate-forme et la documentation, ce qui pourrait être utile si vous êtes nouveau ici ou nouveau dans l'intégration continue (IC).

<h2 id="integration-continue">Qu'est-ce que l'intégration continue ?
<a href="#integration-continue" class="ancre-titre after"></a></h2>

L'intégration continue est la pratique de fusionner fréquemment des changements de code - plutôt que de fusionner dans un grand changement à la fin d'un cycle de développement. L'objectif est de créer des logiciels plus sains en développant et en testant par incréments plus petits. C'est là que Travis CI entre en jeu.

En tant que plate-forme d'intégration continue, Travis CI prend en charge votre processus de développement en construisant et en testant automatiquement les modifications de code, fournissant une rétroaction immédiate sur le succès du changement. Travis CI peut également automatiser d'autres parties de votre processus de développement en gérant les déploiements et les notifications.

<h2 id="construire-tester-et-deployer">Constructions et automatisation IC : construire, tester et déployer
<a href="#construire-tester-et-deployer" class="ancre-titre after"></a></h2>

Lorsque vous exécutez une construction, Travis CI clone votre référentiel GitHub dans un tout nouveau environnement virtuel et effectue une série de tâches pour créer et tester votre code. Si une ou plusieurs de ces tâches échouent, la construction est considérée comme [cassée](#constructions-cassees). Si aucune des tâches échoue, la build est considérée comme [adoptée](#constructions-cassees) et Travis CI peut déployer votre code sur un serveur Web ou un hôte d'application.

La construction d'IC peut également automatiser d'autres parties de votre flux de travail de livraison. Cela signifie que vous pouvez avoir des travaux dépendants les uns des autres avec [des étapes de construction](./etapes-de-construction.md), configurer des [notifications](./notifications.md), préparer des [déploiements](./deploiements.md) après les constructions et de nombreuses autres tâches.

<h2 id="construction-etapes-taches-et-phases">Constructions, étapes, tâches et phases
<a href="#construction-etapes-taches-et-phases" class="ancre-titre after"></a></h2>

Dans la documentation de Travis CI, certains mots courants ont des significations spécifiques : 

* _build_ - Un groupe de _jobs_ qui s'exécutent en séquence. Par exemple, une build peut avoir deux _jobs_, chacun teste un projet avec une version différente d'un langage de programmation. Une _build_ se termine lorsque tous ses travaux sont terminés. 
* _stage_ - Un groupe de _jobs_ qui s'exécutent en parallèle dans le cadre d'un processus de _build_ séquentiel composé de plusieurs [_stages_](./etapes-de-construction.md). 
* _job_ - Un processus automatisé qui clone votre référentiel dans un environnement virtuel, puis réalise une série de _phases_ telles que la compilation de votre code, l'exécution de tests, etc. Un travail échoue si le code de retour de la _phase_ de `script` est non nul. 
* _phase_ - les [étapes séquentielles](./cycle-de-vie-de-job.md) d'un _job_. Par exemple, la phase `install` précède la phase `script`, qui précède la phase `deploy` facultative.

<h2 id="constructions-cassees">Constructions cassées
<a href="#constructions-cassees" class="ancre-titre after"></a></h2>

La build est considérée comme _cassée_ lorsqu'un ou plusieurs de ses jobs se terminent avec un état qui n'est pas _passé_ :

* _errored_ - Une commande dans la phase `before_install`, `install`, ou `before_script` a renvoyé un code de sortie non nul. Le job s'arrête immédiatement.
* _failed_ - Une commande de la phase `script` a renvoyé un code de sortie non nul Le job continue de s'exécuter jusqu'à ce qu'il soit terminé.
* _canceled_ - Un utilisateur annule le job avant qu'il ne soit terminé.

Notre page [problèmes courants de build](./problemes-courants-de-construction.md) est un bon point de départ pour trouver la raison pour laquelle votre construction est défectueuse.

<h2 id="notes-sur-infrastructure-et-environnement">Notes sur l'infrastructure et l'environnement
<a href="#notes-sur-infrastructure-et-environnement" class="ancre-titre after"></a></h2>

Travis CI propose quelques environnements d'infrastructure différents, vous pouvez donc sélectionner la configuration qui convient le mieux à votre projet : 

* _Ubuntu Linux_ - Ces environnements Ubuntu Linux s'exécutent à l'intérieur des machines virtuelles complètes, fournissent de nombreuses ressources de calcul et prennent en charge l'utilisation de `sudo`, `setuid` et `setgid`. Découvrez plus d'informations sur [l'environnement Ubuntu Linux Build](./environnement-linux.md). 
* _Windows_ - Actuellement, la version 1803 de Windows Server est prise en charge. Si vous voulez en savoir plus, consultez [l'environnement Windows Build](./environnement-windows.md).

Plus de détails sur nos environnements de construction sont disponibles dans notre documentation sur [l'Environnement CI](./environnement-ci.md).

Maintenant que vous avez lu les bases, rendez-vous sur notre [Guide d'Intégration](./embarquement.md) pour plus de détails sur la configuration de votre première version, ou lisez quelques [Tutoriels](./tutoriels.md) pour commencer.
<hr class="invisible">
