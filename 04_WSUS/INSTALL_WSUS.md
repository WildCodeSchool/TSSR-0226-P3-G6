## Sommaire

1. [Prérequis techniques](#1-prérequis-techniques)
2. [Étapes d'installation et de configuration](#2-étapes-dinstallation-et-de-configuration)
   * [A. Installation du rôle WSUS](#a-installation-du-rôle-wsus)
   * [B. Tâches de post-installation](#b-tâches-de-post-installation)
   * [C. Configuration initiale de WSUS](#c-configuration-initiale-de-wsus)
   * [D. Création des groupes d'ordinateurs WSUS](#d-création-des-groupes-dordinateurs-wsus)
3. [Configuration de la GPO (Déploiement)](#3-configuration-de-la-gpo-déploiement)

---

## 1. Prérequis techniques

Avant de procéder à l'installation du rôle WSUS, les éléments suivants doivent être validés :

* **Machine Virtuelle (SRVWIN04) :**
    * RAM : 4 Go minimum.
    * Réseau : Connectée au LAN.
    * Stockage : 
        * Disque 1 (OS) : 50 Go (C:)
        * Disque 2 (Stockage WSUS) : 50 Go
     
* **Configuration Réseau et Système :**
    * Adresse IP fixe configurée dans le sous-réseau LAN.
    * Serveur DNS pointant vers les contrôleurs de domaine (SRVWIN01 / SRVWIN02).
    * Nom de la machine défini sur `SRVWIN04`.
    * Serveur intégré au domaine Active Directory `tssr.lan`.
    * Mises à jour Windows Update du serveur SRVWIN04 effectuées avant l'ajout du rôle.

---

## 2. Étapes d'installation et de configuration

### A. Installation du rôle WSUS

1. Ouvrir le **Gestionnaire de serveur**.
2. Cliquer sur **Gérer** > **Ajouter des rôles et fonctionnalités**.
3. Type d'installation : Choisir **Installation basée sur un rôle ou une fonctionnalité**.
4. Sélection du serveur : Sélectionner `SRVWIN04.tssr.lan`.
5. Rôles de serveurs : Cocher **Services de mise à jour Windows Server (WSUS)**. Accepter l'ajout des fonctionnalités requises (notamment IIS).
6. Fonctionnalités : Laisser les choix par défaut, cliquer sur **Suivant**.
7. Services de rôle WSUS : S'assurer que **WID Connectivity** (Base de données interne) et **WSUS Services** sont cochés. Cliquer sur **Suivant**.
8. Emplacement du contenu : 
    * Cocher **Stocker les mises à jour aux emplacements suivants**.
    * Saisir le chemin pointant vers le deuxième disque dur (ex: `W:\WSUSContent`).
9. Rôle Serveur Web (IIS) : Laisser les paramètres par défaut, cliquer sur **Suivant**.
10. Cliquer sur **Installer**. Patienter jusqu'à la fin de l'installation.

### B. Tâches de post-installation

1. Dans le Gestionnaire de serveur, cliquer sur le drapeau d'alerte jaune en haut à droite.
2. Cliquer sur **Lancer les tâches de post-installation**.
3. Patienter pendant la création de la structure des dossiers et de la base de données WID.

### C. Configuration initiale de WSUS

1. Ouvrir la console **Services de mise à jour Windows Server** (via Outils d'administration).
2. L'assistant de configuration initiale se lance.
3. **Programme d'amélioration :** Décocher la participation.
4. **Serveur en amont :** Sélectionner **Synchroniser à partir de Microsoft Update**.
5. **Serveur proxy :** Laisser vide (sauf si le pare-feu pfSense impose un proxy explicite).
6. **Connexion :** Cliquer sur **Démarrer la connexion** (cette étape télécharge la liste des produits disponibles, cela peut prendre quelques minutes).
7. **Langues :** Sélectionner uniquement **Français** et **Anglais**.
8. **Produits :** Décocher "Tous les produits Windows". Sélectionner uniquement :
    * *Windows 10*
    * *Windows 11*
    * *Windows Server 2022* (pour les autres serveurs de l'infrastructure)
9. **Classifications :** Cocher uniquement :
    * *Mises à jour critiques*
    * *Mises à jour de sécurité*
10. **Planifier la synchronisation :** Choisir **Synchronisation automatique** et régler l'heure sur `02:00:00` (pour ne pas impacter la bande passante en journée).
11. Cocher **Commencer la synchronisation initiale** et cliquer sur **Terminer**.

### D. Création des groupes d'ordinateurs WSUS

1. Dans la console WSUS, étendre le nœud du serveur > **Ordinateurs** > **Tous les ordinateurs**.
2. Faire un clic droit sur **Tous les ordinateurs** > **Ajouter un groupe d'ordinateurs**.
3. Créer les groupes correspondant aux départements ou serveurs (ex: `Clients_EcoTech`, `Serveurs_Infrastructure`).
4. Aller dans **Options** > **Ordinateurs**.
5. Sélectionner : **Utiliser la stratégie de groupe ou les paramètres de registre des ordinateurs**. (Obligatoire pour l'application via GPO).
6. Cliquer sur **Appliquer** puis **OK**.

---

## 3. Configuration de la GPO (Déploiement)

Pour que les machines du domaine viennent s'enregistrer sur le WSUS, une GPO doit être configurée sur le contrôleur de domaine (SRVWIN01).

1. Sur SRVWIN01, ouvrir **Gestion de stratégie de groupe**.
2. Créer une nouvelle GPO nommée `GPO_WSUS_Clients` et la lier à l'OU contenant les ordinateurs clients.
3. Modifier la GPO et naviguer vers : `Configuration ordinateur > Stratégies > Modèles d'administration > Composants Windows > Windows Update > Gérer l'expérience utilisateur finale`.
4. Paramétrer **Configuration du service Mises à jour automatiques** : 
    * Activer. 
    * Option : *4 - Téléchargement auto et planification des installations*.
5. Naviguer vers : `Configuration ordinateur > Stratégies > Modèles d'administration > Composants Windows > Windows Update > Gérer les mises à jour proposés à partir du service de mise à jour Windows Server`.
6. Paramétrer **Spécifier l'emplacement du service de mise à jour intranet Microsoft** :
    * Activer.
    * Serveur intranet : `http://SRVWIN04.tssr.lan:8530`
    * Serveur de statistiques : `http://SRVWIN04.tssr.lan:8530`
7. Paramétrer **Autoriser le ciblage côté client** :
    * Activer.
    * Nom du groupe cible : `Clients_EcoTech` (doit correspondre exactement au nom créé sur WSUS).
8. Forcer l'actualisation de la stratégie sur un poste client de test avec la commande `gpupdate /force`, suivi de `wuauclt /reportnow`.
