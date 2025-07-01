Installation de GLPI sur une VM Debian 11.6 CPU : 1 RAM : 2 Go Stockage : 20 Go

Comptes : root ou bootb
Mots de passe : Azerty1*

### Pré-requis
Carte réseau par pont
Installer le système d'exploitation
Mise a jours sytème
Installer sudo
Ajouter l'utilisateur dans le groupe sudo



### 1. Installation SSH 


- Installation

        apt install openssh-server

- Modification du fichier de configuration SSH pour authoriser la connexion par clé publique

        nano /etc/ssh/sshd_config --> Décommmenter Pubkeyauthentication yes
    
- Génération d'un paire de clés SSH depuis le client

        ssh-keygen -t ed25519

- Copie de la clé publique sur le serveur

        ssh-copy-id bootb@<ip_de_la_vm>

- Connexion sur la VM Debian en SSH depuis le client

        ssh bootb@<ip_de_la_vm>

- Relancer le serveur


### 2. Apache

- Installation

        sudo apt install apache2 -y

- Activation au démarage :


        sudo systemctl enable apache2


### 3. Maria DB

- Installation de la base de donnée

        sudo apt install mariadb-server -y


### 4. PHP

- Installation des dépendances

        sudo apt install ca-certificates apt-transport-https software-properties-common lsb-release curl lsb-release -y

- Ajout du dépôt pour PHP 8.1

 
        su -
        wget -qO /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg

         echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php.list

        curl -sSL https://packages.sury.org/php/README.txt | bash -
            
        sudo apt update && apt upgrade -y
 
- Installation de PHP 8.1

        sudo apt install php8.1 -y

- Installation des modules annexes

        sudo apt install php8.1 libapache2-mod-php -y

        sudo apt install php8.1-{ldap,imap,apcu,xmlrpc,curl,common,gd,mbstring,mysql,xml,intl,zip,bz2} -y


### 5.Maria DB

- Installation

        sudo mysql_secure_installation


Répondre Y à toutes les questions sauf "Change the root password?"

- Configuration de la base de données

        sudo mysql -u root -p

- Dans la configuration de Mariadb (nom de la BDD : glpidb)
 

        create database glpidb character set utf8 collate utf8_bin;

  -Compte d accès à la BDD glpidb : glpi

  -Mot de passe du compte glpi : Azerty1*

        grant all privileges on glpidb.* to glpi@localhost identified by "Azerty1*";

        flush privileges;

        quit
    

### 6. Téléchargement des ressources GLPI sur GitHub

        wget https://github.com/glpi-project/glpi/releases/download/10.0.15/glpi-10.0.15.tgz

- Création du dossier pour glpi

        sudo mkdir /var/www/html/glpi.lab

- Décompression de l'archive

        tar -xzvf glpi-10.0.15.tgz

- Copie du dossier décompréssé vers le nouveau dossier crée

        sudo cp -R glpi/* /var/www/html/glpi.lab


- Suppression du fichier index.php dans /var/www/html

        sudo rm /var/www/html/index.html

- Droits d'accès aux fichiers

        sudo chown -R www-data:www-data /var/www/html/glpi.lab

        sudo chmod -R 775 /var/www/html/glpi.lab

### 7. Configuration de PHP

- Edition du fichier /etc/php/8.1/apache2/php.ini

        sudo nano -l /etc/php/8.1/apache2/php.ini

    Modification/vérification des paramètres : (recherches avec ctrl+w)

        memory_limit = 64M # <= à changer ligne 435

        file_uploads = on

        max_execution_time = 600 # <= à changer ligne 410

        session.auto_start = 0

        session.use_trans_sid = 0

        Redémarrer le serveur.

### 8. Connexion à l'interface Web 

- A partir d'une machine graphique, sur un navigateur web : 

        http://<Adresse_IP_du_serveur_GLPI>/

- Sur la page d'installation :

    -	Langue : Français
    -	Cliquer sur Installer

- Configuration de la connexion à la base de données :

    -	Serveur SQL : 127.0.0.1
    -	Utilisateur : glpi
    -	Mot de passe : Azerty1*

- Choisir la base de données crée: glpidb

     Les identifiants et mots de passe par défaut sont :

    - glpi/glpi pour le compte administrateur

    - tech/tech pour le compte technicien

    - normal/normal pour le compte normal

    - post-only/postonly pour le compte postonly

- Se connecter et modifier le mot de passe administrateur

Fin de l'installation.