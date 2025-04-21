# Procédure SYSPREP
Procédure visant à créer une image déployable via MDT.

## Portée 
À qui s’adresse cette procédure 

## Historique des versions
| Version | Date de révision | Descriptif du changement | Auteur   |
|---------|------------------|--------------------------|----------|
| V1      | 07/12/2023       | Ajout du contenu, des étapes | Rémi Cst |
| V1.1    | 07/12/2023       | Vérification du contenu | Rémi Cst |
| V1.2    | 08/12/2023       | Mise en forme           | Rémi Cst |

## Table des matières
- [A) Préparer le système à cloner avec SYSPREP](#préparer-le-système)
  - [Prérequis :](#prérequis-préparer-le-système)
  - [Procédure :](#procédure-préparer-le-système)
- [B) Préparer l’outil WinPE](#préparer-outil-winpe)
  - [Prérequis :](#prérequis-préparer-outil-winpe)
  - [Procédure :](#procédure-préparer-outil-winpe)
- [C) Préparer le .wim](#préparer-le-wim)
  - [Prérequis :](#prérequis-préparer-le-wim)
  - [Procédure :](#procédure-préparer-le-wim)

## A) Préparer le système à cloner avec SYSPREP
### Prérequis : 
- Machine Win10 ou 11 propre (pas de machine montée depuis Win7 ou autre).
- Tous les logiciels à déployer sont installés sur la machine.

### Procédure : 
1. Faites une machine Windows 10 ou 11 (physique ou virtuelle).
2. Vérifiez que les logiciels que vous voulez retrouver sur les postes déployés sont installés sur le PC.
3. Une fois que tout cela est fait, exécutez l’outil « sysprep.exe » situé dans `C:\Windows\System32\Sysprep`. 
4. L’« outil de préparation système » s’ouvre.
   - Dans « action de nettoyage du système », choisissez « Entrer en mode OOBE (Out-of-Box Experience) ». Cette option permettra aux utilisateurs finaux de personnaliser toutes leurs options lors du premier démarrage de l'ordinateur déployé (paramètres régionaux et autres préférences). 
   - Cochez « Généraliser » pour supprimer toutes les informations spécifiques au matériel, comme les identifiants SID.
   - Choisissez l’option d’extinction « Arrêter » afin d’éteindre l'ordinateur après le processus.

## B) Préparer l’outil WinPE
### Prérequis : 
- Machine Windows avec connexion internet.

### Procédure : 
1. Téléchargez l’“ADK pour Windows” et son addon via ce lien. Ces outils serviront à avoir accès à un terminal pour créer l’ISO de WinPE.
2. Ouvrez l’« adksetup.exe » précédemment téléchargé et ne touchez pas les valeurs par défaut. Faites suivant et installez. Faites la même chose pour le « adkwinpesetup ».
3. Maintenant que tout est installé, ouvrez l’« environnement de déploiement et d’outils de création d’images ».
4. Tapez la commande `copype amd64 C:\WinPE` si vous avez un processeur 64 bits et que vous souhaitez copier les fichiers dans le chemin `C:\WinPE`. Modifiez les informations au besoin. Cette commande va copier les fichiers requis à la création de l’ISO, dans le dossier sélectionné.
5. Exécutez la commande `MakeWinPEMedia /ISO C:\WinPE C:\WinPE\WinPE.iso` afin de créer l’ISO à partir des fichiers précédemment copiés. Encore une fois, adaptez les chemins en fonction de ce que vous avez. 
6. Bravo, vous avez maintenant l’ISO de WinPE. Il se trouve dans le dossier `C:\WinPE` si vous n’avez pas touché le chemin par défaut de la commande.
7. Optionnel : Vous pouvez maintenant le graver sur une clé avec un outil adapté (RUFUS par exemple).

## C) Préparer le .wim
### Prérequis : 
- Avoir préparé le système à capturer avec SYSPREP.
- Avoir une clé gravée avec l’ISO de WinPE ou simplement l’ISO si vous êtes sur environnement virtualisé.
- Avoir branché une clef USB ou ajouter un disque supplémentaire sur le PC.

### Procédure : 
1. Maintenant que vous avez l’ISO de WinPE, démarrez sur la clé (machine physique) ou l’ISO (machine virtuelle).
2. Vous avez devant vous la console administrateur. Tapez la commande `diskpart` afin d’accéder au mode gestion de disque. Ensuite, tapez `list vol` afin d’avoir la liste des lecteurs disponibles (disques durs, clés USB, etc.).
3. Repérez dans la liste quel lecteur est à cloner et sur quel lecteur envoyer le .wim du système cloné, puis faites `Exit` pour quitter le mode de gestion de disques.
4. Lancez la génération du .wim avec la commande :
   dism /Capture-Image /ImageFile:D:\MonImage.wim /CaptureDir:C:\ /Name:"NomDeTonImage"
- Remplacez `D:\MonImage.wim` par le chemin où vous voulez enregistrer l'image.
- Remplacez `NomDeTonImage` par un nom significatif pour l'image. 
5. Une fois que le processus est terminé, quittez l’utilitaire. Cela va démarrer Windows. 
6. Vérifiez maintenant que vous retrouvez bien votre image .wim dans le lecteur que vous avez précisé dans la commande précédente.
