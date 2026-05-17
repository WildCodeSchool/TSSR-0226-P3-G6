# Projet d'Infrastructure Réseau - EcoTech Solutions

## Sommaire
1. [Présentation du Projet](#1-présentation-du-projet)
2. [Nomenclature et Convention de Nommage](#2-nomenclature-et-convention-de-nommage)
3. [Plan d'Adressage IP et Segmentation](#3-plan-dadressage-ip-et-segmentation)
4. [Architecture de l'Annuaire (OU)](#4-architecture-de-lannuaire-ou)
5. [Liste du Matériel et Configuration des VM](#5-liste-du-matériel-et-configuration-des-vm)
6. [Schéma Réseau de l'Infrastructure](#6-schéma-réseau-de-linfrastructure)

---

## 1. Présentation du projet
Ce projet consiste en la conception et le déploiement d'une infrastructure réseau complète pour la société **EcoTech Solutions**. L'objectif est de migrer d'un environnement non managé vers une architecture centralisée sous Windows Server 2022 et Debian, sécurisée par un pare-feu pfSense.

### Contexte de l'entreprise
- **Effectif** : 245 collaborateurs répartis en 7 départements.
- **Localisation** : Bordeaux.
- **Parc matériel** : 100 % de PC portables.

---

## 2. Nomenclature et convention de nommage

### 2.1. Domaine
- **Nom FQDN** : `tssr.lan`

### 2.2. Serveurs et équipements

- **FW01** : Pare-feu pfSense.
- **SRVWIN01** : DC Principal (AD-DS, DNS, DHCP).
- **SRVWIN02** : DC Secondaire (AD-DS, DNS) - Windows Core.
- **SRVWIN04** : Serveur de mises à jour (WSUS).
- **SRVLX01** : Serveur Linux (GLPI, Messagerie).
- **IPBX01** : Serveur VoIP (FreePBX).

### 2.3. Postes clients

- **CLIWIN01** : Poste Windows 10.
- **CLIWIN02** : Poste Windows 11.

### 2.4. Objets Active Directory
- **Utilisateurs** : `<2_premières_lettres_prénom><nom>` (ex: `alroux`).

- **Groupes (AGDLP)** : 
  - Globaux : `G_G_<NomDepartement>` (ex: `G_G_DSI`).
  - Domaine Local : `G_DL_<Ressource>_<Droits>` (ex: `G_DL_PartageIT_RW`).

- **GPO** : `GPO_<Cible>_<Fonction>` (ex: `GPO_U_RestrictionHoraires`).

---

## 3. Plan d'adressage IP et segmentation

### 3.1. Zone WAN (Accès Internet)
- **Interface WAN Pare-feu** : IP via DHCP (Réseau Box FAI).

### 3.2. Zone LAN (Réseau Interne sécurisé) - 10.0.10.0/24
- **Interface LAN pfSense** : `10.0.10.254`.
- **Plage Serveurs (IP Fixes)** : `10.0.10.1` à `10.0.10.50`.
  - *SRVWIN01* : `10.0.10.10`.
  - *SRVWIN02* : `10.0.10.11`.
  - *SRVWIN04* : `10.0.10.12`.
  - *SRVLX01* : `10.0.10.20`.
  - *IPBX01* : `10.0.10.30`.
- **Plage Clients (DHCP)** : `10.0.10.100` à `10.0.10.200`.

### 3.3. Zone DMZ (Zone exposée) - 10.0.20.0/24
- **Interface DMZ pfSense** : `10.0.20.254`.
- **Serveur Web** : `10.0.20.10`.

---

## 4. Architecture de l'annuaire (OU)
Structure hiérarchique au sein de l'OU racine `OU_EcoTech` :
- **OU_Utilisateurs**
    - `OU_Commercial`, `OU_Communication`, `OU_Developpement`, `OU_Direction`, `OU_DSI`, `OU_Finance`, `OU_RH`.
 
- **OU_Ordinateurs**
  
- **OU_Serveurs**
  
- **OU_Groupes**

---

## 5. Liste du matériel et configuration des VM

| Nom VM | Rôles et OS | Interfaces Réseau Virtuelles |
| :--- | :--- | :--- |
| **FW01** | pfSense (Routage, Pare-feu) | 3 (WAN, LAN, DMZ) |
| **SRVWIN01** | Win Server 2022 GUI (AD DS, DNS, DHCP) | 1 (LAN) |
| **SRVWIN02** | Win Server 2022 Core (AD DS Redondant) | 1 (LAN) |
| **SRVWIN04** | Win Server 2022 (WSUS) | 1 (LAN) |
| **SRVLX01** | Debian CLI (GLPI, Messagerie) | 1 (LAN) |
| **IPBX01** | FreePBX (VoIP) | 1 (LAN) |
| **CLIWIN01** | Client Windows 10 | 1 (LAN) |
| **CLIWIN02** | Client Windows 11 | 1 (LAN) |

*Mots de passe par défaut : `Azerty1*` (Login admin pfSense: `admin` / `pfsense`).*

---

## 6. Schéma réseau de l'infrastructure

![schema](Ressources/Schema-réseau.png)

---

## 7. Journal de bord et suivi de projet

### Choix techniques
* **Hyperviseur** : Oracle VirtualBox

### Difficultés rencontrées

### Solutions et alternatives

### Améliorations possibles
