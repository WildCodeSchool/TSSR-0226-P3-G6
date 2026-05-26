## 2. Installation et Configuration de l'Active Directory (AD DS) et du DNS

Cette section détaille pas à pas l'installation du rôle AD DS sur notre serveur `SRVWIN01`, sa promotion pour créer le domaine `tssr.lan`, ainsi que la configuration de la zone DNS inversée.

### a. Phase 1 : Installation du rôle AD DS

Cette étape permet de copier les fichiers nécessaires pour l'Active Directory sur le serveur.

#### Étape 1 : Choix du type d'installation
Dans le Gestionnaire de serveur, on clique sur "Ajouter des rôles et des fonctionnalités". Sur le premier écran, on sélectionne "Installation basée sur un rôle ou une fonctionnalité".
![Type d'installation](Ressources/images/AD1.png)

#### Étape 2 : Sélection du serveur cible
On choisit notre serveur `SRVWIN01` dans la liste. On vérifie bien qu'il affiche la bonne adresse IP statique du réseau LAN.
![Sélection du serveur](Ressources/images/AD1.png)

#### Étape 3 : Sélection du rôle à installer
Dans la liste des rôles, on coche la case "Services de domaine Active Directory". Une fenêtre s'ouvre pour ajouter les outils de gestion nécessaires, on clique sur "Ajouter des fonctionnalités".
![Cochage du rôle AD DS](ressources/images/ad/AD3.png)

#### Étape 4 : Validation des fonctionnalités
Cet écran permet d'ajouter des fonctionnalités supplémentaires. Il n'y a rien à cocher de plus, les outils requis sont déjà sélectionnés par défaut. On clique sur Suivant.
![Validation des fonctionnalités](ressources/images/ad/AD4.png)

#### Étape 5 : Informations sur le rôle
Windows affiche un écran d'information qui explique brièvement le rôle de l'Active Directory. On clique simplement sur Suivant.
![Informations AD DS](ressources/images/ad/AD5.png)

#### Étape 6 : Confirmation de l'installation
On vérifie le résumé des éléments qui vont être ajoutés sur le serveur, puis on clique sur le bouton "Installer".
![Confirmation du rôle](ressources/images/ad/AD6.png)

#### Étape 7 : Fin de l'installation et lien de promotion
Une fois la barre de progression terminée, l'installation des fichiers est réussie. On clique sur le lien bleu "Promouvoir ce serveur en contrôleur de domaine" pour lancer la configuration du domaine.
![Progression et lien de promotion](ressources/images/ad/AD7.png)

---

### b. Phase 2 : Configuration et Promotion en Contrôleur de Domaine

Cette phase permet de configurer le domaine de l'entreprise EcoTech Solutions.

#### Étape 8 : Création de la nouvelle forêt
Dans l'assistant qui s'ouvre, on sélectionne l'option "Ajouter une nouvelle forêt" car c'est le premier serveur de notre réseau. Dans la case "Nom de domaine racine", on tape le nom demandé : `tssr.lan`.
![Création de la forêt](ressources/images/ad/AD8.png)

#### Étape 9 : Options et mot de passe de secours (DSRM)
On laisse le niveau fonctionnel par défaut (Windows Server 2016). Les cases "Serveur DNS" et "Catalogue global" restent cochées. On saisit un mot de passe sécurisé pour le mode de secours (DSRM), qui servira si l'annuaire a un problème un jour.
![Options et mot de passe DSRM](ressources/images/ad/AD9.png)

#### Étape 10 : Redémarrage et vérification du Gestionnaire de serveur
L'assistant termine sa configuration et le serveur redémarre automatiquement. Après s'être connecté, on ouvre le Gestionnaire de serveur et on constate que les services AD DS et DNS apparaissent bien dans le tableau de bord à gauche.
![Vérification après redémarrage](ressources/images/ad/AD10.png)

---

### c. Phase 3 : Configuration du Serveur DNS (Zone de recherche inversée)

Le rôle DNS a été installé automatiquement avec l'Active Directory. Nous devons maintenant créer la zone inversée pour l'adresse IP de notre réseau LAN.

#### Étape 11 : Ouverture de la console DNS
Dans les Outils du Gestionnaire de serveur, on ouvre la console "DNS". On peut voir que la zone de recherche directe `tssr.lan` est bien active.
![Console DNS](ressources/images/ad/AD11)

#### Étape 12 : Lancement de l'assistant de nouvelle zone
On fait un clic droit sur le dossier "Zones de recherche inversée" et on choisit "Nouvelle zone..." pour démarrer l'assistant de configuration.
![Nouvelle zone inversée](ressources/images/ad/AD12.png)

#### Étape 13 : Choix du type de zone
On sélectionne l'option "Zone principale" et on s'assure que la case "Enregistrer la zone dans Active Directory" est bien cochée pour plus de sécurité et une réplication automatique.
![Type de zone principale](ressources/images/ad/AD13.png)

#### Étape 14 : Zone de réplication
On choisit l'option "Vers tous les serveurs DNS s'exécutant sur des contrôleurs de domaine dans ce domaine" pour que la zone se synchronise correctement sur nos futurs serveurs redondants.
![Portée de réplication](ressources/images/ad/AD13.png)

#### Étape 15 : Choix de la zone IPv4
On sélectionne "Zone de recherche inversée IPv4" puis on clique sur Suivant.
![Choix IPv4](ressources/images/ad/AD14.png)

#### Étape 16 : Saisie de l'ID réseau
Dans la case "ID réseau", on tape les trois premiers octets de notre plan d'adressage LAN, c'est-à-dire : `172.16.20`.
![Saisie ID Réseau](ressources/images/ad/AD15.png)

#### Étape 17 : Mises à jour dynamiques
On sélectionne "Autoriser uniquement les mises à jour dynamiques sécurisées". C'est le paramètre obligatoire et recommandé pour un environnement Active Directory.
![Mises à jour sécurisées](ressources/images/ad/AD16.png)

#### Étape 18 : Fin de l'assistant DNS
L'assistant affiche le résumé complet des paramètres de notre nouvelle zone inversée. On clique sur "Terminer" pour finaliser la création.
![Résumé de la zone DNS](ressources/images/ad/AD17.png)

---

### d. Phase 4 : Validation du succès de l'installation

Cette dernière partie confirme que toute l'infrastructure Active Directory et DNS est bien en place et fonctionne correctement.

#### Étape 19 : Vérification de la zone inversée active
De retour dans le Gestionnaire DNS, on clique sur notre zone inversée `20.16.172.in-addr.arpa`. On vérifie visuellement qu'elle est bien créée et qu'elle contient les enregistrements de base du serveur (SOA et NS).
![Vérification zone DNS créée](ressources/images/ad/AD18.png)

#### Étape 20 : Vérification de l'annuaire du domaine
On ouvre la console "Utilisateurs et ordinateurs Active Directory" (`dsa.msc`). On vérifie que notre domaine `tssr.lan` est bien accessible en haut de l'arborescence et que l'on peut naviguer dans les dossiers par défaut.
![Ouverture de la console AD](ressources/images/ad/AD19.png)

#### Étape 21 : Validation du Contrôleur de Domaine
Dans cette même console, on clique sur le dossier "Domain Controllers". On valide le succès complet de l'opération en constatant que notre serveur `SRVWIN01` y est bien présent avec le catalogue global activé.
![Validation du contrôleur de domaine](ressources/images/ad/AD21.png)
