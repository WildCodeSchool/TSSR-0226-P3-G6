## Sommaire

* [1. Installation et promotion du contrôleur de domaine Active Directory (AD DS)](1.installation-et-promotion-du-contrôleur-de-domaine-active-directory-ad-ds)
  * [a. Phase 1 : Installation des fichiers des rôles AD DS et DNS](#a-phase-1--installation-des-fichiers-des-rôles-ad-ds-et-dns)
    * [Étape 1 : Écran d'accueil de l'assistant](#étape-1--écran-daccueil-de-lassistant)
    * [Étape 2 : Choix du type d'installation](#étape-2--choix-du-type-dinstallation)
    * [Étape 3 : Sélection du serveur cible](#étape-3--sélection-du-serveur-cible)
    * [Étape 4 : Sélection des rôles à installer](#étape-4--sélection-des-rôles-à-installer)
    * [Étape 5 : Validation des fonctionnalités complémentaires](#étape-5--validation-des-fonctionnalités-complémentaires)
    * [Étape 6 : Écran d'information du Serveur DNS](#étape-6--écran-dinformation-du-serveur-dns)
    * [Étape 7 : Écran d'information AD DS](#étape-7--écran-dinformation-ad-ds)
    * [Étape 8 : Confirmation avant le lancement](#étape-8--confirmation-avant-le-lancement)
    * [Étape 9 : Progression de l'installation](#étape-9--progression-de-linstallation)
    * [Étape 10 : Fin de l'installation des composants](#étape-10--fin-de-linstallation-des-composants)
  * [b. Phase 2 : Assistant de Configuration et Promotion du Domaine](#b-phase-2--assistant-de-configuration-et-promotion-du-domaine)
    * [Étape 11 : Notification dans le Gestionnaire de serveur](#étape-11--notification-dans-le-gestionnaire-de-serveur)
    * [Étape 12 : Lancement de la promotion](#étape-12--lancement-de-la-promotion)
    * [Étape 13 : Configuration du déploiement de la forêt](#étape-13--configuration-du-déploiement-de-la-forêt)
    * [Étape 14 : Options du contrôleur de domaine et mot de passe DSRM](#étape-14--options-du-contrôleur-de-domaine-et-mot-de-passe-dsrm)
    * [Étape 15 : Options DNS (Message d'avertissement)](#étape-15--options-dns-message-davertissement)
    * [Étape 16 : Nom NetBIOS du domaine](#étape-16--nom-netbios-du-domaine)
    * [Étape 17 : Examen des options choisies](#étape-17--examen-des-options-choisies)
    * [Étape 18 : Vérification des prérequis et installation](#étape-18--vérification-des-prérequis-et-installation)
  * [c. Phase 3 : Finalisation, Redémarrage et Validation](#c-phase-3--finalisation-redémarrage-et-validation)
    * [Étape 19 : Notification de redémarrage automatique](#étape-19--notification-de-redémarrage-automatique)
    * [Étape 20 : Connexion au domaine d'entreprise](#étape-20--connexion-au-domaine-dentreprise)
    * [Étape 21 : Validation du Gestionnaire de serveur opérationnel](#étape-21--validation-du-gestionnaire-de-serveur-opérationnel)
* [2. Domaine AD DS](#2-domaine-ad-ds)
  * [Organisation AD DS](#organisation-ad-ds)
    * [Étape 1 : OU (Unités d'Organisation)](#étape-1--ou-unités-dorganisation)
    * [Étape 2 : Groupes (Implémentation de l'AGDLP)](#étape-2--groupes-implémentation-de-lagdlp)
    * [Étape 3 : Utilisateurs](#étape-3--utilisateurs)  

## 1. Installation et promotion du contrôleur de domaine Active Directory (AD DS)

Cette section détaille pas à pas l'installation des rôles AD DS et DNS sur le serveur `SRVWIN01`, suivie de sa promotion pour créer la racine de la forêt `tssr.lan`.

### a. Phase 1 : Installation des fichiers des rôles AD DS et DNS

Cette première phase permet de copier les dossiers et les outils d'administration nécessaires pour l'Active Directory et le DNS sur le disque local du serveur.

#### Étape 1 : Écran d'accueil de l'assistant
Dans le Gestionnaire de serveur, on clique sur "Ajouter des rôles et des fonctionnalités". L'assistant s'ouvre sur la page d'introduction "Before you begin". On clique sur Suivant.

![Avant de commencer](Ressources/AD1.png)

#### Étape 2 : Choix du type d'installation
Sur l'écran "Select installation type", on sélectionne la première option : "Installation basée sur un rôle ou une fonctionnalité".

![Type d'installation](Ressources/AD2.png)

#### Étape 3 : Sélection du serveur cible
Sur l'écran "Select destination server", on choisit notre serveur `SRVWIN01` dans la liste.

![Sélection du serveur](Ressources/AD3.png)

#### Étape 4 : Sélection des rôles à installer
Sur l'écran "Select server roles", on coche la case "Services de domaine Active Directory" et la case "Serveur DNS". On valide l'ajout des outils de gestion (RSAT) pour les deux rôles.

![Cochage des rôles AD DS et DNS](Ressources/AD4.png)

#### Étape 5 : Validation des fonctionnalités complémentaires
Sur l'écran "Select features", il n'y a rien à modifier. Les composants requis par Windows Server sont déjà pré-cochés. On clique sur Suivant.

![Validation des fonctionnalités](Ressources/AD5.png)

#### Étape 6 : Écran d'information du Serveur DNS
L'écran "DNS Server" affiche les informations spécifiques au rôle DNS et son intégration. On clique sur Suivant.

![Informations Serveur DNS](Ressources/AD6.png)

#### Étape 7 : Écran d'information AD DS
L'écran "AD DS" affiche les conseils et les remarques de Microsoft concernant les services d'annuaire. On clique sur Suivant.

![Informations AD DS](Ressources/AD7.png)

#### Étape 8 : Confirmation avant le lancement
Sur l'écran "Confirmation", on vérifie la liste complète des composants et des consoles qui vont être installés, puis on clique sur le bouton "Installer".

![Confirmation des rôles](Ressources/AD8.png)

#### Étape 9 : Progression de l'installation
La barre de progression avance pendant que Windows Server copie et installe les fichiers nécessaires aux deux rôles sur le disque.

![Installation en cours](Ressources/AD9.png)

#### Étape 10 : Fin de l'installation des composants
Une fois que l'installation des fichiers est terminée avec succès, on peut quitter cet assistant en cliquant sur le bouton "Close".

![Fin de l'installation](Ressources/AD10.png)

---

### b. Phase 2 : Assistant de Configuration et Promotion du Domaine

Cette deuxième phase permet de configurer la structure logique de notre annuaire Active Directory.

#### Étape 11 : Notification dans le Gestionnaire de serveur
De retour sur le tableau de bord principal du Gestionnaire de serveur, on remarque qu'un drapeau jaune de notification est apparu en haut à droite de l'interface.

![Drapeau jaune de notification](Ressources/AD11.png)

#### Étape 12 : Lancement de la promotion
On clique sur ce drapeau jaune pour ouvrir le menu des tâches post-déploiement, puis on clique sur le lien "Promouvoir ce serveur en contrôleur de domaine" (Promote this server to a domain controller).

![Lien de promotion du serveur](Ressources/AD12.png)

#### Étape 13 : Configuration du déploiement de la forêt
L'assistant de promotion s'ouvre sur l'écran "Deployment Configuration". Comme il s'agit du tout premier serveur de notre réseau, on coche "Ajouter une nouvelle forêt" (Add a new forest) et on saisit le nom obligatoire : `tssr.lan`.

![Configuration du déploiement](Ressources/AD13.png)

#### Étape 14 : Options du contrôleur de domaine et mot de passe DSRM
Sur l'écran "Domain Controller Options", on conserve le niveau fonctionnel par défaut. On s'assure que les cases DNS et Catalogue Global sont cochées, puis on écrit un mot de passe sécurisé pour le mode de restauration de secours (DSRM).

![Options du contrôleur de domaine](Ressources/AD14.png)

#### Étape 15 : Options DNS (Message d'avertissement)
L'écran "DNS Options" affiche un message concernant la délégation DNS. Cet avertissement est tout à fait normal lors de la création d'une nouvelle zone racine isolée. On clique sur Suivant.

![Options DNS](Ressources/AD15.png)

#### Étape 16 : Nom NetBIOS du domaine
Sur l'écran "Additional Options", le système examine notre saisie et attribue automatiquement le nom NetBIOS de notre domaine. On vérifie qu'il indique bien `TSSR` en majuscules.

![Nom NetBIOS du domaine](Ressources/AD16.png)

#### Étape 17 : Examen des options choisies
L'écran "Review Options" affiche un résumé complet de tous les choix et paramètres techniques que nous avons configurés avant de lancer définitivement l'écriture sur le serveur.

![Examen des options](Ressources/AD17.png)

#### Étape 18 : Vérification des prérequis et installation
Sur l'écran "Prerequisites Check", le système fait ses dernières vérifications de sécurité. Dès que l'icône de validation verte apparaît en haut pour confirmer la conformité, on clique sur le bouton "Installer". Le serveur configure le domaine puis redémarre automatiquement.

![Vérification des prérequis](Ressources/AD18.png)

---

### c. Phase 3 : Finalisation, Redémarrage et Validation

#### Étape 19 : Notification de redémarrage automatique
Une fois la configuration terminée, une boîte de dialogue Windows s'affiche pour indiquer que la machine va se déconnecter et redémarrer automatiquement pour appliquer les modifications de l'Active Directory.

![Notification de redémarrage de l'ordinateur](Ressources/AD19.png)

#### Étape 20 : Connexion au domaine d'entreprise
Après le redémarrage du serveur, on constate le succès de l'opération sur l'écran de verrouillage Windows qui propose désormais de se connecter avec le compte administrateur du domaine sous la forme `TSSR\Administrateur`.

![Connexion Windows sous l'identité TSSR\Administrateur](Ressources/AD20.png)

#### Étape 21 : Validation du Gestionnaire de serveur opérationnel
Une fois la session ouverte, le tableau de bord du Gestionnaire de serveur s'affiche. On valide que les briques technologiques "AD DS" et "DNS" sont bien présentes, actives et totalement fonctionnelles dans le menu latéral gauche.

![Tableau de bord fonctionnel](Ressources/AD21.png)


## 2. Domaine AD DS

### Organisation AD DS

Cette section détaille la structuration de l'annuaire Active Directory pour la société EcoTech Solutions, comprenant la création des Unités d'Organisation (OU), des comptes utilisateurs de test et l'implémentation de la stratégie de groupes AGDLP.

#### Étape 1 : OU (Unités d'Organisation)

Afin d'isoler les objets de production et de permettre l'application ciblée des futures stratégies de groupe (GPO), une structure d'OU hiérarchique a été mise en place.

1. Dans le **Gestionnaire de serveur**, ouvrir **Outils** > **Utilisateurs et ordinateurs Active Directory**.
2. Faire un clic droit sur la racine du domaine `tssr.lan` > **Nouveau** > **Unité d'organisation**.
3. Créer l'OU principale `EcoTechSolutions` en veillant à laisser cochée la protection contre la suppression accidentelle.
4. Sous l'OU `EcoTechSolutions`, créer les sous-OU correspondantes aux départements définis pour la phase de test :
   - `Service-Commercial`
   - `Service-RH`

#### Étape 2 : Groupes (Implémentation de l'AGDLP)

La stratégie AGDLP est appliquée pour sécuriser les futurs accès aux ressources.

1. **Création du Groupe Global (Fonction) :** Dans l'OU `Service-Commercial`, faire un clic droit > **Nouveau** > **Groupe**. Nommer le groupe `G_Service-Commercial_U` (Étendue : Globale / Type : Sécurité).
2. **Imbrication dans le Groupe Local de Domaine (Ressource) :** Un groupe local de domaine nommé `DL_Partage-Commercial_RW` est créé dans une OU dédiée aux ressources. Le groupe global `G_Service-Commercial_U` y est ensuite imbriqué comme membre.
3. Répéter l'opération pour le département `Service-RH` avec le groupe global `G_Service-RH_U`.

#### Étape 3 : Utilisateurs

Création de 6 utilisateurs répartis équitablement dans les deux départements pilotes, respectant la nomenclature officielle de l'entreprise.

1. Dans l'OU du département cible, faire un clic droit > **Nouveau** > **Utilisateur**.
2. Saisir les informations d'identité et définir le nom d'ouverture de session (SamAccountName) en minuscules, sans espace ni accent.
3. Définir un mot de passe provisoire et cocher obligatoirement la case **L'utilisateur doit changer le mot de passe à la prochaine ouverture de session** pour garantir la confidentialité des accès.
4. Intégrer les utilisateurs dans leurs groupes globaux respectifs (`G_Service-Commercial_U` ou `G_Service-RH_U`) selon leur affectation.


### GPO (Objets de Stratégie de Groupe)

Cette section détaille la mise en place des stratégies de sécurité et de restriction de l'environnement utilisateur. 

Conformément à l'ordre d'application LSDOU (Local, Site, Domaine, OU), les stratégies affectant l'authentification et les mots de passe des comptes Active Directory doivent impérativement être liées à la racine du domaine pour être effectives.

#### 1. Politique de mot de passe et verrouillage de compte

Cette stratégie (GPO) sécurise les accès en imposant des mots de passe robustes et en protégeant l'annuaire contre les attaques par force brute (bruteforce).

**Création et liaison de la stratégie :**
1. Ouvrir le **Server Manager**, puis cliquer sur **Tools** > **Group Policy Management**.
2. Dérouler l'arborescence jusqu'au domaine `tssr.lan`.
3. Faire un clic droit sur `tssr.lan` > **Create a GPO in this domain, and Link it here...**.
4. Nommer la stratégie : `GPO_Domaine_Securite-Comptes` et valider.

**Configuration de la politique de mots de passe :**
1. Faire un clic droit sur la nouvelle GPO > **Edit...**.
2. Naviguer dans l'arborescence suivante : 
   `Computer Configuration` > `Policies` > `Windows Settings` > `Security Settings` > `Account Policies` > `Password Policy`.
3. Paramétrer les éléments suivants :
   - **Password must meet complexity requirements** : Cochez *Enabled* (Exige un mélange de majuscules, minuscules, chiffres et caractères spéciaux).
   - **Minimum password length** : Définir sur *12 characters* (Norme minimale de sécurité recommandée).
   - **Enforce password history** : Définir sur *5 passwords remembered* (Empêche la réutilisation immédiate des anciens mots de passe).

**Configuration du verrouillage de compte (Anti-Bruteforce) :**
1. Toujours dans la section `Account Policies`, sélectionner le sous-dossier **Account Lockout Policy**.
2. Paramétrer l'élément suivant :
   - **Account lockout threshold** : Définir sur *3 invalid logon attempts* (Verrouille le compte après 3 échecs consécutifs).
3. Valider la fenêtre d'avertissement de Windows : le système configure automatiquement les paramètres liés (`Account lockout duration` et `Reset account lockout counter after`) sur 10 minutes par défaut.
4. Fermer l'éditeur (la sauvegarde s'applique automatiquement).
