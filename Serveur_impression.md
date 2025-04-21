# Procédure Serveur d’Impression

## Portée

Ce document s’adresse à tout technicien souhaitant installer un serveur d’impression et déployer une imprimante.

## Historique des versions

| Version | Date        | Descriptif du changement             | Auteur         |
|---------|-------------|--------------------------------------|----------------|
| V1      | 10/12/2023  | Ajout du A) et du B)                 | Rémi Cst       |
| V1.1    | 12/12/2023  | Vérification du contenu              | Rémi Cst       |
| V1.2    | 12/12/2023  | Ajout d’une partie du C)             | Rémi Cst       |
| V1.3    | 13/12/2023  | Ajout de la 2ème partie du C)        | Rémi Cst       |
| V1.4    | 14/12/2023  | Mise en forme                        | Rémi Cst       |
| V1.5    | 09/01/2024  | Correction et Finalisation           | Antoine Oriol  |
| V1.6    | 27/01/2024  | Relecture                            | Rémi Cst       |

## Table des matières

- [Portée](#portée)
- [Historique des versions](#historique-des-versions)
- [A) Installation du rôle](#a-installation-du-rôle)
  - [Prérequis](#prérequis)
  - [Procédure](#procédure)
- [B) Installation d’une imprimante](#b-installation-dune-imprimante)
  - [Prérequis](#prérequis-1)
  - [Procédure](#procédure-1)
- [C) Déploiement par GPOs](#c-déploiement-par-gpos)
  - [Prérequis](#prérequis-2)
  - [Procédure](#procédure-2)

---

## A) Installation du rôle

### Prérequis

- Machine sous Windows Server 2019 minimum

### Procédure

1. Ouvrir le gestionnaire de serveur
![Ouverture du gestionnaire de serveur](assets/impression_gestionnaire_de_serveur.png)
3. Installer le rôle **Service d’impression et de numérisation de document**
4. Laissez coché **Serveur d’impression**
5. Ne touchez à rien d’autre. Cliquer sur *Suivant* jusqu’à cliquer sur *Installer*

---

## B) Installation d’une imprimante

### Prérequis

- Avoir accès à Internet (pas forcément sur le serveur)
- (Optionnel) Avoir une imprimante physique

### Procédure

1. Télécharger les pilotes de l’imprimante sur le site du constructeur (format **PostScript** recommandé)
2. Ouvrir **Gestion de l’impression** (dans les outils d’administration Windows)
3. Ajouter un **pilote** :
   - Clic droit sur **Pilotes** > *Ajouter un pilote*
   - Suivant > choisir **x64**
   - Sélectionner le pilote ou utiliser *Disque fourni* pour importer le fichier `.inf`
4. Ajouter un **port** :
   - Clic droit sur **Ports** > *Ajouter un port*
   - Type **TCP/IP** > Suivant > Entrer l’adresse IP de l’imprimante
   - Continuer même si l’imprimante n’est pas trouvée
5. Ajouter l’**imprimante** :
   - Clic droit sur **Imprimantes** > *Ajouter*
   - Choisir le port et le pilote ajoutés
   - Ne pas partager tout de suite
6. Partager l’imprimante :
   - Clic droit > Gérer le partage > *Partager l’imprimante* et *Lister dans l’annuaire*

---

## C) Déploiement par GPOs

### Prérequis

- Serveur avec rôle AD et GPOs activé

### Procédure

1. Ouvrir **Gestion de stratégies de groupe**
2. Créer une GPO nommée `Imprimante déploiement` dans l’OU des utilisateurs concernés :
   - Clic droit sur l’OU > *Créer un objet de GPO dans ce domaine, et le lier ici…*
3. Modifier la GPO :
   - Clic droit > *Modifier*
   - Dans l’éditeur, naviguer jusqu’à :  
     `Configuration utilisateur > Paramètres > Panneau de configuration > Imprimantes`
   - Clic droit > *Nouveau* > *Imprimante partagée*
4. Configurer l’imprimante :
   - Action : `Créer`
   - Sélectionner l’imprimante dans l’annuaire
   - Cocher si elle doit être l’imprimante par défaut
   - Onglet **Commun** : cocher *Exécuter dans le contexte de sécurité de l’utilisateur connecté*

5. Sur un poste client :
   - Ouvrir une invite de commande
   - Exécuter `gpupdate /force`
   - Se connecter avec un utilisateur de l’OU concernée

---

**Procédure validée par Mr CIMADOMO – Responsable du SNTS**
