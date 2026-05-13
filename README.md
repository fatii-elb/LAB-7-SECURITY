
## Rapport Final - Analyse Sécurité Application DIVA

---

## TABLE DES MATIERES

1. Introduction et Objectifs
2. Environnement de Test
3. Installation et Configuration
4. Analyse Statique
5. Configuration Analyse Dynamique
6. Resultats et Vulnerabilites
7. Recommandations
8. Conclusion

---

## 1. INTRODUCTION ET OBJECTIFS

Lab 7 a pour objectif d'effectuer une analyse dynamique d'une application mobile Android en utilisant MobSF (Mobile Security Framework). L'application testée est DIVA (Damn Insecure and Vulnerable App), une application intentionnellement vulnérable conçue à des fins éducatives.

Objectifs du Lab:
- Installer et configurer MobSF
- Mettre en place un environnement d'analyse dynamique
- Analyser une application mobile pour identifier les vulnérabilités
- Documenter les résultats et recommandations de sécurité

---

## 2. ENVIRONNEMENT DE TEST

Systeme d'exploitation: Windows 10/11
Processeur: Architecture x86_64
RAM: Minimum 8 GB
Docker: Version 29.3.1
WSL2: Hyper-V activé

Outils utilises:
- MobSF (Mobile Security Framework) v4.5.0
- Docker Desktop
- Android Emulator (Pixel 6)
- PowerShell
- ADB (Android Debug Bridge)

---

## 3. INSTALLATION ET CONFIGURATION

### 3.1 Installation de Docker et MobSF

Etapes:
1. Installer Docker Desktop depuis https://www.docker.com/
2. Activer Hyper-V dans Windows
3. Demarrer Docker Desktop

Commande pour lancer MobSF:
```
docker run -it -p 8000:8000 mobsf/mobsf:latest
```

Ou pour utiliser une image alternative:
```
docker run -it -p 8000:8000 ajinabraham/mobsf:latest
```
<img width="1679" height="635" alt="image" src="https://github.com/user-attachments/assets/44d63d7b-0874-4efe-822d-8ade42544e61" />

Acces: http://localhost:8000
<img width="1655" height="988" alt="image" src="https://github.com/user-attachments/assets/a3c412d5-014b-4adb-9053-520fa2b1672c" />


### 3.2 Configuration de l'Emulateur Android

Etapes:
1. Ouvrir Android Studio
2. Acceder a Device Manager
3. Creer ou selectionner un emulateur Pixel 6
4. Demarrer l'emulateur
5. Attendre le demarrage complet (20-30 secondes)

Configuration de l'emulateur:
- Modele: Pixel 6
- OS: Android 11 ou superieur
- Architecture: x86_64
- Mode: Emulation complete

Verification:
```powershell
adb devices
# Resultat attendu: emulator-5554    device
```
<img width="1686" height="343" alt="image" src="https://github.com/user-attachments/assets/a559071e-5067-4782-9619-0dbc62dd1d63" />

### 3.3 Installation de l'APK DIVA

Etape 1: Telecharger DIVA APK

Cloner le depot DIVA:
```powershell
git clone https://github.com/xAltmime/diva-apk-file.git
cd diva-apk-file
```

Ou telecharger depuis la version officielle DIVA sur GitHub.
<img width="1647" height="820" alt="image" src="https://github.com/user-attachments/assets/b872bb46-8ebf-4c75-ac6d-9e68b32a0989" />

Etape 2: Localiser le fichier APK

Le fichier DivaApplication.apk se trouve dans le dossier clone.

Chemin: C:\Users\LENOVO\diva-apk-file\DivaApplication.apk (exemple)

Etape 3: Installer l'APK sur l'emulateur

Copier le fichier vers Downloads:
```powershell
copy DivaApplication.apk C:\Users\LENOVO\Downloads\DIVA.apk
```

Installer via ADB:
```powershell
adb install C:\Users\LENOVO\Downloads\DIVA.apk
# Resultat: Performing Streamed Install
#           Success
```

Verification de l'installation:
```powershell
adb shell pm list packages | findstr diva
# Resultat: package:jakhar.aseem.diva
```

<img width="1647" height="820" alt="image" src="https://github.com/user-attachments/assets/12c7a010-9c50-421a-a1e6-9543ab91656b" />
<img width="1717" height="1243" alt="image" src="https://github.com/user-attachments/assets/bf415891-012d-4f3a-a4c4-02d0e66687a0" />
<img width="792" height="900" alt="image" src="https://github.com/user-attachments/assets/42fd7065-f7ad-42d7-853e-044bed5beca7" />


### 3.4 Configuration du Proxy Reseau

Dans l'emulateur Android:

Etapes:
1. Ouvrir Settings
2. Aller a Wi-Fi
3. Maintenir appui sur le reseau connecte
4. Cliquer sur "Modify" ou "Edit"
5. Cocher "Show advanced options"
6. Trouvez "Proxy" et selectionnez "Manual"
7. Entrer les parametres:
   - Proxy hostname: 10.0.2.2
   - Proxy port: 8888
8. Cliquer "Save"

Justification:
10.0.2.2 est l'adresse speciale pour acceder a l'hote Windows depuis l'emulateur Android.
MobSF ecoute sur le port 8888 pour intercepter le trafic.
<img width="823" height="1461" alt="image" src="https://github.com/user-attachments/assets/3ecaca27-d4ee-4dd3-a4f5-117f2eb3e8ee" />


### 3.5 Activation du Debogage USB

Dans l'emulateur Android:

Etapes:
1. Ouvrir Settings
2. Aller a "About Phone"
3. Taper rapidement sur "Build Number" 7 fois
4. Revenir a Settings
5. Aller a "Developer Options"
6. Chercher "USB Debugging"
7. Activer le curseur


---

## 4. ANALYSE STATIQUE

### 4.1 Upload du fichier APK a MobSF

Etapes:
1. Ouvrir le navigateur: http://localhost:8000
2. Cliquer sur "Upload & Analyze"
3. Selectionner le fichier DIVA.apk
4. Cliquer sur "Upload"

Attendre 2-3 minutes pour l'analyse (le systeme extrait, decompile et analyse l'APK).
<img width="970" height="631" alt="image" src="https://github.com/user-attachments/assets/37cf1cbb-793e-4db3-8c2d-8a768299bdba" />

<img width="1001" height="283" alt="image" src="https://github.com/user-attachments/assets/b8c41db1-87ff-41c9-bbeb-751410cfbf7a" />
<img width="1012" height="847" alt="image" src="https://github.com/user-attachments/assets/11ebeafa-7ea0-4ecc-aaef-fa8ef6d53129" />

### 4.2 Resultats de l'Analyse Statique
<img width="441" height="972" alt="image" src="https://github.com/user-attachments/assets/1a3550d0-3cb7-4c4c-a711-ff12828ddcc9" />

Une fois l'analyse terminee, MobSF affiche un rapport complet.

Informations Cles:

Nom de l'application: Diva
Package: jakhar.aseem.diva
Version: 1.0
Taille: 1.43 MB

Hashes fichier APK:
- MD5: 82ab8b2193b3cfb1c737e3a786be363a
- SHA1: 27e849d9d7b86a3a3357fb3e980433a91d416801
- SHA256: 5cefc51fce9bd760b92ab2340477f4dda84b4ae0c5d04a8c9493e4fe34fab7c5

Score de securite: 36/100 (RISQUE ELEVE)
Grade: C

Composants de l'application:
- Nombre d'Activities: 17
- Nombre de Services: 0
- Nombre de Receivers: 0
- Nombre de Providers: 1
- Activities exportees: 2
- Providers exportes: 1

<img width="982" height="335" alt="image" src="https://github.com/user-attachments/assets/4cf32511-817a-4a60-8f4d-7044f753d5cd" />
### 4.3 Permissions de l'Application

L'application demande 3 permissions:

1. android.permission.INTERNET
   - Severite: Normal
   - Description: Acces Internet complet
   - Risque: Permet les appels reseau

2. android.permission.READ_EXTERNAL_STORAGE
   - Severite: Dangerous
   - Description: Lecture du stockage externe
   - Risque: Acces aux fichiers utilisateur

3. android.permission.WRITE_EXTERNAL_STORAGE
   - Severite: Dangerous
   - Description: Ecriture au stockage externe
   - Risque: Modification/suppression de fichiers

---

## 5. CONFIGURATION ANALYSE DYNAMIQUE

### 5.1 Activation du Mode TCP ADB

Pour une analyse dynamique complète, il faut configurer la connexion ADB:

```powershell
# Activer TCP sur l'emulateur
adb -s emulator-5554 tcpip 5555

# Resultat: restarting in TCP mode port: 5555
```

### 5.2 Configuration dans MobSF

Certaines versions de MobSF necessitent une configuration manuelle:

Etapes:
1. Dans MobSF, aller a Settings (coin superieur droit)
2. Chercher "ANALYZER_IDENTIFIER"
3. Entrer: emulator-5554 (ou votre identificateur emulateur)
4. Sauvegarder

Alternativement, pour les versions qui ne detectent pas l'emulateur:
- Proceder avec l'analyse statique uniquement
- Les resultats statiques suffisent pour documenter les vulnerabilites


### 5.3 Lancement de l'Application DIVA

Dans l'emulateur Android:

Etapes:
1. Chercher et ouvrir l'application DIVA
2. Accepter les permissions si demande
3. Lire la description de bienvenue
4. Visualiser la liste des challenges/vulnerabilites

La page principale affiche 11 categories de vulnerabilites:
1. Insecure Logging
2. Hardcoding Issues (Part 1)
3. Insecure Data Storage (Parts 1-4)
4. Input Validation Issues (Parts 1-2)
5. Access Control Issues (Parts 1-3)


### 5.4 Exploration des Vulnerabilites

Tester manuellement chaque challenge:

Challenge 1: Insecure Logging
- Objective: Identifier ce qui est log et ou
- Action: Cliquer sur "Insecure Logging"
- Resultat: Voir les donnees sensibles affichees
- Proof: Les donnees sont loggees en texte clair

Challenge 2: Hardcoding Issues
- Objective: Trouver les credentials codes en dur
- Action: Inspecter le code ou utiliser Logcat
- Resultat: Trouver "pkey" : "notespin"
- Proof: Secret trouve dans l'APK

Challenge 3-6: Insecure Data Storage
- Objective: Acceder aux donnees stockees sans chiffrement
- Action: Cliquer sur chaque part pour voir le stockage
- Resultat: Donnees lisibles en texte clair
- Proof: Pas de chiffrement applique

Challenge 7-8: Input Validation
- Objective: Exploiter les failles de validation
- Action: Essayer une injection SQL
- Payload exemple: ' OR '1'='1 --
- Resultat: Contournement de la validation

Challenge 9-11: Access Control
- Objective: Acceder a des ressources protegees
- Action: Examiner les permissions et composants
- Resultat: Controle d'acces insuffisant


### 5.5 Monitoring du Trafic Reseau

MobSF proxy monitore le trafic:

Etapes:
1. Pendant que l'application DIVA est utilisee, MobSF enregistre le trafic
2. Revenir a MobSF dans le navigateur
3. Aller a l'onglet "Dynamic Analyzer"
4. Visualiser le trafic capture (si disponible)

Informations capturees:
- Requetes HTTP/HTTPS
- Appels API
- Donnees transmises
- Reponses serveur


---

## 6. RESULTATS ET VULNERABILITES

### 6.1 Resume des Vulnerabilites

Nombre total de vulnerabilites: 13

Repartition par severite:
- CRITICAL (HIGH): 5
- MEDIUM: 7
- INFO: 1

### 6.2 Vulnerabilites CRITICAL

Vulnerabilite 1: Debug Enabled [android:debuggable=true]
Severite: CRITICAL
Fichier: BuildConfig.java
Description: Le degage est active ce qui permet aux reverse engineers d'attacher un debugger
Impact: Acces aux stack traces et helper classes
Recommandation: Desactiver pour les builds de production

Vulnerabilite 2: Application Signed with Debug Certificate
Severite: CRITICAL
Fichier: Certificate Analysis
Description: L'application est signee avec un certificat de debug (C=US, O=Android, CN=Android Debug)
Impact: Pas adequat pour une application de production
Recommandation: Utiliser un certificat de release pour la production

Vulnerabilite 3: Application Vulnerable to Janus Vulnerability
Severite: CRITICAL
Versions Android affectees: 5.0-8.0
Description: L'application utilise uniquement la signature v1, ce qui la rend vulnerable a Janus sur Android 5.0-8.0
Impact: Modification de l'APK apres signature
Recommandation: Signer avec v2 et v3 en addition de v1

Vulnerabilite 4: Min SDK Too Old (API 15)
Severite: CRITICAL
Description: L'application peut s'installer sur Android 4.0.3-4.0.4 qui ont plusieurs vulnerabilites non corrigees
Impact: Utilisateurs sur anciennes versions non securisees
Recommandation: Augmenter minSdk a au moins API 29 (Android 10)

Vulnerabilite 5: Hardcoded Secrets Found
Severite: CRITICAL
Secret trouve: "pkey" : "notespin"
Description: Des credentials sont codes en dur dans l'APK
Impact: Compromission immediate du PIN
Recommandation: Supprimer tous les secrets du code et les stocker securement



### 6.3 Vulnerabilites MEDIUM

Vulnerabilite 6: Application Data Can Be Backed Up
Severite: MEDIUM
Parametrage: [android:allowBackup=true]
Description: N'importe qui peut sauvegarder les donnees de l'application via ADB
Impact: Utilisateurs avec USB Debugging active peuvent copier les donnees
Recommandation: Definir android:allowBackup="false"

Vulnerabilite 7: Activity Not Protected - APICredsActivity
Severite: MEDIUM
Composant: jakhar.aseem.diva.APICredsActivity
Description: L'activity est exportee sans protection
Impact: D'autres applications peuvent y acceder
Recommandation: Ajouter une protection ou supprimer android:exported

Vulnerabilite 8: Activity Not Protected - APICreds2Activity
Severite: MEDIUM
Composant: jakhar.aseem.diva.APICreds2Activity
Description: L'activity est exportee sans protection
Impact: D'autres applications peuvent y acceder
Recommandation: Ajouter une protection ou supprimer android:exported

Vulnerabilite 9: SQL Injection Possible
Severite: MEDIUM
Fichiers affectes: InsecureDataStorage2Activity.java, SQLInjectionActivity.java, NotesProvider.java
Description: Utilisation de requetes SQL brutes sans sanitization
Impact: Extraction ou modification non autorisee de donnees
Exemple de payload: ' OR '1'='1 --
Recommandation: Utiliser des Prepared Statements ou Room

Vulnerabilite 10: Insecure External Storage
Severite: MEDIUM
Fichier: InsecureDataStorage4Activity.java
Description: Donnees stockees en texte clair sur le stockage externe
Impact: Accessible par toute autre application
Recommandation: Utiliser EncryptedSharedPreferences ou chiffrer les donnees

Vulnerabilite 11: Sensitive Data in Temp Files
Severite: MEDIUM
Fichier: InsecureDataStorage3Activity.java
Description: Donnees sensibles ecrites dans des fichiers temporaires
Impact: Accessible par d'autres processus
Recommandation: Utiliser le stockage chiffre

Vulnerabilite 12: Content Provider Not Protected
Severite: MEDIUM
Composant: jakhar.aseem.diva.NotesProvider
Parametrage: [android:exported=true]
Description: Le Content Provider est accessible par d'autres applications
Impact: Lecture/modification non autorisee des notes
Recommandation: Ajouter android:permission ou definir android:exported="false"

### 6.4 Vulnerabilites INFO

Vulnerabilite 13: Logging of Sensitive Information
Severite: INFO
Fichiers affectes: 7 fichiers Java (AccessControl1/2Activity, InsecureDataStorage2/3/4Activity, LogActivity, SQLInjectionActivity)
Description: L'application log des informations sensibles
Impact: Donnees sensibles visibles dans Logcat
Recommandation: Ne pas logger les donnees sensibles

### 6.5 Certificat et Signature

Information certificat:
Subject: C=US, O=Android, CN=Android Debug
Signature Algorithm: rsassa_pkcs1v15
Valid From: 2015-11-02 08:32:11
Valid To: 2045-10-25 08:32:11
Issuer: C=US, O=Android, CN=Android Debug
Serial Number: 0x218330df
Hash Algorithm: sha256

Hashes certificat:
MD5: d620162ac34ee974d7fd3a1862e7e4df
SHA1: ae4ead5aeaba4e9e4fc928e7c7f7fd459f008031
SHA256: 35d7f7ad35dfb826b70fa4b73187ed478540e32c8b8c5653b86568029fcd5840
SHA512: e936169585893a7a248e393ddb296b2101432de5b07c3d7e2bdc8070dc0b58277b46e443eff3730b4f5a25b61f78c9078f54cc325cb86c17160b5bae13148d1e

Schemas de signature:
- v1 signature: Oui (VULNERABLE a Janus)
- v2 signature: Non
- v3 signature: Non
- v4 signature: Non

Protection des binaires natifs (libdivajni.so):
- NX Bit: Non (RISQUE)
- Stack Canary: Non dans la plupart (RISQUE)
- RELRO: Full (OK)
- Fortify: Non (RISQUE)
- Symbols Stripped: Oui (OK)

---

## 7. RECOMMANDATIONS

### 7.1 Corrections CRITICAL (A Implémenter Immediatement)

1. Desactiver le Debage en Production
   Modifier AndroidManifest.xml:
   android:debuggable="false"

2. Utiliser un Certificat Production
   Generer une clé de release:
   keytool -genkey -v -keystore release.jks -keyalg RSA -keysize 2048 -validity 10000

3. Supprimer les Secrets du Code
   - Extraire les credentials vers un serveur backend
   - Utiliser EncryptedSharedPreferences pour le stockage local
   - Ne pas coder les secrets en dur

4. Augmenter le Min SDK
   Modifier build.gradle:
   minSdkVersion 29

5. Ajouter les Signatures v2 et v3
   Configurer dans build.gradle:
   v2SigningEnabled true
   v3SigningEnabled true

### 7.2 Corrections MEDIUM (A Corriger)

6. Desactiver le Backup
   android:allowBackup="false"

7. Proteger les Activities Exportees
   android:exported="false"
   Ou ajouter une protection par permission

8. Prevenir les Injections SQL
   Utiliser des Prepared Statements:
   String[] selectionArgs = { id };
   db.query("users", null, "id=?", selectionArgs, null, null, null);

9. Chiffrer les Donnees Externes
   Utiliser EncryptedFile ou chiffrement manuel AES-256

10. Implémenter le Stockage Securise
    Ajouter la dependance: androidx.security:security-crypto

11. Supprimer les Donnees Sensibles des Logs
    Ne pas utiliser Log.d() pour les donnees sensibles

12. Proteger le Content Provider
    Ajouter android:permission ou android:exported="false"

### 7.3 Corrections MOYEN (A Planifier)

13. Code Obfuscation
    Activer minification et proguard dans les builds de release

14. Root Detection
    Implémenter la detection du root pour les pays necessitant la securite

15. HTTPS Pinning
    Utiliser OkHttp avec certificate pinning

---

## 8. CONCLUSION

L'analyse de l'application DIVA a identifie 13 vulnerabilites de securite majeures:
- 5 vulnerabilites CRITICAL
- 7 vulnerabilites MEDIUM
- 1 information

Score de securite global: 36/100

DIVA est intentionnellement conçue pour etre vulnerable a des fins educatives. Cette analyse demontre l'importance de:

1. Desactiver le debage en production
2. Utiliser des certificats de production
3. Ne pas coder les secrets en dur
4. Implémenter un stockage securise
5. Valider et sanitizer toutes les entrees utilisateur
6. Proteger les composants de l'application
7. Utiliser le chiffrement pour les donnees sensibles

Les vulnerabilites identifiees representent des risques critiques qui doivent etre corriges avant le deploiement en production.

Lab 7 a ete completé avec succes. Tous les objectifs ont ete atteints:
- Installation et configuration de MobSF
- Configuration de l'environnement d'analyse dynamique
- Analyse statique complete
- Documentation des vulnerabilites
- Recommandations de securite

---

---

Fin du Rapport Lab 7
Date: 13 Mai 2026
Outil: MobSF v4.5.0
Score Final: 36/100 (RISQUE ELEVE)
Statut: COMPLETE
