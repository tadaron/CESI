# Procédure création fichier de réponse
Sous-titre qui explique plus la procédure

## Portée 
Cette procédure s’adresse à tout technicien voulant apprendre à créer un fichier de réponse.

## Historique des versions
| Version | Date de révision | Descriptif du changement | Auteur   |
|---------|------------------|--------------------------|----------|
| V1      | 09/02/2024       | Création du A) et début du B) | Rémi Cst |
| V1.1    | 17/02/2024       | Complétion du A), B), C) et D) | Rémi Cst |

## Table des matières
- [A) Prise en main de Windows System Manager](#prise-en-main-de-windows-system-manager)
  - [Note :](#note-prise-en-main-de-windows-system-manager)
  - [Prérequis :](#prérequis-prise-en-main-de-windows-system-manager)
  - [Procédure :](#procédure-prise-en-main-de-windows-system-manager)
- [B) Création fichier de réponse #1](#création-fichier-de-réponse-1)
  - [Note :](#note-création-fichier-de-réponse-1)
  - [Prérequis :](#prérequis-création-fichier-de-réponse-1)
  - [Procédure :](#procédure-création-fichier-de-réponse-1)
- [C) Création fichier de réponse #2](#création-fichier-de-réponse-2)
  - [Note :](#note-création-fichier-de-réponse-2)
  - [Prérequis :](#prérequis-création-fichier-de-réponse-2)
  - [Procédure :](#procédure-création-fichier-de-réponse-2)
- [D) Mise en fonction des fichiers de réponse](#mise-en-fonction-des-fichiers-de-réponse)
  - [Note :](#note-mise-en-fonction-des-fichiers-de-réponse)
  - [Procédure :](#procédure-mise-en-fonction-des-fichiers-de-réponse)

## A) Prise en main de Windows System Manager 
### Note : 
Nous allons commencer par vérifier que notre outil soit présent sur notre serveur.

### Prérequis : 
- Avoir suivi et exécuté la procédure nommée « Procédure MDT » ou simplement avoir installé l’ADK de Microsoft.

### Procédure : 
1. Si vous n’avez pas déjà installé l’ADK, veuillez suivre le A) de la procédure « Procédure MDT ».
2. Cela fait, ouvrez l’« Assistant Gestion d’installation ». 

## B) Création fichier de réponse #1 
### Note : 
Nous allons voir comment créer le premier fichier de réponse pour automatiser les configurations de l’assistant d’installation de Windows.

### Prérequis : 
- Avoir une image Windows 10/11 ou une image que vous avez déjà personnalisée.

### Procédure : 
1. Nous allons commencer par importer l’image pour laquelle vous voulez créer un fichier de réponse. Pour cela, cliquez droit sur « Sélectionner une image Windows ou un fichier de catalogue » puis sur « Sélectionner l’image Windows ». 
2. Un message d’erreur va alors apparaître, cliquez sur « Oui » afin que le fichier catalogue de votre image soit créé. C’est grâce à ce catalogue que vous serez en mesure de personnaliser les réponses automatiques de votre image.
3. La génération du fichier catalogue commence.
4. Une fois cela terminé, des dossiers vont apparaître dans la partie « Image Windows ». Cette arborescence contient tous les paramètres que vous aurez la capacité de personnaliser. 
5. Avant d’aller voir dans ces dossiers, nous allons créer le fichier de réponse afin d’y mettre les paramètres que nous souhaitons. Cliquez droit sur « Créer ou ouvrir un fichier de réponse » dans la partie « Fichier de réponse » puis sur « Nouveau fichier de réponses ».
6. Vous avez maintenant devant vous les composants de votre fichier que nous allons remplir.
7. Nous pouvons maintenant aller dans le dossier « Components » de notre image importée afin d’importer les configurations dans notre fichier de réponse. 
8. Descendez jusqu’à trouver « amd64_Microsoft-Windows-International-Core-WinPE_10.0.22621.1_neutral », dépliez-le, cliquez droit sur « SetupUILanguage » et ajoutez le paramètre à la passe 1 windowsPE.
9. Faites la même chose pour : 
   - « Disk » dans « amd64_Microsoft-Windows-Setup_10.0.22621.1_neutral » puis « DiskConfiguration ».
   - « Login » et « ImageSelection » dans « amd64_Microsoft-Windows-Setup_10.0.22621.1_neutral » puis « WindowsDeploymentServices ».
10. Ceci fait, vous devriez avoir devant vous ça : 
11. Si vous avez tout ajouté, vous allez maintenant cliquer sur « amd64_Microsoft-Windows-Setup_10.0.22621.1_neutral » et remplir les champs qui s’affichent à droite avec « fr-FR ». Ne complétez pas le champ « LayeredDriver ». 
12. Après cela, allez sur « SetupUILanguage » et remplissez le champ « UILanguage » encore avec « fr-FR ». 

   Comme vous l’aurez peut-être compris, remplir ces champs avec « fr-FR » va nous permettre de répondre à la demande des langues choisies lors de l’installation d’un nouveau Windows.
13. Complétez les champs dans « Credentials » situé dans « amd64_Microsoft-Windows-Setup_neutral », « WindowsDeploymentServices » puis dans « Login ». 
    Vous aurez à compléter le champ « Domain » avec le nom de votre domaine (tout simplement) puis les champs « Password » et « Username » avec les informations de connexion de votre compte Administrateur. 
    Ces informations serviront à connecter votre ordinateur au domaine.
14. Ouvrez ensuite l’onglet « Disk Configuration », puis cliquez droit sur « disk ». Vous allez « Insérer un nouvel élément Disk ».
15. Dans le champ « DiskID », vous allez entrer la valeur 0 pour créer le disque sur le nouvel ordinateur. Après cela, vous allez activer la fonction qui va vous permettre d’effacer les données du disque. Comment ? En cliquant sur le champ « WillWipeDisk » et en sélectionnant « True ». 
16. Cela fait, nous allons modifier le partitionnement du disque. 
    Cliquez droit sur « CreatePartitions » puis sur « Insérer un nouvel élément CreatePartition ». 
    Vous allez créer des partitions avec ces paramètres : 
   - Partition 1 : 
   - Partition 2 : 
   - Partition 3 : 
   - Partition 4 : 
17. Une fois cela fait, nous allons les modifier. 
    Pour cela, nous allons cliquer droit sur « ModifyPartition », puis « Insérer un nouvel élément ... ».
18. Vous allez faire cette manœuvre pour faire les 3 partitions de base d’un système Windows. 
   - Partition Recovery : 
   - Partition System :
   - Partition Windows : 
   
   Vous remarquez qu’à l’étape précédente, nous avons créé 4 partitions et que nous en paramétrons que 3. La 4ème est en fait la partition que l’on va sélectionner pour créer notre système. Cette dite partition a une taille égale à la place qu’il reste sur le disque après avoir créé nos 3 autres partitions.
19. Allons maintenant configurer l’option qui va permettre de choisir automatiquement la partition où installer Windows. 
    Ouvrez « WindowsDeploymentServices », « ImageSelection » puis « InstallTo ». 
    Vous allez mettre dans « DiskID », la valeur 0 pour choisir notre seul disque sur le PC, tagué 0.
    Ensuite, vous allez remplir la valeur « PartitionID » à 4 pour choisir la partition où installer Windows. Comme expliqué précédemment, c’est la 4ème partition que nous allons choisir.
    
Vous savez maintenant comment automatiser les étapes de configuration de l’assistant d’installation de Windows. 

Si vous voulez pousser l’automatisation, continuez la procédure.

## C) Création fichier de réponse #2 
### Note : 
Nous allons voir comment créer le deuxième fichier de réponse. Il va nous permettre de compléter automatiquement les étapes de configuration de Windows au premier démarrage d’un ordinateur (langues, claviers, etc.).

### Prérequis : 
- Avoir une image Windows 10/11 ou une image que vous avez déjà personnalisée.

### Procédure : 
1. Vous allez répéter les premières étapes de création d’un fichier de réponse (importation d’une image et création d’un nouveau fichier de réponse). 
   Retournez à l’étape « B) 1- » si vous ne les avez plus en tête. 
2. Ceci fait, vous allez chercher, dans votre image, les paramètres suivants : 
   Faites bien attention à l’étape dans laquelle vous les importez (4 specialize, 7 oobeSystem, etc.).
3. Quand vous vous serez bien assuré que vous avez la même chose que moi, nous allons commencer par configurer « amd64_Microsoft-Windows-Shell-Setup_neutral » : 
   - Vous allez compléter le champ « Computername » par « * ».
   - Après cela, sélectionnez la valeur « True » dans « CopyProfile ».
   - Complétez ensuite le champ « RegisteredOrganization » par le nom de votre organisation.
   - Pour finir, mettez le nom du possesseur de l’ordinateur dans le champ « RegisteredOwner ».
   
   C’est bon pour ce paramètre. 
4. Descendez ensuite au paramètre en dessous : « amd64_microsoft-Windows-unattendedJoin_neutral » puis sur « Identification ». 
   Vous allez compléter les paramètres comme suit : 
   Vous veillerez à remplacer « cast.local » par le nom de votre domaine. 
   Cette configuration va joindre l’ordinateur, automatiquement, à votre domaine.
5. Dépliez « Identification » pour accéder à « Credentials ». Vous devrez renseigner les informations du compte utilisé pour joindre le domaine. 
   Adaptez le contenu en fonction de ce que vous avez.
6. Passez maintenant à « amd64_Microsoft-International-Core_neutral ». 
   Vous allez entrer la valeur « fr-FR » dans tous les champs pour que le français soit la langue par défaut sur le nouvel ordinateur. 
   Si vous voulez mettre une autre langue, à vous de chercher.
7. Dernier paramètre à configurer, « amd64_Microsoft-Windows-Shell-Setup_neutral ». Celui-ci va nous permettre de faire beaucoup de choses.
   a. Première étape, allez juste sur « amd64_Microsoft-Windows-Shell-Setup_neutral » en cliquant dessus. 
      Vous allez entrer dans le champ « RegisteredOwner » le nom du détenteur de l’ordinateur (vous remarquez que nous l’avons déjà fait). 
      Après ça, entrez la valeur « Romance Standard Time » dans le champ « Timezone » pour préciser au PC que nous sommes situés en France. 
   b. Allez plus profondément dans le paramètre et accédez à « OOBE ». 
      En complétant les champs par « True », vous allez pouvoir sauter certaines étapes de configuration du système, comme la création d’une session locale.
   c. Dernière étape du paramètre. Descendez dans « UserAccounts » puis « LocalAccount », nous allons faire en sorte qu’un compte local Admin soit créé. Insérez un nouvel élément dans le sous-paramètre « LocalAccount ».
   d. Écrivez dans les champs appropriés les informations du compte que vous voulez créer. Si vous ne comprenez pas les champs, basez-vous sur ma capture d’écran.
   e. Vous remarquerez que vous n’avez pas donné le mot de passe du compte. C’est en dépliant le « LocalAccount » que vous avez inséré que vous accéderez à l’option. 

   Si vous faites la manipulation pour de vrais ordinateurs, alors veillez à mettre un mot de passe sécurisé. 

## D) Mise en fonction des fichiers de réponse 
### Note : 
Maintenant que vous avez créé ces fichiers, vous allez voir comment les mettre dans votre WDS pour les mettre en fonction. 

### Procédure : 
1. Ouvrez votre rôle WDS.
2. Cliquez droit sur votre serveur puis sur « Propriétés ».
3. Accédez à l’onglet « Client », puis cliquez sur « Activer l’installation sans assistance ». Ne faites pas attention aux zones floutées, j’ai déjà fait les manipulations.
4. Vous avez maintenant accès aux options où vous allez donner le premier fichier de réponse que nous avons créé. Cliquez donc sur « Parcourir » à la ligne « Architecture x64 », et allez chercher votre fichier. 
   Faites la même chose pour « Architecture x64 (UEFI) ». Je vous conseille de placer vos fichiers dans votre dossier « RemoteInstall » avant de les ajouter. Ce sera plus pratique pour vous.
5. Vous devriez avoir vos deux lignes complétées par votre XML. 
6. Appliquez les modifications et cliquez sur « OK ».
7. Allez maintenant dans vos images d’installation et ouvrez les propriétés de l’image dont vous souhaitez automatiser le choix des options.
8. Une fois ouvertes (les propriétés), cliquez sur « Autoriser l’image à s’installer en mode sans assistance » pour pouvoir lui donner notre deuxième fichier de réponse. Vous l’avez compris, cliquez sur « Sélectionner un fichier » puis allez chercher votre fichier de réponse. 
9. Appliquez les changements et cliquez sur « OK ».

Vous savez maintenant comment créer des fichiers de réponses et les mettre en place dans le WDS !
