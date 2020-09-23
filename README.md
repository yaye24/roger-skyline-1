# Roger-skyline-1
Sujet d’initiation à l’Administration Système et Réseau.

## Introduction

  Le but est de s'initier aux bases l'administration système et réseau. Pour ça on va créer notre serveur à partir de 0.

  Le sujet nous demande d'utiliser Linux, un **système d'exploitation** (OS ou Operating System en anglais) au même titre que Windows ou Mac OS utilisé à 42. Comme c'est interdit de bidouiller les ordis pour changer l'OS, on passe par l'installation d'une **machine virtuelle** (VM ou Virtual Machine en anglais). Une machine virtuelle fonctionne comme une machine réelle (exemple: un ordinateur) mais n'existe pas physiquement, ça ressemble aux émulateurs de consoles pour ceux qui connaissent, mais là ce serait un émulateur d'OS. L'intérêt c'est de pouvoir choisir l'OS qu'on veut en plus de pouvoir travailler avec un environnement vierge.

## Création de la VM

  On utilise Virtual Box pour créer notre VM (la machine) et on va installer dessus la version **Debian (64-bits)** de Linux (l'OS).
  
  ![alt text](https://upload.wikimedia.org/wikipedia/commons/d/d5/Virtualbox_logo.png)

  1) Ouvrir Virtual Box, cliquer sur New

  2) Choisir le <nom_de_la_machine>
  
     Le dossier où sera installé la VM = /sgoinfre/goinfre/Perso/<login>
  
     Type = Linux
     
     Version = Debian (64-bits)
  
  3) Memory size = 1024 MB
     
     Create a virtual hard disk now
     
     VDI (VirtualBox Disk Image)
     
     Dynamically allocated
     
     Virtual hard disk size = 7.46GB (En fait ce ne sont pas des GB mais des GiB et 7.46GiB ~ 8.00GB).
     
     Voilà, notre VM existe virtuellement mais avant de la lancer on va régler les configs.
  
  4) Clique droit sur <nom_de_la_machine> puis Settings.
  
     Onglet _System_, décocher _Floppy_ et le mettre en dessous de _Optical_ et _Hard Disk_ (cette manip' permet de ne pas à avoir à réinstaller Debian à chaque fois qu'on démarre notre VM.
     
     Onglet _Display_, _Scale Factor_ à 150% (pour avoir un écran plus grand).
     
     Onglet _Storage_, cliquer sur _Empty_ puis à droite le petit rond bleu et _Choose Virtual Optical Disk File_.
     
     Donner le chemin vers l'**image disque** (.iso) de Debian 64-bits qu'on télécharge ici: https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-10.5.0-amd64-netinst.iso
     
     L'**image disque** c'est comme un disque d'installation Debian, mais version virtuelle et comme notre VM, il n'existe pas physiquement.
     
     Onglet _Network_, Attached to = Bridged Adapter (c'est pour avoir accès à Internet sur notre VM grace à notre machine réelle).
  
     Les réglages c'est ok, on peut maintenant lancer la VM avec _Start_

  5) Graphical install
  
     Langue, Location, Keyboard
     
     Hostname = <nom_de_serveur>
     
     Domain name = <nom_de_serveur>.com
     
     Root password = toor (Root c'est le nom de l'utilisateur suprême, il a tous les droits)
     
     Entrer <nom_user> avec son <mdp_user>
     
     Partitioning method = Manual
     
     SCSI2 (...) -> Create new empty partition
     
     FREE SPACE -> Create a new partition -> 4.2 GB -> Primary -> Beginning -> Done setting up the partition
     
     FREE SPACE -> Create a new partition -> 2.8 GB -> Logical -> End -> Done setting up the partition
     
     FREE SPACE -> Create a new partition -> 1.0 GB -> Logical -> End -> Use as = Swap area -> Done setting up the partition
     
     Finish et on laisse blanc/par défaut jusqu'à _Install the GRUB boot loader_ -> Yes -> /dev/sda
  
Linux est maintenant installé sur notre VM. On y accède depuis VirtualBox avec un double-clic sur <nom_de_la_machine>.

## Les packages
On installe les packages suivants:
     
    sudo apt install sudo vim net-tools

**sudo** permet aux utilisateurs non root d'utiliser des commandes admins (par ex: `sudo adduser new_user`). Pour que ça marche il faut que l'utilisateur non root soit ajouté dans la liste des utilisateurs sudo.

**net-tools** contient les commandes réseaux tel que `netstat` pour lister la table de routage.

## Nouvel utilisateur et droits admin
  1) Créer un nouvel utilisateur:
  
    sudo adduser nom_utilisateur
    
  Lorsqu'un nouvel utilisateur est créé, un dossier à son nom est créé dans _/home/nom_utilisateur_. La liste des utilisateurs existants se trouve dans _/etc/passwd_.
  
  2) Ajouter le dans la liste des utilisateurs sudo:
  
    sudo adduser nom_utilisateur sudo
  
  On peut aussi l'ajouter directement dans le fichier _/etc/sudoers_ avec vim.

  _**REMARQUE**_: pour supprimer un utilisateur ET son dossier de _/home_, on utilise: _deluser --remove-home_
  
## Adresse IP et Netmask
  L'**adresse IP** est une combinaison unique de 4 octets qui représente le chemin qui relie notre machine (ici c'est la VM) à un réseau.
  Pour voir son **adresse IP**:
  
    ip addr

  Il devrait être de la forme _10.11.X.X/Y_ avec _Y_ étant la longueur du **netmask**.
  
  Le **netmask** est une combinaison de 4 octets avec la particularité de s'écrire avec une succession de 1 puis de 0 (ex: 11111111.11111000.00000000.00000000 == 255.248.0.0). Il délimite l'adresse réseau contenue dans l'adresse IP en s'appliquant comme un masque sur cette dernière.
    
    00001010.00001011.11001000.01011010 -> (10.11.200.90) Adresse IP d'une machine de 42
    11111111.11111111.00000000.00000000 -> (255.255.0.0) Netmask de 42
    00001010.00001011.00000000.00000000 => (10.11.0.0) Adresse réseau de 42
  
  Comme c'est une succession de 1 puis de 0, on peut définir un netmask par sa longueur; càd le nombre de 1 qu'il y a avant qu'il n'y ait que des 0). Cette longueur varie de 0 à 32 (4 octets * 8 bits).

  L'adresse qu'on affiche avec `ip addr` correspond à notre adresse privée; c'est le chemin entre la VM et le réseau (privée) de l'école. Elle est attribuée automatiquement par le service DHCP du réseau de l'école.
  
  1) On va configurer la VM pour fixer son adresse IP et lui attribuer un netmask de 30:
  
    sudo vim /etc/network/interfaces
    
  2) Dans ce fichier `/etc/network/interfaces`, on modifie:
    
    allow-hotplug enp0s3
    face enp0s3 inet dhcp
    
  Par:
  
    auto enp0s3

  3) Puis on crée le fichier enp0s3:
  
    sudo vim /etc/network/interfaces.d/enp0s3
    
  4) Dans lequel on écrit:
  
    iface enp0s3 inet static
            address 10.11.200.90
            netmask 255.255.255.252
            gateway 10.11.254.254

  Pour l'adresse IP, on met l'IP qu'on obtient avec la commande `ip addr`. De même pour la **gateway** avec `netstat -nr` sur notre machine réelle. La **gateway** est l'intermédiaire entre 2 réseaux, ici le réseau local de 42 et Internet.

  5) Enfin on relance le service réseau de VM:
  
    service networking restart
    
  On peut vérifier le changement avec _ip addr_ et notre adress IP devrait être _10.11.200.90/30_.
  
## Service SSH
  1) SSH est un service de connexion à distance d'une machine à une autre. Par défaut ce service se fait à travers le **port 22**. Pour le modifier:
  
    sudo vim /etc/ssh/sshd_config
  
  On décommente et on change le numéro de port (libre au choix mais il faut rester cohérent pour la suite):
  
    Port 55555
    
  2) On relance le service SSH de la VM:
  
    service ssh restart
    
  3) On se connecte à notre VM avec (à partir d'un terminal de notre machine réelle):
  
    ssh nom_utilisateur@10.11.200.90 -p 55555
    
  Il faut maintenant configurer la connexion SSH par clé et interdire la connexion en tant que _root_.
  
  4) On ouvre un 2ème terminal et dans ~/.ssh on génère une paire de clé SSH:
  
    ssh-keygen
    
  5) On copie la clé publique générée et on la donne:
  
    ssh-copy-id -i ~/.ssh/id_rsa.pub <user>@10.11.200.233 -p 55555
  
  6) Sur la VM, dans `/etc/ssh/sshd_config`, on active l'authentification par publickeys:
    
    PubkeyAuthentication yes
    
  On désactive l'authentification par mot de passe et l'authentification de l'utilisateur _root_:
  
    PasswordAuthentication no  
    PermitRootLogin no
    
  Enfin on `service ssh restart` pour confirmer les changements.
    
## Firewall
  Le **firewall** est un ensemble de règles qui va controller le trafic entrant ou sortant de la machine sur laquelle il est mis en place. L'intérêt principal étant de protéger la machine, il peut être aussi utilisé pour le NAT ou ajustement de bande passante. _(source: https://wiki.debian.org/DebianFirewall)_

  Ces règles sont définies par la commande `iptables`. Son utilisation n'est pas facile si on n'est pas familiarisé avec, donc on va utiliser la commande `ufw`.

  1) `ufw` s'utilise avec une syntax plus naïve et réalise les commandes `iptables` "à notre place".

    sudo apt install ufw
    
  2) On l'active:
  
    ufw enable
    
  3) On initialise (interdiction d'entrer, libre de sortir):
  
    ufw default deny incoming
    ufw default allow outgoing
    
  4) On autorise la connexion SSH (ici, port 55555):
  
    ufw allow 55555
    
  5) On restart:
  
    service ufw restart
    
## Se protéger des attaques DoS (Denial of Service)
  Le **DoS** est une attaque qui a pour but de ralentir ou mettre hors service une machine ou un réseau. Ça se fait en noyant sa cible de requêtes, celle-ci ralentit à cause du trop grand nombre de requêtes jusqu'à crash.
  
  1) `fail2ban` va se charger de détecter ces attaques:
    
    sudo apt install fail2ban
    
  2) Ouvrir `/etc/fail2ban/jail.d/defaults-debian.conf`:
    
    [sshd]
    enabled = true
    bantime = 60
    findtime = 600
    maxretry = 5
    
  3) On restart:
  
    service fail2ban restart
    
## Se protéger des scans de ports
    

    
    
  
  
  
  
    
  
