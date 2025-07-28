# Tâches cron

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#ajouter-des-taches-cron">Ajouter des tâches cron</a></li>
            <li><a href="#passer-les-taches-cron">Passer les tâches cron</a></li>
            <li><a href="#supprimer-les-taches-cron">Supprimer les tâches cron</a></li>
            <li><a href="#detecter-les-builds-declenchees-par-cron">Détecter les builds déclenchées par cron</a></li>
            <li><a href="#notifications">Notifications</a></li>
        </ul>
    </div>
</div>
</p>

Les _jobs cron_ de Travis CI fonctionnent de manière similaire à l'utilitaire `cron` ; ils exécutent des builds à intervalles réguliers indépendamment du fait que des commits aient été poussés vers le référentiel. Les jobs cron récupèrent toujours le commit le plus récent sur une branche particulière et construisent le projet à partir de cet état. Les tâches cron peuvent être exécutées `daily`, `weekly` ou `monthly`, ce qui signifie en pratique jusqu'à une heure après l'intervalle de temps sélectionné, et vous ne pouvez pas les configurer pour qu'elles s'exécutent à une heure spécifique.

Configurez les travaux cron à partir de l'onglet paramètres «Cron Jobs» sur votre page Travis CI.

![Page des paramètres avec la section cron ](https://docs.travis-ci.com/images/cron-section.png)

<div class="notice cadre">
Si vos builds API ou cron s'exécutent sur le même commit ou la même branche à plusieurs reprises, vous remarquerez que GitHub a une limite de <a href="https://developer.github.com/v3/repos/statuses/#create-a-status">1000 mises à jour de statut</a> par commit, ce qui fait que le statut du build Travis CI s'affiche incorrectement sur GitHub. Evitez ce problème en spécifiant une branche régulièrement mise à jour pour vos builds automatisés.
</div>

<h2 id="ajouter-des-taches-cron">Ajouter des tâches cron.
<a href="#ajouter-des-taches-cron" class="ancre-titre after"></a></h2>

Ajouter des tâches Cron en remplissant les champs suivants et en cliquant sur le bouton Ajouter : 

* Branche - Sélectionnez la branche sur laquelle exécuter la build ;
* Intervalle - Sélectionnez à quelle fréquence exécuter la build : `daily`, `weekly`, ou `monthly`.
* Options - Sélectionnez entre l'option pour `Always run` ou `Do not run if there has been a build in the last 24h`.

![Ajouter un travail cron](https://docs.travis-ci.com/images/cron-adding.png)

Confirmez que le travail cron s'affiche dans l'onglet de vos paramètres :

![Cron Job créé](https://docs.travis-ci.com/images/cron-created.png)

<h2 id="passer-les-taches-cron">Passer des tâches cron.
<a href="#passer-les-taches-cron" class="ancre-titre after"></a></h2>

Veuillez noter que les tâches cron seront exécutées de toute façon et ne peuvent pas être ignorées, même avec [ci skip] dans le dernier message de commit.

<h2 id="supprimer-les-taches-cron">Supprimer les tâches cron.
<a href="#supprimer-les-taches-cron" class="ancre-titre after"></a></h2>

Cliquez sur l'icône de la petite poubelle sur le côté droit de la page :

![Supprimer un travail cro](https://docs.travis-ci.com/images/cron-deleting.png)

<h2 id="detecter-les-builds-declenchees-par-cron">Détecter les builds déclenchées par cron.
<a href="#detecter-les-builds-declenchees-par-cron" class="ancre-titre after"></a></h2>

Pour vérifier si une build a été déclenchée par cron, examinez la variable d'environnement `TRAVIS_EVENT_TYPE`pour voir si elle a la valeur `cron`.

<h2 id="notifications">Notifications
<a href="#notifications" class="ancre-titre after"></a></h2>

Les builds de tâches cron utilisent les mêmes [paramètres de notification](./notifications.md) que les constructions push normales.
<hr class="invisible">