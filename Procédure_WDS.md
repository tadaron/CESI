# Procédure WDS
Procédure visant à apprendre à déployer une image Windows 10 avec le WDS.
Procédure liée à celle du MDT et du SYSPREP.

## Portée
À qui s’adresse cette procédure 

## Historique des versions
| Version | Date de révision | Descriptif du changement | Auteur   |
|---------|------------------|--------------------------|----------|
| V1      | 15/11/2023       | Création du A) et B)     | Rémi CST |
| V1.1    | 09/01/2024       | Complétion du B) et début du C) | Rémi CST |
| V1.2    | 10/01/2024       | Complétion du C) et création du D) | Rémi CST |
| V1.3    | 13/01/2024       | Complétion du D) et E)   | Rémi CST |
| V1.4    | 14/01/2024       | Vérification de la mise en forme | Rémi CST |

## Table des matières
- [A) Installation du rôle](#installation-du-rôle)
  - [Prérequis :](#prérequis-installation)
  - [Procédure :](#procédure-installation)
- [B) Configuration du rôle](#configuration-du-rôle)
  - [Prérequis :](#prérequis-configuration)
  - [Procédure :](#procédure-configuration)
- [C) Configuration d’une image Windows 10 pour le démarrage en mode BIOS](#configuration-image-windows-10)
  - [Prérequis :](#prérequis-configuration-image)
  - [Procédure :](#procédure-configuration-image)
- [D) Configuration du DHCP pour un démarrage en mode BIOS](#configuration-dhcp-bios)
  - [Note :](#note-configuration-dhcp-bios)
  - [Prérequis :](#prérequis-configuration-dhcp-bios)
  - [Procédure :](#procédure-configuration-dhcp-bios)
- [E) Configuration du DHCP pour un démarrage en mode UEFI/BIOS](#configuration-dhcp-uefi-bios)
  - [Note :](#note-configuration-dhcp-uefi-bios)
  - [Prérequis :](#prérequis-configuration-dhcp-uefi-bios)
  - [Procédure :](#procédure-configuration-dhcp-uefi-bios)

## A) Installation du rôle
### Prérequis : 
- Avoir une machine Windows Server 2022 de préférence.

### Procédure : 
1. Commencez par installer le rôle sur un serveur Windows 2022 de préférence. Dans mon cas, je vais le déployer sur mon premier contrôleur de domaine. N’essayez pas de l’installer sur votre serveur WSUS, cela ne marchera pas. 
   Allez dans le gestionnaire de serveur, cliquez sur « Gérer » et sur « Ajouter des rôles et fonctionnalités ».
2. Laissez tout par défaut, sélectionnez juste le rôle « Windows Deployment Services » et installez-le. 

## B) Configuration du rôle
### Prérequis : 
- Avoir le rôle DHCP configuré sur un serveur Windows sur votre réseau.
- Avoir un ISO de Windows 10 ou 11, personnalisé ou non.
- Avoir un lecteur supplémentaire sur le serveur, avec une contenance suffisante pour stocker des ISO, des images de démarrage et autres outils.

### Procédure :
1. Une fois le rôle installé, vous le trouverez sous le nom de « Services de déploiement Windows ».
2. Configurons donc le WDS : clic droit sur le serveur où est installé le rôle puis « Configurer le serveur ».
3. Nous sommes entrés dans l’« Assistant Configuration des services de déploiement Windows ». Configurons le serveur comme suit: 
   - **1ère page** : « Intégré à l’Active Directory », si vous avez un domaine. Sinon, choisissez « Serveur autonome ».
   - **2ème page** : Renseignez le dossier dans lequel les ISO et autres outils vont être stockés. Il faut que ce soit un lecteur autre que le C. Créez donc un nouveau lecteur, vous pouvez l’appeler WDS. Nous y créerons un dossier RemoteInstall, c’est en fait le nom par défaut du dossier du WDS.

   Faites suivant jusqu’à la page « Paramètres initiaux du serveur PXE ».
   - **4ème et dernière page** : Nous y sélectionnerons l’option « Répondre à tous les ordinateurs clients (connus et inconnus) » sans cocher la case « Exiger l’approbation administrateur pour les ordinateurs inconnus etc. ». 
   Je me permets ce choix, car, j’ai beaucoup de postes à déployer dans un délai plutôt court. Cela prime par conséquent sur la sécurité. À vous de voir si la sécurité est une composante importante pour vous.

## C) Configuration d’une image Windows 10 pour le démarrage en mode BIOS
### Prérequis : 
- Avoir un ISO de Windows 10.

### Procédure :
1. Serveur initialisé, vous allez maintenant ajouter une image de démarrage et une d’installation dans votre WDS.
   Pour ce faire, cliquez droit sur « Image de démarrage », puis « Ajouter une image de démarrage ». 
2. Une fenêtre vous permettant d’aller chercher le fichier « boot.wim » s’ouvre. Cliquez sur parcourir, puis ouvrez votre ISO de Windows 10.
   
   Dans mon cas, je suis sur l’hyperviseur VMware donc je vais simplement simuler le fait d’insérer un CD sur le PC avec l’option ci-dessous. Pour avoir cette option, il vous faudra aller dans les paramètres « Hardware » de la VM. 
   Cliquez donc sur « Add » et sélectionnez « CD/DVD Drive », allez chercher votre ISO Windows 10. C’est tout ce qu’il faudra faire. 
   Vous retrouverez votre ISO comme si vous aviez inséré un disque sur votre PC.
3. Le boot.wim se trouve dans le dossier « sources » de votre ISO.
4. Ouvrez-le. Vous avez donc votre fenêtre de sélection de fichier avec son chemin.
5. Après avoir validé le chemin, vous devrez choisir le nom de cette image de démarrage. Dans mon cas, c’est une image Win 10 64bit, donc écrivez Windows 10. 
6. À la prochaine page, vous avez le récapitulatif des manipulations que l’on a faites.
7. Cliquez sur suivant, cela va ajouter l’image de démarrage à votre bibliothèque d’images. 
8. Pour ce qui est d’ajouter l’image d’installation, cela reste très similaire.
   Cliquez droit sur « Images d’installation » puis sur « Ajouter une image d’installation ».
9. Nous créerons ensuite un groupe d’images « Windows 10 ». 
10. Comme pour l’image de démarrage, on va chercher le .wim. Cette fois il sera appelé « install.wim ».
11. Vous allez tomber sur un écran, qui vous permettra de choisir les images disponibles à l’installation, quand vous démarrerez la machine en PXE. 
   Nous choisirons seulement Windows 10 Pro, nous n’avons pas besoin des autres. Elles ne disposent pas des fonctionnalités que nous recherchons.
12. Faites suivant jusqu’à ce que l’image soit ajoutée à votre librairie d’images d’installation.

## D) Configuration du DHCP pour un démarrage en mode BIOS
### Note : 
Si vous essayez de configurer un ISO Windows 11, cela ne marchera pas. Un des prérequis de celui-ci est l’UEFI. Nous n’allons que configurer notre étendue pour le démarrage BIOS. L’UEFI fera l’objet du prochain point.

### Prérequis :
- Avoir un serveur Windows possédant le rôle DHCP sur le même réseau que le WDS.
- Avoir une étendue DHCP configurée.

### Procédure :
1. Ouvrez le rôle DHCP sur votre serveur, cliquez sur votre étendue, créée au préalable. Cliquez droit sur « Options d’étendue », puis configurez les options. 
2. La console des options s’ouvre. Vous y chercherez les options 66 et 67 afin de les configurer et de les activer.
   - **Option 66** : Nous y spécifierons l'adresse IP du serveur PXE, c'est ici c'est le serveur WDS.
   - **Option 67** : Nous y spécifierons le nom du fichier de démarrage, indiquez la valeur générique "boot\x64\wdsnbp.com".
3. Appliquez les changements et fermez la console. Vous retrouverez les valeurs configurées dans les « Options d’étendue ».

Vous pouvez maintenant démarrer votre machine sur le réseau en mode BIOS. 
Le DHCP va lui attribuer une IP. 
L’IP du serveur WDS va lui être renseigné et il pourra ainsi démarrer sur l’image Windows 10, préalablement configurée. 

Si la machine est une VM, pensez à : 1- Lui mettre le bon type de carte réseau ; 2- Ne pas lui spécifier d’ISO ; 3- La mettre en mode BIOS dans les paramètres avancés.

## E) Configuration du DHCP pour un démarrage en mode UEFI/BIOS
### Note : 
Nous allons configurer notre étendue DHCP afin que l’on puisse démarrer aussi bien sur l’UEFI que sur le BIOS. Cette méthode est donc valable pour Windows 10 ou 11. 
Les manipulations seront faites sous PowerShell car les faire à la main serait très long.
Nous allons donc :
- Activer la prise en charge de l'option 60 dans le serveur DHCP.
- Déclarer des classes de fournisseurs pour différencier les machines BIOS et UEFI.
- Créer une stratégie pour les machines en mode BIOS.
- Créer une stratégie pour les machines en mode UEFI.

### Prérequis : 
- Avoir un serveur Windows possédant le rôle DHCP sur le même réseau que le WDS.
- Avoir une étendue DHCP configurée.
- Avoir accès à PowerShell sur le serveur où est le DHCP.
- Avoir configuré un ISO de Windows 11 dans le WDS (même méthode que pour celui de Windows 10, voir « C) Configuration d’une image Windows 10 pour le démarrage en mode BIOS »).

### Procédure :
1. Allez sur votre serveur DHCP, ouvrez le rôle, et comme montré plus tôt, allez dans les « Options d’étendue ». 
   Commencez par supprimer la configuration que l’on vient de faire pour le mode BIOS.
2. Restez dans les options d’étendue. Vous allez vérifier que l’option 60 n’est pas affichée. Pas de problème, c’est tout à fait normal. 

   Vous allez donc ouvrir PowerShell et exécuter la commande suivante en remplaçant ce qui est souligné par vos informations :
