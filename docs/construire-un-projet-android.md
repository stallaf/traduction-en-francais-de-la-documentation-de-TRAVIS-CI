# Construire un projet Android

<p>
<div class="tdm">
    <div class="deux-colonnes">
        <ul>
            <li><a href="#environnement-ci-pour-les-projets-android">Environnement CI pour les projets Android</a></li>
            <li><a href="#configuration-niveau-api30+">Exemple de configuration pour le niveau API 30+</a></li>
            <li><a href="#installation-de-composants-android-sdk">Installation des composants Android SDK</a></li>
            <li><a href="#gestion-des-licences">Gestion des licences</a></li>
            <li><a href="#composants-preinstalles">Composants préinstallés</a></li>
            <li><a href="#extras"><code>extras;google;google_play_services</code></a></li>
            <li><a href="#emulateur">Création et démarrage d'un émulateur</a></li>
            <li><a href="#gestion-des-dependances">Gestion des dépendances</a></li>
            <li><a href="#commandes-de-test">Commandes de test par défaut</a></li>
            <li><a href="#mise-en-cache">Mise en cache</a></li>
            <li><a href="#test-avec-plusieurs-jdk">Test avec plusieurs JDK</a></li>
            <li><a href="#matrice-de-construction">Matrice de construction</a></li>
            <li><a href="#construction-sur-differents-environnements">Construction sur différents environnements</a></li>
            <li><a href="#exemples">Exemples</a></li>
            <li><a href="#reference-de-configuration-de-construction">Référence de configuration de construction 
        </ul>
        </ul>
    </div>
</div>
</p>

Ce guide couvre l'environnement de construction et les sujets de configuration spécifiques aux projets Android ciblant l'API 30 et plus. Veuillez consulter nos guides [d'intégration](./embarquement.md) et de [configuration générale de construction](./personnalisation-de-la-construction.md) avant de continuer.

<div class="notice cadre">
<strong>Remarque :</strong> les versions Android ne sont pas disponibles sur les environnements macOS.
</div>

<h2 id="environnement-ci-pour-les-projets-android">Environnement CI pour les projets Android.
<a href="#environnement-ci-pour-les-projets-android" class="ancre-titre after"></a></h2>

Les versions Android sont prises en charge sur nos environnements Bionic, Focal et Jammy. Spécifiez un de ceux-ci dans votre `.travis.yml` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: android
dist: focal  # Options: focal, jammy, or bionic
```

Travis CI fournit une suite complète d'outils pour les projets basés sur JVM, notamment plusieurs JDK, Ant, Gradle, Maven, sbt et Leiningen.

<h2 id="configuration-niveau-api30+">Exemple de configuration pour le niveau de l'API 30+.
<a href="#configuration-niveau-api30+" class="ancre-titre after"></a></h2>

Vous trouverez ci-dessous un exemple `.travis.yml` configuré pour les projets Android ciblant le niveau de l'API 30 et supérieur. Notez que seuls les extras requis (répertoriés plus tard) sont inclus :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: android
dist: focal
android:
  components:
    # Uncomment these if you need the latest SDK tools:
    # - tools
    # - platform-tools
    # Build Tools for API 30
    - build-tools;30.0.0
    # Android SDK Platform for API 30
    - platforms;android-30
    # Android system image for API 30 with Google APIs (x86_64)
    - system-images;android-30;google_apis;x86_64 
    # Required extras
    - extras;android;m2repository         # Android Support Repository (v47.0.0)
    - extras;google;auto                  # Android Auto Desktop Head Unit Emulator (v2.0)
    - extras;google;google_play_services  # Google Play services (v49)
    - extras;google;instantapps           # Google Play Instant Development SDK (v1.9.0)
    - extras;google;m2repository          # Google Repository (v58)
    - extras;google;market_apk_expansion  # Google Play APK Expansion library (v1)
    - extras;google;market_licensing      # Google Play Licensing Library (v1)
    - extras;google;simulators            # Android Auto API Simulators (v1)
    - extras;google;webdriver             # Google WebDriver
```

<h2 id="installation-de-composants-android-sdk">Installation des composants Android SDK .
<a href="#installation-de-composants-android-sdk" class="ancre-titre after"></a></h2>

Dans votre `.travis.yml`, spécifiez les composants SDK exacts à installer :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: android
dist: focal
android:
  components:
    - build-tools;30.0.0
    - platforms;android-30
    - system-images;android-30;google_apis;x86_64 
    - extras;android;m2repository
    - extras;google;auto
    - extras;google;google_play_services
    - extras;google;instantapps
    - extras;google;m2repository
    - extras;google;market_apk_expansion
    - extras;google;market_licensing
    - extras;google;simulators
    - extras;google;webdriver
```

<div class="notice cadre">
Astuce : exécutez <code>sdkmanager --list</code> sur votre machine locale pour afficher tous les composants disponibles et leurs noms exacts.
</div>

<h2 id="gestion-des-licences">Gestion des licences.
<a href="#gestion-des-licences" class="ancre-titre after"></a></h2>

Travis CI accepte les licences demandées par défaut. Pour des licences de liste blanche explicitement, ajoutez la clé `licences` :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: android
dist: focal
android:
  components:
    - build-tools;30.0.0
    - platforms;android-30
    - extras;android;m2repository
    - extras;google;auto
    - extras;google;google_play_services
    - extras;google;instantapps
    - extras;google;m2repository
    - extras;google;market_apk_expansion
    - extras;google;market_licensing
    - extras;google;simulators
    - extras;google;webdriver
  licenses:
    - 'android-sdk-preview-license-52d11cd2'
    - 'android-sdk-license-.+'
    - 'google-gdk-license-.+'
```

Les licences peuvent également être référencées en utilisant des expressions régulières.

<h2 id="composants-preinstalles">Composants préinstallés.
<a href="#composants-preinstalles" class="ancre-titre after"></a></h2>

Les composants suivants sont préinstallés dans les environnements de construction Android Travis CI. Cependant, pour les versions stables, énumérez explicitement tous les composants requis : 

* tools
* platform-tools
* build-tools;30.0.0
* platforms;android-30
* extras;android;m2repository
* extras;google;m2repository

<p id="extras">
<a href="#extras" class="ancre-titre after"></a>
</p>

* extras;google;google_play_services

<h2 id="emulateur">Création et démarrage d'un émulateur.
<a href="#emulateur" class="ancre-titre after"></a></h2>

Si vos tests nécessitent un émulateur, utilisez `avdmanager` pour créer un AVD et démarrer l'émulateur:

```yaml
before_script:
  # Create an AVD named "test" using a system image for API 30
  - echo no | avdmanager create avd -n test -k "system-images;android-30;google_apis;x86_64" --force
  # Start the emulator without audio or window
  - emulator -avd test -no-audio -no-window &
  # Unlock the emulator screen
  - adb shell input keyevent 82 &
```

Ajustez les durées de sommeil si nécessaire.

<h2 id="gestion-des-dependances">Gestion des dépendances.
<a href="#gestion-des-dependances" class="ancre-titre after"></a></h2>

Travis CI suppose que votre projet utilise un outil de construction JVM (par exemple, Gradle, Maven) qui gère automatiquement les dépendances.
Pour les projets utilisant ANT ou d'autres outils, spécifiez votre commande de dépendance :

<div class="titre-code">
    <div class="item gauche">&nbsp;.travis.yml</div><div class="item droit">YAML</div>
</div>
```yml
language: android
dist: focal
install: ant deps
```

<h2 id="commandes-de-test">Commandes de test par défaut.
<a href="#commandes-de-test" class="ancre-titre after"></a></h2>

<h3 id="projet-maven">Projets maven.
<a href="#projet-maven" class="ancre-titre after"></a></h3>

Si votre référentiel contient un `pom.xml` (et pas de `build.gradle`), Maven 3 est utilisé avec :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
mvn install -B
```

<h3 id="projet-gradle">Projets Gradle.
<a href="#projet-gradle" class="ancre-titre after"></a></h3>

Pour les référentiels avec un fichier `build.gradle`, Travis s'exécute :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
gradle build connectedCheck
```

Si une enveloppe Gradle (`gradlew`) existe, il utilise :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
./gradlew build connectedCheck
```

<h3 id="projet-ant">Projets Ant.
<a href="#projet-ant" class="ancre-titre after"></a></h3>

Si aucun fichier Maven ou Gradle n'est trouvé, Travis utilise par défaut :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">Bash</div>
</div>
```bash
ant debug install test
```

Remplacez ces valeurs par défaut en utilisant notre guide de configuration de build général si nécessaire.

<h2 id="mise-en-cache">Mise en cache.
<a href="#mise-en-cache" class="ancre-titre after"></a></h2>

Pour optimiser les constructions et éviter de télécharger le cache après chaque build, ajoutez :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">YAML</div>
</div>
```yml
before_cache:
  - rm -f $HOME/.gradle/caches/modules-2/modules-2.lock
  - rm -fr $HOME/.gradle/caches/*/plugin-resolution/
cache:
  directories:
    - $HOME/.gradle/caches/
    - $HOME/.gradle/wrapper/
    - $HOME/.android/build-cache
```

<h2 id="test-avec-plusieurs-jdk">Test avec plusieurs JDK.
<a href="#test-avec-plusieurs-jdk" class="ancre-titre after"></a></h2>

Vous pouvez effectuer des tests sur plusieurs JDK, comme décrit dans notre guide [Testing Against Multiple JDKs](./construire-un-projet-en-java.md#a-faire) (Tests sur plusieurs JDK).

<h2 id="matrice-de-construction">Matrice de construction.
<a href="#matrice-de-construction" class="ancre-titre after"></a></h2>

Pour les projets Android, vous pouvez créer une matrice de construction en fournissant des tableaux pour `env` et `jdk`.

<h2 id="construction-sur-differents-environnements">Construction sur différents environnements.
<a href="#construction-sur-differents-environnements" class="ancre-titre after"></a></h2>

Les projets Android sont pris en charge sur `dist: bionic`, `dist: focal` et `dist: jammy`. Pour construire sur un environnement différent, installez les paquetages et outils nécessaires. Par exemple :

<div class="titre-code">
    <div class="item gauche">&nbsp;</div><div class="item droit">YAML</div>
</div>
```yml
os: linux
language: java
jdk: openjdk17
env:
  global:
    - ANDROID_HOME=$HOME/travis-tools/android
    - ANDROID_SDK_ROOT=$HOME/travis-tools/android
before_install:
  # Set up the Android SDK command line tools
  - mkdir -p $ANDROID_HOME && mkdir $HOME/.android && touch $HOME/.android/repositories.cfg
  - cd $ANDROID_HOME && wget -q "https://dl.google.com/android/repository/commandlinetools-linux-10406996_latest.zip" -O commandlinetools.zip
  - unzip -q commandlinetools.zip && mkdir -p cmdline-tools && mv cmdline-tools/* cmdline-tools/tools
  - cd $TRAVIS_BUILD_DIR
  # Update PATH for command line tools, emulator, and platform-tools
  - export PATH=$ANDROID_HOME/cmdline-tools/tools/bin/:$PATH
  - export PATH=$ANDROID_HOME/emulator/:$PATH
  - export PATH=$ANDROID_HOME/platform-tools/:$PATH
install:
  # Install required SDK components
  - sdkmanager --sdk_root=$ANDROID_HOME --list | awk '/Installed/{flag=1; next} /Available/{flag=0} flag'
  - yes | sdkmanager --sdk_root=$ANDROID_HOME --install "platform-tools" "platforms;android-30" "build-tools;30.0.0" "emulator" "system-images;android-30;google_apis;x86_64"
  - sdkmanager --list --sdk_root=$ANDROID_HOME | awk '/Installed/{flag=1; next} /Available/{flag=0} flag'
  # Create an AVD for testing
  - echo "no" | avdmanager create avd --verbose --force --name "my_android_30" --package "system-images;android-30;google_apis;x86_64" --tag "google_apis" --abi "x86_64"
  - sudo chmod -R 777 /dev/kvm
  # Start the emulator in the background
  - adb kill-server && adb start-server &
  - sleep 15
  - emulator @my_android_30 -no-audio -no-window &
  - sleep 60
script:
  - sdkmanager --list --sdk_root=$ANDROID_HOME | awk '/Installed/{flag=1; next} /Available/{flag=0} flag'
  - adb devices
  # ... additional build/test commands
```

<h2 id="exemples">Exemples.
<a href="#exemples" class="ancre-titre after"></a></h2>

Voici quelques exemples de projets qui utilisent Travis CI avec Android : 

* [roboguice/roboguice](https://github.com/roboguice/roboguice/blob/master/.travis.yml) ;
* [ruboto/ruboto](https://github.com/ruboto/ruboto/blob/master/.travis.yml) ;
* [RxJava Android Example Project](https://github.com/andrewhr/rxjava-android-example/blob/master/.travis.yml) ;
* [Gradle Example Project](https://github.com/pestrada/android-tdd-playground/blob/master/.travis.yml) ;
* [Maven Example Project](https://github.com/embarkmobile/android-maven-example/blob/master/.travis.yml) ;
* [Ionic Cordova Example Project](https://github.com/samlsso/Calc/blob/master/.travis.yml).

<h2 id="reference-de-configuration-de-construction">Référence de configuration de construction.
<a href="#reference-de-configuration-de-construction" class="ancre-titre after"></a></h2>

Pour plus de détails, consultez la [référence de configuration Travis CI Build pour Android](https://config.travis-ci.com/ref/language/android).
<hr class="invisible">