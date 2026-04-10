WorkManager Codelab
===================================

Compte rendu – Application Blur-O-Matic avec WorkManager
1. Introduction
Dans cet atelier de programmation Android, nous avons utilisé l’application Blur-O-Matic afin d’apprendre à utiliser WorkManager, un composant de l’architecture Jetpack permettant d’exécuter des tâches en arrière-plan.
L’objectif principal de l’application est de permettre à l’utilisateur de sélectionner un niveau de flou pour une image, d’appliquer ce flou et d’enregistrer l’image finale sur l’appareil.
Les opérations telles que le floutage de l’image, le nettoyage des fichiers temporaires et l’enregistrement de l’image sont exécutées en arrière-plan grâce à WorkManager.
________________________________________
2. Présentation de l’application
L’application Blur-O-Matic propose une interface simple permettant :
•	de choisir le niveau de flou 
•	de lancer le traitement avec le bouton Start 
•	d’enregistrer l’image floutée 
L’écran principal contient :
•	une image 
•	des boutons radio pour sélectionner le niveau de flou 
•	un bouton Start 
Lorsque l’utilisateur appuie sur Start, l’application démarre un processus en arrière-plan qui :
1.	nettoie les fichiers temporaires 
2.	applique le flou à l’image 
3.	enregistre l’image finale 
________________________________________
3. Exploration de la structure du projet
Le projet contient plusieurs classes importantes :
BlurViewModel
Cette classe gère l’état de l’application et communique avec le repository pour démarrer les tâches.
WorkManagerBluromaticRepository
Cette classe est responsable de la gestion des tâches WorkManager et du lancement des workers.
BlurWorker
Ce worker applique le flou à l’image.
CleanupWorker
Ce worker supprime les fichiers temporaires créés lors du traitement.
SaveImageToFileWorker
Ce worker enregistre l’image floutée dans un fichier permanent.
BluromaticScreen
Cette classe contient l’interface utilisateur développée avec Jetpack Compose.
________________________________________
4. WorkManager
WorkManager est un composant Android Jetpack qui permet d’exécuter des tâches en arrière-plan même si l’application est fermée.
Ses principaux avantages sont :
•	exécution garantie des tâches 
•	gestion automatique des threads 
•	possibilité d’enchaîner plusieurs tâches 
•	surveillance de l’état des tâches 
Dans cette application, WorkManager est utilisé pour :
•	flouter une image 
•	nettoyer les fichiers temporaires 
•	enregistrer l’image finale 
________________________________________
5. Création du BlurWorker
Le BlurWorker est une classe qui étend CoroutineWorker.
Elle contient la méthode principale :
override suspend fun doWork(): Result
Cette méthode :
1.	récupère l’URI de l’image 
2.	applique la fonction blurBitmap() 
3.	enregistre l’image floutée 
4.	retourne le résultat 
Les opérations sont exécutées dans :
withContext(Dispatchers.IO)
pour effectuer les opérations d’entrée/sortie.
________________________________________
6. Données d’entrée et de sortie
Les données entre les workers sont transmises à l’aide d’un objet Data.
Exemple :
val inputData = workDataOf(
KEY_IMAGE_URI to imageUri,
KEY_BLUR_LEVEL to blurLevel
)
Ces données sont récupérées dans le worker grâce à :
inputData.getString(KEY_IMAGE_URI)
inputData.getInt(KEY_BLUR_LEVEL)
________________________________________
7. Chaîne de WorkManager
Au lieu d’exécuter une seule tâche, nous avons créé une chaîne de workers.
La chaîne est la suivante :
1️⃣ CleanupWorker
→ supprime les fichiers temporaires
2️⃣ BlurWorker
→ applique le flou à l’image
3️⃣ SaveImageToFileWorker
→ enregistre l’image finale dans le stockage
La chaîne est créée avec :
workManager.beginWith(cleanup)
.then(blurWork)
.then(saveWork)
.enqueue()
Ainsi chaque worker s’exécute après le précédent.
________________________________________
8. Résultat
Après l’exécution de l’application :
•	les workers s’exécutent en arrière-plan 
•	des notifications indiquent l’état du traitement 
•	l’image floutée est enregistrée dans le stockage de l’appareil 
L’utilisateur peut ensuite afficher l’image finale.
________________________________________
9. Captures d’écran
Capture 1

 ![WhatsApp Image 2026-04-10 at 22 14 38 (2)](https://github.com/user-attachments/assets/206c44d5-4030-4178-9d72-fb32f612b8ae)

