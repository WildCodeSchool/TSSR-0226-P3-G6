## Sommaire

* [1. Installation et Configuration du Serveur GLPI (Linux Debian)](#1-installation-et-configuration-du-serveur-glpi-linux-debian)
  * [a. Phase 1 : Préparation du système et installation de la pile LAMP](#a-phase-1--préparation-du-système-et-installation-de-la-pile-lamp)
    * [Étape 1 : Mise à jour du système d'exploitation](#étape-1--mise-à-jour-du-système-dexploitation)
    * [Étape 2 : Installation des rôles Apache, MariaDB et PHP 8.3](#étape-2--installation-des-rôles-apache-mariadb-et-php-83)
    * [Étape 3 : Configuration de la version PHP par défaut](#étape-3--configuration-de-la-version-php-par-défaut)
  * [b. Phase 2 : Création et configuration de la base de données](#b-phase-2--création-et-configuration-de-la-base-de-données)
    * [Étape 4 : Connexion au service MariaDB](#étape-4--connexion-au-service-mariadb)
    * [Étape 5 : Création de la base et de l'utilisateur dédié](#étape-5--création-de-la-base-et-de-lutilisateur-dédié)
  * [c. Phase 3 : Déploiement des fichiers GLPI](#c-phase-3--déploiement-des-fichiers-glpi)
    * [Étape 6 : Téléchargement de l'archive officielle](#étape-6--téléchargement-de-larchive-officielle)
    * [Étape 7 : Extraction dans le répertoire web](#étape-7--extraction-dans-le-répertoire-web)
    * [Étape 8 : Application des droits et permissions d'accès](#étape-8--application-des-droits-et-permissions-daccès)
  * [d. Phase 4 : Sécurisation d'Apache (VirtualHost et Routage)](#d-phase-4--sécurisation-dapache-virtualhost-et-routage)
    * [Étape 9 : Création du VirtualHost dédié à GLPI](#étape-9--création-du-virtualhost-dédié-à-glpi)
    * [Étape 10 : Création du fichier de réécriture (.htaccess)](#étape-10--création-du-fichier-de-réécriture-htaccess)
    * [Étape 11 : Activation du site et redémarrage du service](#étape-11--activation-du-site-et-redémarrage-du-service)
  * [e. Phase 5 : Assistant d'installation Web](#e-phase-5--assistant-dinstallation-web)
    * [Étape 12 : Lancement de l'interface web](#étape-12--lancement-de-linterface-web)
    * [Étape 13 : Vérification des prérequis système](#étape-13--vérification-des-prérequis-système)
    * [Étape 14 : Paramétrage de la connexion SQL](#étape-14--paramétrage-de-la-connexion-sql)
    * [Étape 15 : Initialisation et fin de l'installation](#étape-15--initialisation-et-fin-de-linstallation)
  * [f. Phase 6 : Post-installation et Sécurisation (Hardening)](#f-phase-6--post-installation-et-sécurisation-hardening)
    * [Étape 16 : Connexion au tableau de bord](#étape-16--connexion-au-tableau-de-bord)
    * [Étape 17 : Suppression du fichier d'installation](#étape-17--suppression-du-fichier-dinstallation)
    * [Étape 18 : Modification des mots de passe par défaut](#étape-18--modification-des-mots-de-passe-par-défaut)

## 1. Installation et Configuration du Serveur GLPI (Linux Debian)

### a. Phase 1 : Préparation du système et installation de la pile LAMP

Cette première phase permet de mettre en place le socle technique web (Linux, Apache, MariaDB, PHP) nécessaire au fonctionnement de GLPI. Toutes les commandes sont exécutées en tant qu'utilisateur `root`.

#### Étape 1 : Mise à jour du système d'exploitation
Avant toute installation, on s'assure que les dépôts et les paquets du système Debian sont à jour en exécutant la commande suivante :
```bash
apt update && apt upgrade -y
```

#### Étape 2 : Installation des rôles Apache, MariaDB et PHP 8.3
On installe le serveur web Apache, le moteur de base de données MariaDB, ainsi que PHP 8.3 et l'ensemble des modules requis par GLPI 10.
```bash
apt install -y apache2 mariadb-server php8.3 php8.3-curl php8.3-gd php8.3-intl php8.3-mbstring php8.3-mysqli php8.3-xml php8.3-xmlrpc php8.3-zip php8.3-bz2 php8.3-ldap php8.3-exif php8.3-opcache php8.3-sodium libapache2-mod-php8.3 git unzip sudo
```

#### Étape 3 : Configuration de la version PHP par défaut
En cas de présence de multiples versions de PHP sur le système, on force l'utilisation de PHP 8.3 pour la ligne de commande (CLI) afin d'assurer la compatibilité avec les scripts GLPI :
```bash
update-alternatives --set php /usr/bin/php8.3
```

---

### b. Phase 2 : Création et configuration de la base de données

GLPI nécessite une base de données relationnelle pour stocker son inventaire et ses tickets.

#### Étape 4 : Connexion au service MariaDB
On ouvre une invite de commande SQL en se connectant en tant qu'administrateur à la base de données locale :
```bash
mysql -u root -p
```

#### Étape 5 : Création de la base et de l'utilisateur dédié
On crée une base de données vierge nommée `glpidb`, un utilisateur `glpiuser`, et on lui attribue tous les droits sur cette base, ainsi que le droit de lecture sur les fuseaux horaires du serveur :
```sql
CREATE DATABASE glpidb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'glpiuser'@'localhost' IDENTIFIED BY 'Azerty1*';
GRANT ALL PRIVILEGES ON glpidb.* TO 'glpiuser'@'localhost';
GRANT SELECT ON mysql.time_zone_name TO 'glpiuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

### c. Phase 3 : Déploiement des fichiers GLPI

#### Étape 6 : Téléchargement de l'archive officielle
On se place dans un répertoire temporaire pour télécharger l'archive tarball officielle de la dernière version de GLPI 10 depuis GitHub :
```bash
cd /tmp
wget https://github.com/glpi-project/glpi/releases/download/10.0.15/glpi-10.0.15.tgz
```

#### Étape 7 : Extraction dans le répertoire web
On extrait le contenu de l'archive compressée directement dans le dossier racine classique des serveurs web Linux (`/var/www/html/`) :
```bash
tar -xvzf glpi-10.0.15.tgz -C /var/www/html/
```

#### Étape 8 : Application des droits et permissions d'accès
Il est crucial que le service web (représenté par l'utilisateur `www-data`) soit propriétaire des fichiers pour pouvoir y écrire. On définit également les permissions CHMOD standard (755 pour les dossiers, 644 pour les fichiers) :
```bash
chown -R www-data:www-data /var/www/html/glpi
find /var/www/html/glpi -type d -exec chmod 755 {} \;
find /var/www/html/glpi -type f -exec chmod 644 {} \;
```

---

### d. Phase 4 : Sécurisation d'Apache (VirtualHost et Routage)

Pour des raisons de sécurité, la racine web ne doit pas pointer sur le dossier complet de GLPI, mais **uniquement sur le sous-dossier `/public`** afin d'interdire l'accès aux fichiers de configuration.

#### Étape 9 : Création du VirtualHost dédié à GLPI
On crée un nouveau fichier de configuration Apache nommé `glpi.conf` :
```bash
nano /etc/apache2/sites-available/glpi.conf
```
On y insère la configuration suivante ciblant explicitement `/var/www/html/glpi/public` :
```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/glpi/public

    <Directory /var/www/html/glpi/public>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/glpi_error.log
    CustomLog ${APACHE_LOG_DIR}/glpi_access.log combined
</VirtualHost>
```

#### Étape 10 : Création du fichier de réécriture (.htaccess)
L'utilisation du dossier sécurisé `/public` nécessite que GLPI redirige ses URL internes. On crée manuellement le fichier de routage attendu par Apache :
```bash
nano /var/www/html/glpi/public/.htaccess
```
Contenu à y insérer :
```apache
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php [QSA,L]
```

#### Étape 11 : Activation du site et redémarrage du service
On désactive la page Apache par défaut, on active notre nouveau VirtualHost, on charge le module de réécriture, puis on redémarre le service pour appliquer l'architecture :
```bash
a2dissite 000-default.conf
a2ensite glpi.conf
a2enmod rewrite
systemctl restart apache2
```

---

### e. Phase 5 : Assistant d'installation Web

L'architecture serveur est prête. La suite de la configuration s'effectue graphiquement depuis un poste client via un navigateur web.

#### Étape 12 : Lancement de l'interface web
Sur un poste client du réseau, on ouvre le navigateur et on accède à l'adresse IP du serveur (`http://172.16.20.20`). L'assistant de configuration GLPI s'affiche. On sélectionne la langue et on accepte les termes de la licence. On clique sur le bouton **Installer**.

#### Étape 13 : Vérification des prérequis système
L'assistant lance un scan du serveur. Tous les voyants (versions de PHP, extensions requises, permissions, vérification de l'accès web sécurisé) doivent valider les tests de compatibilité. On clique sur "Continuer" en bas de page.

#### Étape 14 : Paramétrage de la connexion SQL
Sur l'écran de configuration de la base de données, on renseigne les identifiants créés lors de la Phase 2 :
* **Serveur SQL :** `localhost`
* **Utilisateur SQL :** `glpiuser`
* **Mot de passe SQL :** `Azerty1*`

#### Étape 15 : Initialisation et fin de l'installation
L'assistant détecte la base existante `glpidb`. On la sélectionne. GLPI procède à l'injection de ses tables dans la base de données. Un message confirme la réussite de l'installation et résume les comptes créés par défaut (notamment le compte administrateur `glpi` / `glpi`).

---

### f. Phase 6 : Post-installation et Sécurisation (Hardening)

#### Étape 16 : Connexion au tableau de bord
De retour sur la page d'accueil, on se connecte avec les identifiants par défaut `glpi`. Le tableau de bord s'affiche, présentant des alertes de sécurité dans un bandeau orange qu'il est impératif de corriger avant la mise en production.

#### Étape 17 : Suppression du fichier d'installation
Afin d'empêcher qu'un utilisateur malveillant ne relance l'assistant d'installation pour écraser la base de données, on retourne dans la console SSH du serveur (`SRVLX01`) et on supprime le script d'initialisation :
```bash
rm /var/www/html/glpi/install/install.php
```

#### Étape 18 : Modification des mots de passe par défaut
Depuis l'interface web de GLPI, on navigue dans **Administration** > **Utilisateurs**. Pour des raisons évidentes de sécurité, on sélectionne chaque compte d'usine (les comptes `glpi`, `tech`, `normal`, et `post-only`) pour leur attribuer un nouveau mot de passe robuste, ou on les désactive si on ne compte pas les utiliser. Le bandeau d'alerte disparaît alors, signifiant que le serveur GLPI est pleinement sécurisé et opérationnel.

