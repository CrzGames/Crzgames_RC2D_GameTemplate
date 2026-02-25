# 🎮 RC2D Example (Créer un nouveau jeu)

RC2D fournit un projet d’exemple **cross-platform**.  
Quand tu crées un nouveau jeu, la majorité des changements à faire concernent :

- ✅ **Android** (package / Activity / libs / nom affiché)
- ✅ **Icônes** (une seule image → génération automatique pour toutes les plateformes)
- ✅ **Métadonnées globales (CMake)** 

<br /><br />

---

<br /><br />

# 🤖 Android

## 1️⃣ Déplacer / renommer le package Java (dossiers)

### Chemin actuel :
```
android-project/app/src/main/java/com/crzgames/testexe/
```

### Exemple nouveau package :
```
android-project/app/src/main/java/com/mycompany/mygame/
```

> ⚠️ Important : les dossiers doivent correspondre exactement au package (même structure).

<br />

## 2️⃣ Mettre à jour le package dans `MyGame.java`

### Fichier :
```
android-project/app/src/main/java/com/crzgames/testexe/MyGame.java
```

### Remplace :
```java
package com.crzgames.testexe;
```

### Par :
```java
package com.mycompany.mygame;
```

<br />

## 3️⃣ Mettre à jour Gradle : `applicationId` + `namespace`

### Fichier :
```
android-project/app/build.gradle
```

### Modifie :
- `applicationId`
- `namespace`

### Exemple :
```gradle
android {
  namespace "com.mycompany.mygame"
  defaultConfig {
    applicationId "com.mycompany.mygame"
  }
}
```

### 🔎 Explication

- namespace → utilisé par Android Gradle Plugin
- applicationId → identifiant final APK / Play Store

Ils doivent correspondre exactement au package Java.

<br />

## 4️⃣ Modifier AndroidManifest.xml

Fichier :

android-project/app/src/main/AndroidManifest.xml

Modifier cette ligne :

<activity android:name="com.crzgames.testexe.MyGame"

Par :

<activity android:name="com.mycompany.mygame.MyGame"

### 🔎 Pourquoi ?

Android doit savoir quelle classe lancer au démarrage.

Si cette valeur ne correspond pas :
- Crash immédiat au lancement
- ClassNotFoundException

<br />

## 4️⃣ Changer le nom affiché du jeu

Android ne permet pas d’utiliser uniquement `SDL_SetWindowTitle` pour le nom système de l’app.

### Fichier :
```
android-project/app/src/main/res/values/strings.xml
```

C’est ici que tu modifies le nom du jeu.

<br />

## 4️⃣ Ajouter une nouvelle dépendance `.so` + la charger (si besoin)

Si tu ajoutes une lib dynamique supplémentaire :

### 1️⃣ Déclarer le chemin dans `build.gradle`

Toujours dans :
```
android-project/app/build.gradle
```

Ajoute les dossiers dans :

```
jniLibs.srcDirs
```

Cela permet d’ajouter des chemins qui contiennent des `.so`.

### 2️⃣ Charger la lib dans `MyGame.java`

Dans `MyGame.java` :

```java
@Override
protected String[] getLibraries() {
  return new String[] {
    "SDL3",
    "SDL3_image",
    "SDL3_mixer",
    "SDL3_ttf",
    "avcodec",
    "avdevice",
    "avfilter",
    "avformat",
    "avutil",
    "swresample",
    "swscale",
    "onnxruntime",
    "main"
  };
}
```

### ✅ Règle importante :

- Ajoute ta nouvelle lib dans ce tableau
- Respecte l’ordre :

1. `SDL3` en premier  
2. `SDL3_*` juste après  
3. Tes libs externes ensuite  
4. `"main"` toujours en dernier  

<br /><br />

---

<br /><br />

# Changer certaines valeurs dans les scripts "build-scripts/build-deploy-mobile/"
- `buid-deploy-mobile/android-unix.sh`: APP_COMPONENT="com.crzgames.testexe/.MyGame"
- `buid-deploy-mobile/android-windows.sh` : APP_COMPONENT="com.crzgames.testexe/.MyGame"
Changer par votre nouveau package Android !

- `buid-deploy-mobile/ios.sh`: APP_NAME="rc2d-game-template" et BUNDLE_ID="com.crzgames.testexe"
Changer par votre nouveau nom de target de CMake et changer rapport à votre nouveau identifier.

<br /><br />

---

<br /><br />

# CI / CD : Changer certaines valeurs dans les variables d'environnement
`buid_deploy_staging.yml` ET `buid_deploy_production.yml`: 
- APP_NAME
- APP_IOSMACOS_IDENTIFIER
- APP_GAME_DESCRIPTION
- APP_IOS_PROVISIONING_PROFILE_NAME
- APP_IOS_PROVISIONING_PROFILE_UUID
- IOS_APPLE_PROVISIONING_PROFILE_APPLEDISTRIBUTION_NAMEAPPLICATION_BASE64: ${{ secrets.IOS_APPLE_PROVISIONING_PROFILE_APPLEDISTRIBUTION_TESTEXE_BASE64 }} (modifier le secret)
- AWS_ACCESS_KEY_ID: ${{ secrets.AETHERROYALE_S3_BUCKET_OVH_ACCESS_KEY_ID }} (modifier le secret)
- AWS_SECRET_ACCESS_KEY: ${{ secrets.AETHERROYALE_S3_BUCKET_OVH_SECRET_ACCESS_KEY }} (modifier le secret)
- AWS_DEFAULT_REGION: ${{ secrets.AETHERROYALE_S3_BUCKET_OVH_REGION }} (modifier le secret)
- OVH_S3_ENDPOINT: ${{ secrets.AETHERROYALE_S3_BUCKET_OVH_ENDPOINT }} (modifier le secret)
- OVH_S3_BUCKET: ${{ secrets.AETHERROYALE_S3_BUCKET_OVH_NAME }} (modifier le secret)


<br /><br />

---

<br /><br />

# 🖼️ Icônes — 1 image → génération automatique toutes plateformes

RC2D centralise les icônes via une image source unique.

## Fichier source (changer l'image par la votre) :
```bash
# Format obligatoire : PNG
# Taille obligatoire : 1024x1024
icons/app-icon-default.png
```

## 🚀 Générer les icônes
### macOS / Linux :
```bash
chmod +x ./build-scripts/generate-icons/generate-icons-unix.sh
./build-scripts/generate-icons/generate-icons-unix.sh
```
### Windows :
```bat
.\build-scripts\generate-icons\generate-icons-windows.bat
```

Une fois exécuté, tout est régénéré automatiquement pour toutes les plateformes supportées  

<br /><br />

---

<br /><br />
 
# 🧾 Métadonnées globales (CMake)

Dans CMakeLists.txt tu as (modifier si besoin) :

- APP_VERSION_NUMERIC
- APP_VERSION_STR
- APP_COMPANY_NAME  
- APP_GAME_DESCRIPTION  
- APP_LEGAL_COPYRIGHT  
- APP_IOSMACOS_BUILD_VERSION  
- APP_IOSMACOS_IDENTIFIER  
- APP_IOSMACOS_DEVELOPMENT_TEAM_ID
- APP_IOS_PROVISIONING_PROFILE_NAME
- APP_IOS_PROVISIONING_PROFILE_UUID
- APP_IOS_CODE_SIGN_IDENTITY
- APP_MACOS_CODE_SIGN_IDENTITY

Exemple :
```bash
if(DEFINED ENV{APP_MACOS_CODE_SIGN_IDENTITY})
  set(APP_MACOS_CODE_SIGN_IDENTITY "$ENV{APP_MACOS_CODE_SIGN_IDENTITY}" CACHE STRING "Identité de code signing Apple macOS")
else()
  set(APP_MACOS_CODE_SIGN_IDENTITY "Developer ID Application: Corentin Recanzone (U3D28WJ8DV)" CACHE STRING "Identité de code signing Apple macOS")
endif()
```

<br /><br />

---

<br /><br />

# Nom de la Target CMake
De base le nom c'est "rc2d-game-template", ça sera le nom de l'executable pour toute les plateformes, mais on peux le changer. <br />

Aller dans CMakelists.txt : <br />
```bash
# Changer ici
project(rc2d-game-template
  LANGUAGES C CXX
)
```