# 🛠️ Fichier INSTALL - Infrastructure EcoTech Solutions

**Projet :** Déploiement de l'infrastructure système et réseau
**Client :** EcoTech Solutions (245 collaborateurs)
**Rôle cible :** Administrateurs Systèmes et Réseaux

---

## 📑 Sommaire
1. [Prérequis Techniques](#1-prérequis-techniques)
2. [Étapes d'installation et de configuration : Pare-feu (FW01)](#2-étapes-dinstallation-et-de-configuration--pare-feu-fw01)
   - [Étape 2.1 : Création de la Machine Virtuelle](#étape-21--création-de-la-machine-virtuelle)
   - [Étape 2.2 : Installation de pfSense](#étape-22--installation-de-pfsense)
   - [Étape 2.3 : Assignation des interfaces](#étape-23--assignation-des-interfaces-console)
   - [Étape 2.4 : Configuration des adresses IP](#étape-24--configuration-des-adresses-ip-console)
   - [Étape 2.5 : Sécurisation initiale](#étape-25--sécurisation-initiale-webgui)
   - [Étape 2.6 : Implémentation du Deny All et règles de flux LAN](#étape-26--implémentation-du-deny-all-et-règles-de-flux-lan)

---

## 1. Prérequis Techniques

Avant de débuter l'installation, assurez-vous de disposer de l'environnement suivant :

### Matériel / Hyperviseur
* **Logiciel :** Oracle VM VirtualBox (dernière version stable).
* **Ressources hôte recommandées :** Minimum 16 Go de RAM, 100 Go d'espace disque (SSD recommandé), CPU 4 cœurs minimum.

### Fichiers ISO nécessaires
* **pfSense :** pfSense-CE-2.7.x-RELEASE-amd64.iso
* **Windows Server :** Windows Server 2022 (Desktop Experience & Core)
* **Linux :** Debian 12 CLI (Netinst)

---

## 2. Étapes d'installation et de configuration : Pare-feu (FW01)

### Étape 2.1 : Création de la Machine Virtuelle
1. Dans VirtualBox, créer une nouvelle VM nommée `FW01`.
2. **Type :** BSD / **Version :** FreeBSD (64-bit).
3. **RAM :** 1024 Mo / **Disque dur :** 20 Go (VDI, allocation dynamique).
4. **Configuration Réseau (Crucial) :**
    * **Carte 1 (WAN) :** Accès par pont (Sélectionner la carte réseau physique connectée à Internet).
    * **Carte 2 (LAN) :** Réseau interne (Nom : `LAN_ECOTECH`).
    * **Carte 3 (DMZ) :** Réseau interne (Nom : `DMZ_ECOTECH`).

### Étape 2.2 : Installation de pfSense
1. Démarrer la VM en montant l'ISO pfSense.
2. Accepter la charte (Accept) et choisir `Install`.
3. Sélectionner la disposition clavier (Keymap : `fr` ou conserver par défaut).
4. Partitionnement : Auto (ZFS) -> Proceed with Installation -> Stripe -> Sélectionner le disque avec Espace -> `YES`.
5. À la fin de l'installation, choisir `Reboot`. Retirer l'ISO.

### Étape 2.3 : Assignation des interfaces (Console)
1. Au redémarrage, pfSense demande si des VLANs doivent être configurés : Taper `n`.
2. **WAN interface :** Saisir `em0` (Carte 1).
3. **LAN interface :** Saisir `em1` (Carte 2).
4. **Optional 1 interface (DMZ) :** Saisir `em2` (Carte 3).
5. Valider avec `y`.

### Étape 2.4 : Configuration des adresses IP (Console)
1. Dans le menu pfSense, taper `2` (Set interface(s) IP address).
2. **LAN (em1) :**
    * Sélectionner l'interface LAN.
    * Saisir l'adresse IP : `172.16.20.254`
    * Masque de sous-réseau (Subnet bit count) : `24`
    * Passerelle (Gateway) : Appuyer sur Entrée (Aucune).
    * DHCP Server : `n` (Sera géré par le serveur Windows AD).
    * Revert to HTTP (WebGUI) : `y`.
3. **DMZ (em2) :**
    * Répéter l'opération.
    * Adresse IP : `172.16.30.254`
    * Masque : `24`.

### Étape 2.5 : Sécurisation initiale (WebGUI)
1. Démarrer une machine cliente sur le réseau `LAN_ECOTECH` avec une IP statique temporaire (ex: `172.16.20.10/24`, Passerelle: `172.16.20.254`).
2. Ouvrir un navigateur web et aller sur `http://172.16.20.254`.
3. Se connecter avec les identifiants par défaut :
    * **Utilisateur :** `admin`
    * **Mot de passe :** `pfsense`
4. Suivre l'assistant de configuration initial (Setup Wizard) :
    * Hostname : `FW01` / Domain : `tssr.lan`
    * Time Server : `0.fr.pool.ntp.org` / Timezone : `Europe/Paris`
    * Modifier le mot de passe administrateur par défaut.

### Étape 2.6 : Implémentation du Deny All et règles de flux LAN
L'objectif de cette étape est de respecter le cahier des charges de sécurité imposant un principe de moindre privilège ("Deny All").
Aller dans **Firewall > Rules > LAN**. Cliquer sur le bouton **Add** (flèche vers le bas) pour ajouter chaque règle dans l'ordre suivant :

#### Règle 1 : Autoriser le Ping (ICMP)
*   **Action :** Pass
*   **Interface :** LAN
*   **Address Family :** IPv4
*   **Protocol :** ICMP
*   **ICMP Subtype :** Echo Request
*   **Source :** Network -> `LAN subnets`
*   **Destination :** Any
*   **Description :** LAN - Autoriser Ping (ICMP)
*   *Justification technique :* Indispensable pour le diagnostic réseau (troubleshooting). Permet aux équipements du LAN de vérifier la joignabilité d'hôtes externes.

#### Règle 2 : Autoriser la résolution de noms (DNS)
*   **Action :** Pass
*   **Interface :** LAN
*   **Address Family :** IPv4
*   **Protocol :** TCP/UDP
*   **Source :** Network -> `LAN subnets`
*   **Destination :** Any
*   **Destination Port Range :** From `DNS (53)` To `DNS (53)`
*   **Description :** LAN - Autoriser requêtes DNS
*   *Justification technique :* Permet de traduire les noms de domaine en adresses IP. Le protocole UDP est utilisé pour les requêtes standards, le TCP pour les réponses volumineuses ou les transferts de zones.

#### Règle 3 : Autoriser la synchronisation horaire (NTP)
*   **Action :** Pass
*   **Interface :** LAN
*   **Address Family :** IPv4
*   **Protocol :** UDP
*   **Source :** Network -> `LAN subnets`
*   **Destination :** Any
*   **Destination Port Range :** From `NTP (123)` To `NTP (123)`
*   **Description :** LAN - Autoriser la synchronisation horaire (NTP Port 123)
*   *Justification technique :* Maintient l'horloge des machines synchronisée. Critique pour le bon fonctionnement de l'Active Directory (tolérance maximale de 5 minutes d'écart pour l'authentification Kerberos).

#### Règle 4 : Autoriser le surf Web (HTTP)
*   **Action :** Pass
*   **Interface :** LAN
*   **Address Family :** IPv4
*   **Protocol :** TCP
*   **Source :** Network -> `LAN subnets`
*   **Destination :** Any
*   **Destination Port Range :** From `HTTP (80)` To `HTTP (80)`
*   **Description :** LAN - Autoriser surf Web (HTTP 80)
*   *Justification technique :* Permet la navigation sur des sites web non chiffrés ou les redirections automatiques vers le HTTPS.

#### Règle 5 : Autoriser le surf Web Sécurisé (HTTPS)
*   **Action :** Pass
*   **Interface :** LAN
*   **Address Family :** IPv4
*   **Protocol :** TCP
*   **Source :** Network -> `LAN subnets`
*   **Destination :** Any
*   **Destination Port Range :** From `HTTPS (443)` To `HTTPS (443)`
*   **Description :** LAN - Autoriser surf Web Sécurisé (HTTPS 443)
*   *Justification technique :* Autorise la navigation chiffrée. Impératif pour les utilisateurs de l'entreprise afin d'accéder à Internet de manière sécurisée et d'utiliser la messagerie hébergée dans le Cloud (Webmail).

#### Application du Deny All (Blocage par défaut)
1.  Repérer la règle nommée `Default allow LAN to any rule` (IPv4).
2.  Cliquer sur l'icône de statut (coche verte) pour la **désactiver**. L'icône devient grise (Disable). Répéter l'opération pour la règle IPv6.
3.  Cliquer sur le bouton **Apply Changes** en haut à droite pour appliquer la nouvelle politique de sécurité. *Note : Tout trafic ne correspondant pas aux 5 règles ci-dessus sera désormais implicitement bloqué.*

