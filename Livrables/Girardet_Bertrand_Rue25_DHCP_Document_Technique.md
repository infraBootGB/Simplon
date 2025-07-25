
### 1. Installer une machine virtuelle avec le système d’exploitation Windows

- Création d'une VM serveur Windows 2022 à partir d'une image ISO, dans virtual box
    - Ressources : 4Go Ram , 2 processeurs , Disque 50Go
    - Une carte réseau en réseau interne (intnet), une carte réseau en NAT pour l'accès internet
    - Attribution d'une adresse IP statique : 172.16.0.1/24 sur la carte en réseau interne
    - Mises a jours du serveur
    - Création d"un client Windows 10 pour les tests

### 2. Installer le rôle ADDS, le nom de domaine : rue25.com

- Via le serveur manager, ajout des rôles :
    - Active Directory Domain Services
    - DNS Server

- Promotion du serveur en contrôleur de domaine
    - Création du nom de domaine : rue25.com
    - Redémarrage du serveur et connexion en administrateur
    - Configuration des enregistrements: 
        - A et AAAA sont déjà présents
        - Configuration de la zone de résolution inverse IPv4
    


- Vérification du service DNS : le serveur doit pouvoir résoudre son nom de domaine

            nslookup rue25.com


### 3. Installer le rôle DHCP et le configurer pour l’attribution des adresses IP automatiquement

- Via le Server Manager : installation du rôle DHCP et configuration post-installation
- Via la console DHCP création d'un scope "rue25" avec une plage IP de 172.16.0.2 à 172.16.0.200 avec un masque /24 (suppression de l'adresse virtual box pour le DHCP).
- Intégration du client de test au domaine et vérification de l'attribution de l'adresse IP via DHCP.


### 4. Créer les utilisateurs ainsi que les UOs et les groupes de sécurité.Puis

Via la console Users and Computers
- Création d'une OU utilisateurs
- création d'une OU ordinateurs
- création d'un OU rue25-sécurite pour les groupes de sécurité
- création des utilisateurs (logon name format Prénom.Nom)
- création des groupes de sécurité :
    - Service_Direction
    - Service_Consultants
    - Service_Commerciaux
    - Service_Comptables
    - Directrice  (La directrice est présent également dans le groupe Direction )
- Intégration des utilisateurs dans leur groupe respectifs

### 5. optionnellement, un dossier partagé par service, autorisations d’accès applicables aux groupes de sécurité de l’AD

- Création des dossier : Direction, Consultants, Commerciaux, Comptables
- Enregistrement des groupes de sécurité ayant accès aux dossiers partagés via l'onglet Share
- Enregistrement des permissions NTFS pour les groupes via l'onglet sécurité
- Test du fonctionnement du partage depuis le client de test avec le compte de la directrice

