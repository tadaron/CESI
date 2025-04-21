# Procédure WSUS
Procédure pour l’installation/configuration de WSUS et des GPOs associées

## Portée
À qui s’adresse cette procédure 

## Historique des versions
| Version | Date de révision | Descriptif du changement | Auteur   |
|---------|------------------|--------------------------|----------|
| V1      | 08/12/2023       | Création et complétion des champs « administratifs » | Rémi Cst |
| V1.1    | 08/12/2023       | Ajout du contenu du A) et du B) | Rémi Cst |
| V1.2    | 09/12/2023       | Ajout de contenu dans le C) | Rémi Cst |
| V1.3    | 10/12/2023       | Ajout de contenu dans le C) | Rémi Cst |
| V1.4    | 12/12/2023       | Création et premiers remplissages du D) | Rémi Cst |
| V1.5    | 10/02/2024       | Création du E) | Rémi Cst |
| V1.6    | 11/02/2024       | Finition du E) | Rémi Cst |

## Table des matières
- [A) Installation du rôle :](#installation-du-rôle)
  - [Prérequis :](#prérequis-installation)
  - [Procédure :](#procédure-installation)
- [B) Configuration du rôle, premier démarrage](#configuration-premier-démarrage)
  - [Prérequis :](#prérequis-configuration)
  - [Procédure :](#procédure-configuration)
- [C) Configuration du rôle, paramètres spécifiques](#configuration-paramètres-spécifiques)
  - [Prérequis :](#prérequis-configuration-spécifiques)
  - [Procédure :](#procédure-configuration-spécifiques)
- [D) Mise en place des GPOs](#mise-en-place-des-gpos)
  - [Prérequis :](#prérequis-gpos)
  - [Procédure :](#procédure-gpos)
- [E) Test du rôle :](#test-du-rôle)
  - [Prérequis :](#prérequis-test)
  - [Procédure :](#procédure-test)

## A) Installation du rôle 
### Prérequis :
- Machine sous Windows Server 2022 possédant un accès à Internet 

### Procédure : 
1. Ouvrez le « gestionnaire de serveur » sur votre serveur.
2. Gestionnaire de serveur ouvert, cliquez sur « Gérer » en haut à gauche puis sur « Ajouter des rôles et fonctionnalités ». L’ « assistant Ajout de rôles et de fonctionnalités » s’ouvre.
3. Dans l’assistant, nous allons choisir :
   - Type d’installation : « Installation basée sur un rôle ou une fonctionnalité »
   - Sélection du serveur : « Sélectionner un serveur du pool de serveurs » et le serveur sur lequel vous allez installer le rôle
   - Rôles de serveurs : Services WSUS (Windows Server Update Services)
   - Fonctionnalités : Laissez les choix par défaut 
   - Services de rôles : Laissez par défaut
   - Contenu : Spécifiez le chemin où vous souhaitez stocker les mises à jour téléchargées (/!\ En fonction de ce que vous allez choisir de mettre à jour, prévoir assez de place pour stocker les mises à jour)
   Vous n’avez plus qu’à continuer jusqu’à installer le rôle. 
4. Dernière étape, vous allez cliquer sur le drapeau avec le point d’exclamation en haut à droite puis sur « lancer les tâches de post-installation ».

Félicitations ! L’installation du rôle est terminée ! Passons à présent à la configuration du service

## B) Configuration du rôle, premier démarrage
### Prérequis : 
- Avoir un accès à internet décent sur le serveur
- Avoir le rôle installé

### Procédure : 
1. Ouvrez le « gestionnaire de serveur », cliquez sur « gérer » et ouvrez « Services WSUS (Windows Server Update Services) ».
2. Au premier démarrage, c’est l’outil de configuration nommé « Windows Server Update Services Configuration Wizard : WSUS » qui démarre.
3. Vous laisserez tout par défaut jusqu’à cliquer sur « Start Connecting » afin de débuter la synchronisation avec les serveurs de Microsoft. Cela peut prendre du temps.
4. L’étape d’après est le choix des langues des mises à jour à télécharger. Choisissez français pour les machines des utilisateurs qui seront mises à jour. L’anglais peut être une option si vous avez des serveurs dans cette langue.
5. À la prochaine page, vous aurez à choisir pour quel produit télécharger les mises à jour. Nous prendrons au minimum Windows 10 et/ou Windows 11. Faites bien attention à prendre Windows 10, version 1903 and Later et non pas Windows 10 tout court.
6. Prochaine étape, nous choisirons le type de mises à jour. Je vous conseille de ne prendre que celles qui seront vraiment utiles :
   - Mises à jour critiques
   - Mises à jour de la sécurité
   - Mise à jour (tout court)
   - Mises à jour de définitions
   Faites suivant.
7. Nous définirons maintenant la fréquence de synchronisation des mises à jour avec les serveurs de Microsoft. Je vous conseille de choisir la synchro automatique, une fois par jour. À vous de voir quand est-ce que le serveur sera disposé à les prendre.
8. Plus qu’à cliquer sur « Begin initial synchronisation » afin de lancer automatiquement la première synchronisation des mises à jour et de cliquer sur « Finish » sur la page suivante.

Une synchronisation va commencer. Veillez à ce qu’il n’y ait pas de coupure d’internet. Le processus peut prendre énormément de temps, en fonction de votre bande passante.

## C) Configuration du rôle, paramètres spécifiques 
### Prérequis :
- Serveur intégré à un domaine
- Accès à internet sur le serveur
- Aucune synchronisation en cours du WSUS

### Procédure :
1. Première étape, vérifier dans l’onglet « Synchronisation » qu’aucune mise à jour n’est en cours. Si c’est le cas, attendez la fin ou cliquez sur « Stop Synchronization », en haut à gauche.
2. Avant de continuer, sachez qu’il est possible de faire remonter les PCs du parc dans des groupes. Quelle utilité ? Pour trier les postes en fonction de leur localisation ou de leur importance, etc. Afin de leur appliquer des paramètres différents. C’est ce que nous allons faire maintenant.
   
   Ouvrez la console WSUS, comme vue précédemment, et cliquez sur « Options » à gauche puis sur « Computers ». Nous allons faire en sorte que l’intégration des ordinateurs aux groupes soit automatique grâce aux GPOs.
3. Vous avez devant vous 2 options, nous choisirons « Use Group Policy or registry settings on computers » pour gérer les ordinateurs avec des GPOs, comme dit précédemment. Cliquez sur OK.
4. La prochaine étape consistera à la création des groupes où les PC atterriront. Allez dans « computers » sur le panneau latéral de la console. Pas dans les paramètres. Dépliez « Computers », vous y trouverez « Tous les Ordinateurs », dépliez encore. Vous avez devant vous le groupe « Ordinateurs non attribués », c’est le groupe par défaut où les PC atterrissent. 
5. Nous allons créer deux groupes : 
   - Ordinateurs (Groupe de base qui aura toutes les mises à jour)
   - Ordinateurs tests (Groupe de test qui aura les mises à jour avant le groupe « Ordinateurs ». Pourquoi ? Pour éviter de déployer une mise à jour buguée).
6. Créons maintenant une « vue » sur les mises à jour d'une classe et produit spécifique. Comme exemple nous prendrons les mises à jour de sécurité de Windows 11. Cliquez sur « Update » puis sur « Add Update View ».
7. Vous avez 3 choses à faire :
   - Étape 1, sélectionnez la propriété. Dans notre cas, prenez « Updates are in a specific product ». 
   - Étape 2, dans la case en dessous, vous pourrez cliquer sur le texte en bleu « Any product » pour sélectionner quel produit vous voulez. Cliquez dessus et cherchez « Windows 11 ».
   - Étape 3, ajoutons un critère de recherche pour voir seulement les mises à jour de sécurité. C’est la même méthode.
   - Étape 4, entrez le nom de la vue. En l’occurrence, « MAJs sécurité - Windows 11 ».
8. Cela fait, cliquez sur votre vue. C’est ici que vous verrez les mises à jour du produit que vous avez sélectionné. Vous avez à disposition des filtres, par exemple vous pouvez trier les mises à jour non approuvées dont vos PCs ont besoin (filtres « Unapproved » et « Needed »).
9. Dernière étape de la configuration. Vous pouvez voir sur cette page que nous avons accès à beaucoup de paramètres. Notamment à celui de l’« Automatic Approvals » qui va nous permettre d’approuver automatiquement les mises à jour critiques et de sécurité. Cliquez donc sur ladite option et cochez « Règle d’approbation par défaut », c’est d’elle que nous parlions précédemment. Cliquez sur OK.

## D) Mise en place des GPOs 
### Prérequis :
- Accès au rôle « Gestion de stratégie de groupe » de votre domaine

### Procédure : 
1. Nous commencerons par créer des GPOs afin que les ordinateurs du domaine remontent d'eux-mêmes dans les groupes du WSUS. La première étape va être d’ouvrir le rôle « Gestion de stratégie de groupe » et déplier la forêt, le domaine et d’aller au dossier « Objets de stratégie de groupe » dans [votre domaine]. 
2. Nommez-le clairement. Par exemple, « WSUS – Grp Ordinateurs » si l’on reprend le nom des groupes créés précédemment dans le WSUS. Vous l’aurez d’ailleurs compris, nous allons configurer la GPO des ordinateurs lambdas du Castillonais.
3. Cliquez droit dessus puis modifiez.
4. Nous allons maintenant modifier la GPO sur plusieurs paramètres, que vous trouverez dans `Configuration ordinateur -> Stratégies -> Modèles d’administration -> Composants Windows -> Windows Update`.
5. Ouvrez la GPO « Spécifier l’emplacement intranet du service de mise à jour Microsoft ». Cette GPO va nous servir à indiquer aux machines le serveur qui proposera les mises à jour, autrement dit le WSUS.
6. Seconde GPO, nous allons choisir l’option « 4- Téléchargement automatique et planification des installations », son nom parle de lui-même. Pour ce qui est du jour, de l’heure et de la semaine, c’est à vous de voir en fonction des personnes qui utilisent les machines et de leurs horaires de travail.
7. Autre GPO, nommée « Ne pas se connecter à des emplacements Internet Windows Update ». Nous avons précédemment précisé aux machines où aller chercher les mises à jour. Nous allons compléter cela en leur interdisant d’aller les chercher sur les serveurs Microsoft. L’intérêt est d’éviter que toutes les machines aillent sur internet et monopolisent la bande passante.
8. Avant-dernière GPO, consistant à spécifier dans quel groupe du WSUS, les ordinateurs vont être envoyés. Vous les avez déjà créés précédemment. Plus qu’à donner le nom dans le seul champ de la GPO.
9. Dernière GPO, qui va nous permettre d’empêcher le redémarrage intempestif des PC pendant les horaires de travail. Elle se nomme « Désactiver le redémarrage automatique pour les mises à jour pendant les heures d’activité ». C’est encore à vous de voir.
10. Après avoir fini tous les paramétrages, allez sur l’UO sur laquelle vous souhaitez appliquer la GPO. Par exemple, une UO « Ordinateur ». Cliquez droit et sélectionnez « Lier un objet de stratégie de groupe existant ». Sélectionnez votre GPO.

Pensez à vérifier que votre ordinateur soit dans l’UO « Ordinateur » où vous avez mis votre GPO.

Vous avez terminé de configurer les GPOs pour les postes « basiques » du réseau. Vous pouvez maintenant refaire la même chose pour chaque groupe d’ordinateurs de votre WSUS. À vous de voir quels paramètres des GPOs seront modifiés.

## E) Test du rôle 
### Prérequis : 
- Avoir fini toutes les configurations précédentes 

### Procédure : 
1. Démarrez maintenant une machine cliente et lancez les mises à jour dans Windows Update, faites bien attention à ce qu’elle soit intégrée à votre réseau. Attendez 30 secondes. 
2. Allez dans votre WSUS et vérifiez que votre ordinateur remonte bien dans son groupe. 
3. Si vous voyez le PC dans le bon groupe, alors approuvez une mise à jour, n’importe laquelle, et attendez quelques minutes, le temps que votre serveur la télécharge et la déploie. Pour l’exemple, j’ai pris une mise à jour de sécurité dans notre vue précédemment créée.

   Choisissez ensuite vers quel(s) groupe(s) l’approuver. Dans mon cas, vers le groupe où j’ai déjà un poste.
4. Quelques minutes après, vous devriez voir que votre machine cliente télécharge depuis le serveur la mise à jour précédemment validée. Il va ensuite l’installer.

Vous savez maintenant installer et configurer un WSUS ! À vous de jouer pour adapter les configurations à votre besoin.
