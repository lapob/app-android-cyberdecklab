# CyberDeckLab

CyberDeckLab e una app Android in Kotlin/Jetpack Compose pensata come piccolo
workspace mobile in stile cyberdeck: scuro, tecnico, leggibile, utile per
imparare e insegnare concetti di sicurezza, debugging e workflow da laboratorio.

L'obiettivo e costruire strumenti didattici e difensivi, ispirati alla
sensazione "pocket lab" del Flipper Zero, senza trasformare l'app in un kit per
attivita non autorizzate.

## Stato attuale

- App Compose funzionante con dashboard, moduli, modalita, controlli e checklist.
- Package Android: `com.example.cyberdecklab`.
- APK debug generato in `app\build\outputs\apk\debug\app-debug.apk`.
- Verifica locale del 2026-05-19:
  - `testDebugUnitTest` passato.
  - `assembleDebug` passato.
  - installazione debug riuscita su telefono Android collegato via USB.
  - avvio da ADB riuscito senza crash dell'app nei log.

## Preparare il telefono

1. Sul telefono abilita `Opzioni sviluppatore`.
2. Attiva `Debug USB`.
3. Collega il telefono al PC via USB.
4. Quando appare il popup RSA sul telefono, scegli `Consenti`.

Se `adb` non e nel PATH, usa direttamente quello installato da Android Studio:

```powershell
& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" devices -l
```

Devi vedere una riga simile a:

```text
<DEVICE_ID> device product:... model:...
```

Se vedi `unauthorized`, guarda il telefono e accetta la richiesta di debug.

## Avviare build e test

Apri PowerShell nella cartella del progetto:

```powershell
cd <PROJECT_DIR>
```

Compila e lancia i test locali:

```powershell
.\gradlew.bat testDebugUnitTest assembleDebug
```

Se finisce con `BUILD SUCCESSFUL`, l'APK debug e qui:

```text
<PROJECT_DIR>\app\build\outputs\apk\debug\app-debug.apk
```

## Installare sul telefono per debug

Installa direttamente sul telefono collegato:

```powershell
.\gradlew.bat installDebug
```

Se Android risponde con `INSTALL_FAILED_UPDATE_INCOMPATIBLE`, significa che sul
telefono esiste gia la stessa app firmata con una chiave diversa. Per continuare
in sviluppo, rimuovi quella vecchia e reinstalla:

```powershell
& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" uninstall com.example.cyberdecklab
.\gradlew.bat installDebug
```

Nota: `adb uninstall` cancella anche i dati locali di quella app sul telefono.

## Avviare l'app da terminale

```powershell
& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" shell am start -n com.example.cyberdecklab/.MainActivity
```

## Leggere gli errori con logcat

Prima pulisci i log:

```powershell
& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" logcat -c
```

Poi avvia l'app e leggi solo righe utili per crash/eccezioni:

```powershell
& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" logcat -d -t 300 | Select-String -Pattern "com.example.cyberdecklab|AndroidRuntime|FATAL EXCEPTION|ANR|Exception"
```

Se vuoi seguire i log live:

```powershell
& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" logcat | Select-String -Pattern "com.example.cyberdecklab|AndroidRuntime|FATAL EXCEPTION"
```

## Creare l'APK installer

Per test e condivisione rapida tra dispositivi tuoi, usa la debug build:

```powershell
.\gradlew.bat assembleDebug
```

APK:

```text
app\build\outputs\apk\debug\app-debug.apk
```

Per installarlo manualmente via ADB:

```powershell
& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" install -r app\build\outputs\apk\debug\app-debug.apk
```

Per una build release:

```powershell
.\gradlew.bat assembleRelease
```

APK release generato:

```text
app\build\outputs\apk\release\app-release-unsigned.apk
```

La release richiede una configurazione di firma prima di essere usata come APK
finale da distribuire stabilmente. Per ora, la debug build e perfetta per
sviluppo, prove sul telefono e debugging.

## Aggiornare GitHub

In questo ambiente `git` non risulta nel PATH. Prima verifica dal tuo terminale:

```powershell
git --version
```

Se non viene riconosciuto, installa Git for Windows oppure apri il terminale da
Android Studio dopo aver configurato Git. Poi, dalla cartella del progetto:

```powershell
cd <PROJECT_DIR>
git status
```

Se il progetto non e ancora un repository Git:

```powershell
git init
git add .gitignore README.md app\src\main app\build.gradle.kts build.gradle.kts gradle settings.gradle.kts gradlew gradlew.bat gradle.properties
git commit -m "Add CyberDeckLab Android app starter"
git branch -M main
git remote add origin https://github.com/lapob/app-android-cyberdecklab.git
git push -u origin main
```

Se invece il repository GitHub esiste gia:

```powershell
git status
git add .gitignore README.md app\src\main app\build.gradle.kts build.gradle.kts gradle settings.gradle.kts gradlew gradlew.bat gradle.properties
git commit -m "Update CyberDeckLab app guide"
git push
```

Non aggiungere `local.properties`, `.gradle`, `.kotlin`, `.idea/workspace.xml` o
`app/build`: sono file locali o generati.

## Roadmap didattica

Prossimi moduli consigliati:

- `Learn`: schede brevi su radio, NFC, BLE, rete, firmware e sicurezza difensiva.
- `Tools`: checklist, note cifrate locali, decoder/base converter, log viewer.
- `Field`: simulazioni controllate per insegnare concetti senza toccare sistemi reali.
- `Teacher mode`: percorsi guidati per spiegare una lezione dal telefono.
- `Device debug`: pagina interna che mostra versione app, build, device e stato permessi.
