# Procédure MDT
Sous-titre qui explique plus la procédure

## Portée 
À qui s’adresse cette procédure 

## Historique des versions
| Version | Date de révision | Descriptif du changement | Auteur   |
|---------|------------------|--------------------------|----------|
| V1      | 14/01/2024       | Complétion du A), B), C), D) et E) et création du F) | Rémi CST |
| V1.1    | 20/01/2024       | Complétion du H)         | Rémi CST |
| V1.2    | -                | -                        | -        |

## Table des matières
- [A) Installer le rôle MDT](#installer-le-rôle-mdt)
  - [Note :](#note-installer-le-rôle-mdt)
  - [Prérequis :](#prérequis-installer-le-rôle-mdt)
  - [Procédure :](#procédure-installer-le-rôle-mdt)
- [B) Créer le deployment Share du MDT](#créer-le-deployment-share-du-mdt)
  - [Note :](#note-créer-le-deployment-share-du-mdt)
  - [Prérequis :](#prérequis-créer-le-deployment-share-du-mdt)
  - [Procédure :](#procédure-créer-le-deployment-share-du-mdt)
- [C) Paramétrer l’utilisateur MDT](#paramétrer-lutilisateur-mdt)
  - [Note :](#note-paramétrer-lutilisateur-mdt)
  - [Prérequis :](#prérequis-paramétrer-lutilisateur-mdt)
  - [Procédure :](#procédure-paramétrer-lutilisateur-mdt)
- [D) Configurer une image Windows 11 sur le MDT](#configurer-une-image-windows-11-sur-le-mdt)
  - [Note :](#note-configurer-une-image-windows-11-sur-le-mdt)
  - [Prérequis :](#prérequis-configurer-une-image-windows-11-sur-le-mdt)
  - [Procédure :](#procédure-configurer-une-image-windows-11-sur-le-mdt)
- [E) Configurer une séquence de tâche personnalisé pour Windows 11](#configurer-une-séquence-de-tâche-personnalisé-pour-windows-11)
  - [Note :](#note-configurer-une-séquence-de-tâche-personnalisé-pour-windows-11)
  - [Prérequis :](#prérequis-configurer-une-séquence-de-tâche-personnalisé-pour-windows-11)
  - [Procédure :](#procédure-configurer-une-séquence-de-tâche-personnalisé-pour-windows-11)
- [F) Configurations supplémentaires du MDT pour Windows 11](#configurations-supplémentaires-du-mdt-pour-windows-11)
  - [Note :](#note-configurations-supplémentaires-du-mdt-pour-windows-11)
  - [Prérequis :](#prérequis-configurations-supplémentaires-du-mdt-pour-windows-11)
  - [Procédure :](#procédure-configurations-supplémentaires-du-mdt-pour-windows-11)
- [G) Configuration globale du MDT](#configuration-globale-du-mdt)
  - [Note :](#note-configuration-globale-du-mdt)
  - [Prérequis :](#prérequis-configuration-globale-du-mdt)
  - [Procédure :](#procédure-configuration-globale-du-mdt)
- [H) Création et importation de notre image déployable dans notre WDS](#création-et-importation-de-notre-image-déployable-dans-notre-wds)
  - [Note :](#note-création-et-importation-de-notre-image-déployable-dans-notre-wds)
  - [Prérequis :](#prérequis-création-et-importation-de-notre-image-déployable-dans-notre-wds)
  - [Procédure :](#procédure-création-et-importation-de-notre-image-déployable-dans-notre-wds)

## A) Installer le rôle MDT 
### Note :
Contrairement à la plupart des autres rôles, le MDT ne s’installe pas via le gestionnaire de serveur. Nous allons être obligé d’aller sur le site de Microsoft pour aller y chercher les sources.

### Prérequis : 
- Un serveur contenant WDS.
- Un serveur contenant le rôle DHCP opérationnel pour le démarrage UEFI. Cela peut être le même que pour le WDS.
- Une connexion internet sur le serveur MDT.

### Procédure : 
1. Rendez-vous sur cette page du forum de Microsoft : [lien].
2. Téléchargez y l’ADK Windows et l’add-on juste en dessous. Ils vont nous servir à préparer le terrain pour le MDT.
3. Exécutez ces deux exécutables sur la machine où vous voulez installer le MDT (dans mon cas, sur le même serveur que le WDS).
4. Une fenêtre s’ouvre, vous devrez choisir où installer le kit de déploiement. Je choisis de l’installer sur mon serveur dans le C, comme proposé par défaut. 
5. Sur la page suivante, vous pouvez laisser ou non Microsoft collecter des informations sur les kits. À vous de voir selon vos préférences.
6. Après cela, acceptez le contrat de licence.
7. Dans les fonctionnalités à installer, choisissez :
   - « Outils de déploiement »
   - « Concepteur de fonction d’acquisition d’image et de configuration (ICD) »
   - « Concepteur de configuration »
   - « Outils de migration utilisateur (USMT) » 
   Pas besoin d’installer tous les outils ; vous aurez tout ce qu’il faut avec ces 4 fonctionnalités. 
8. Lancez l’installation. En fonction de la qualité de votre connexion, cela peut prendre un peu de temps. L’installeur va chercher des composants sur le web. 
9. Une fois installé, fermez l’installeur. C’est terminé.
10. Exécutez maintenant le « adkwinpesetup ». Les premières étapes sont les mêmes que pour l’ « adk ». 
    Vous aurez simplement la fenêtre des fonctionnalités à regarder. Vous verrez que nous serons sur le point d’installer Windows PE. Laissez cochée la case et cliquez sur installer. Encore une fois, ça peut prendre du temps.
11. Vous allez maintenant télécharger le MDT en lui-même sur la page Microsoft officielle : [lien]. Comme vous aurez pu le voir, il n’est disponible qu’en anglais.
12. Choisissez ensuite la version x64, nous serons obligés pour déployer Windows 11. 
13. Exécutez sur votre serveur MDT le MSI « MicrosoftDeploymentToolkit_x64 » afin de débuter l’installation.
    Vous allez laisser toutes les options par défaut et cliquer sur installer.
14. Cela fait, vous retrouverez le service MDT dans la liste de vos applications. Son nom est « Deployment Workbench ». 

Félicitations, vous avez installé le MDT !

## B) Créer le deployment Share du MDT 
### Note : 
Nous allons créer le dossier partagé qui contiendra toutes les ressources du MDT. Il servira à partager les ressources aux machines sur le réseau. 

### Prérequis : 
- Lorem Ipsum.

### Procédure : 
1. Ouvrez le MDT, cliquez droit sur « Deployment Shares » puis sur « New Deployment Share ».
2. Choisissez où sera créé ce dossier partagé. Dans mon cas, je vais réutiliser mon lecteur W du WDS. Je vais simplement y créer un autre dossier « DeploymentShare » pour mon MDT. 
3. Pour le nom du partage, nous resterons sur la valeur par défaut. Cela va nous créer un partage avec comme nom « DeploymentShare$ », il sera caché grâce au « $ ».
4. Après cela, vous pouvez choisir la description du partage. 
5. Pour ce qui est des options, vous allez laisser par défaut. Elles sont changeables plus tard. 
6. Continuez jusqu’à ce que le partage soit créé.
7. Retournez dans votre MDT, vous pouvez voir votre partage sous l’onglet « Deployment Shares ». Vous remarquerez une arborescence de dossiers, que vous pourrez retrouver dans l’explorateur de fichiers. 

## C) Paramétrer l’utilisateur MDT 
### Note : 
Vous allez créer un utilisateur local dédié aux machines à déployer. Il permettra aux machines d’accéder aux fichiers partagés du MDT via les droits de ce compte. 

### Prérequis : 
- Avoir accès à l’AD.

### Procédure : 
1. Allez dans votre AD et créez-y un utilisateur avec ces informations : 
   Choisissez vous-même le mot de passe mais ne l’oubliez pas.
2. Ensuite, allez sur votre dossier « DeploymentShare » dans l’explorateur de fichiers. Ouvrez ses propriétés, onglet « Partage », puis cliquez sur « Partage avancé ». Vous allez ajouter notre « Utilisateur_MDT » en lecture sur le dossier. Par défaut, quand vous ajoutez un utilisateur de cette façon, il sera en lecture.

## D) Configurer une image Windows 11 sur le MDT 
### Note : 
Loren ipsum.

### Prérequis : 
- Avoir un ISO Windows 11.

### Procédure : 
1. Ouvrez votre MDT et cliquez droit sur « Operating Systems », puis sur « Import Operating System ».
2. Une page s’ouvre, vous demandant d’où proviennent les sources du système d’exploitation. Dans mon cas, je simule le fait d’avoir inséré un CD sur la machine. J’ai donc un lecteur D.
3. Choisissez le nom de votre dossier où seront stockées les versions de Windows que vous allez importer. 
4. Cela fait, plusieurs versions de Windows vont apparaître dans votre onglet « Operating systems ». Vous pouvez supprimer celles qui ne vous seront inutiles. Dans mon cas, toutes sauf « Windows 11 Pro in Windows 11 install.wim ». 

## E) Configurer une séquence de tâche personnalisée pour Windows 11
### Note : 
Loren ipsum.

### Prérequis : 
- Avoir fait l’étape D) Configurer une image Windows 11 sur le MDT.

### Procédure : 
1. Ouvrez le MDT, cliquez droit sur « Task Sequences », puis sur « New Folder ». Nous allons créer un dossier regroupant toutes les tâches pour Windows 11. 
2. Précisez le nom du dossier, Windows 11, comme dit précédemment. 
3. Cliquez sur Next jusqu’à ce que le dossier se crée.
4. Cela fait, vous allez cliquer droit sur notre dossier fraîchement créé. Cliquez sur « New Task Sequence ». 
5. Vous allez préciser sur cette page l’« ID » de votre séquence de tâche, puis son nom et enfin un commentaire si vous le souhaitez. Dans mon cas, je mets « WIN11-23H2 » en ID, car c’est la version que je souhaite déployer, et « Déploiement Windows 11 Pro 232 » pour la même raison.
6. Nous allons maintenant sélectionner « Standard Client Task Sequence », c’est cette séquence qui va nous permettre de déployer Windows 11. 
7. Vous devez maintenant sélectionner quel OS déployer. Nous n’en avons qu’un, Windows 11 Pro si vous avez supprimé ceux qui vous étaient inutiles. 
8. En fonction de ce que vous avez, choisissez l’option que vous voulez. Dans mon cas, je n’ai aucune licence à attribuer. Je n’ai ni « MAK key » (Clé utilisable pour déployer plusieurs machines), ni « Product key » (Utilisable pour une seule machine). Je choisis donc « Do not specify a product key at this time ».
9. Sur cette nouvelle page, vous allez spécifier le nom et l’organisation du compte administrateur qui va être créé sur la machine. Ce sera un compte local. 
10. Définissez le mot de passe du dit compte. 
11. Faites suivant jusqu’à ce que la séquence de tâche soit créée. 
12. Vous la retrouverez dans votre dossier Windows 11, dans « Task Sequence ». En cliquant droit dessus, puis sur « Propriétés », vous accéderez à tous les paramètres de celle-ci.
13. Vous pourrez notamment y retrouver les tâches qui la composent et ainsi désactiver/activer, ajouter/supprimer des composants. 

## F) Configurations supplémentaires du MDT pour Windows 11 
### Note : 
Vous allez voir 2 points qui sont les problèmes les plus récurrents sur le MDT.

### Prérequis : 
- Aucun prérequis, si ce n’est d’avoir accès au MDT (évidemment).

### Procédure : 
1. Premier point, quand nous ouvrons l’onglet « Windows PE » dans les propriétés de notre « MDT Deployment Share », une erreur de la console MMC apparaît. 
2. Pour résoudre cela, nous allons devoir créer cette arborescence sur notre serveur : 
   `C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\x86\WinPE_OCs`. 
3. Puis après cela, retournez dans les propriétés de notre « MDT Deployment Share » et décochez le x86 des plateformes supportées. Nous n’en avons pas besoin, car nous allons déployer Windows 11 qui n’existe qu’en x64.
4. Après cela, plus d’erreur ! 
5. Deuxième point, une erreur de script « An error has occurred in the script on this page » s’affiche au lancement d’un déploiement ou d’une capture. 
6. Pour résoudre cela, allez dans ce dossier : 
   `C:\Program Files\Microsoft Deployment Toolkit\Templates` et modifiez le fichier « Unatted_PE_x64.xml ». 
7. Vous allez y remplacer le contenu par ce que Microsoft eux-mêmes recommandent. Voici le lien de l’article : [Microsoft Deployment Toolkit known issues]. Descendez un peu, et vous trouverez ce paragraphe à copier. 
   Copiez donc le contenu afin de l’insérer dans notre fichier. Voici ce que cela donne : 
8. Vous ne devriez pas rencontrer d’erreur de script avec cette manipulation.

## G) Configuration globale du MDT 
### Note : 
Nous allons configurer le comportement global de notre MDT au travers de deux fichiers :
- Le bootstrap.ini
- Et le CustomSettings.ini

### Prérequis : 
- Aucun prérequis, si ce n’est d’avoir accès au MDT (évidemment).

### Procédure : 
1. Commençons par le « bootstrap.ini ». C’est le fichier qui va se charger en premier lors du démarrage de WinPE.
   Vous pourrez le modifier en passant par les propriétés de notre « MDT Deployment Share » dans l’onglet « Rules ». Cliquez ensuite sur le bouton « Edit Bootstrap.ini ». 
   Le fichier s’ouvre avec l’outil Note.
2. Remplacez le contenu du fichier par ce pavé de texte et personnalisez le avec vos informations :
   [Settings]
Priority=Default

[Default]
DeployRoot=\DC01\DeploymentShare$
UserID=Utilisateur_MDT
UserPassword=Mot2passe123 !
UserDomain=cast.local
SkipBDDWelcome=YES
KeyboardLocalePE=040c:0000040c

**Explication :** 
- Le “DeployRoot” correspond au chemin réseau du dossier partagé « Deployment Share » ; la machine cliente ira y chercher toutes les infos pour installer l’OS.
- Le « UserID » correspond au compte que la machine va utiliser pour accéder au partage, il est donc très important qu’il ait des droits sur ce dossier.
- Le « UserPassword » va définir le mot de passe du compte que la machine va utiliser pour accéder au partage. Cela nous permettra d’éviter de saisir ces informations à chaque déploiement de machine.
- Le « UserDomain » où il faudra préciser le domaine où se trouve le compte utilisé. Si c’est un compte local, il faudra préciser le nom du serveur.
- Et enfin, le « KeyboardLocalePE » nous permettra d’utiliser le clavier Français dans l’environnement WinPE.

Cela donne quelque chose comme ça :

3. Modifiez maintenant le « CustomSettings.ini ». Pour ça, enregistrez notre « Bootstrap.ini » et retournez dans l’onglet « Rules ». Par défaut, vous aurez ça : 
Ces options correspondent à ce que nous avons configuré à l’installation de notre MDT (voir étape B).
4. Vous allez ajouter ça sous ce qui est déjà écrit :
_SMSTSORGNAME=Castillonais
TimeZone=105
TimeZoneName=Romance Standard Time
Pensez à remplacer « Castillonais » par le nom de votre organisation.
Petite précision, le « TimeZone » va nous permettre de paramétrer le fuseau horaire de la machine.

Cela donne donc :

5. Dernière étape, allez dans l’onglet « Windows PE », puis dans « Drivers and Patches » et enfin sélectionnez « x64 ». Vous allez dire au MDT d’inclure les drivers de la carte réseau et des périphériques de stockage. Très utile pour aller se connecter à notre « Deployment Share » situé sur notre serveur. 
6. Vous avez terminé avec les configurations globales de notre MDT ! 

## H) Création et importation de notre image déployable dans notre WDS 
### Note : 
Comme dit dans le titre, vous allez apprendre à créer l’image à partir de toutes les configurations que nous avons faites précédemment. 

### Prérequis : 
- Aucun prérequis, si ce n’est d’avoir accès au MDT (évidemment).

### Procédure : 
1. Commencez par générer l’image dite « Light Touch ». C’est en fait une sorte de « boot.wim » pour ceux qui ont suivi la procédure WDS. 
Pour ce faire, ouvrez le MDT, cliquez droit sur votre « MDT Deployment Share », puis allez sur « Update Deployment Share ». 
2. Dans la fenêtre qui s’ouvre, choisissez l’option « Optimize the boot image updating process ». Cette option sert à actualiser l’image quand vous effectuerez des modifications légères dessus. 
Dans le cas où vous en feriez beaucoup d’un coup, privilégiez l’autre option (« Completely regenerate the boot images »).
3. Faites suivant à la page d’après, la tâche va se lancer. À la fin de celle-ci, vérifiez ce qui a été écrit dans la console pour s’assurer qu’il n’y a pas eu d’erreur.
4. Cela fait, ouvrez le WDS, cliquez droit sur et chargez notre nouvelle image « LiteTouchPE_x64.wim ». 
Vous la trouverez dans ce chemin : `W:\DeploymentShare\Boot\LiteTouchPE_x64.wim`.
5. Vous n’êtes pas obligé de la renommer. 
6. Faites suivant jusqu’à ce qu’elle soit ajoutée.
7. Elle s’affiche bien dans notre liste d’images de démarrage.

C’est tout pour la création et l’ajout de notre image !
