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

**sudo** permet aux utilisateurs non root d'utiliser des commandes admins (par ex: _sudo adduser new_user_). Pour que ça marche il faut que l'utilisateur non root soit ajouté dans la liste des utilisateurs sudo.

**net-tools** contient les commandes réseaux tel que _netstat_ pour lister la table de routage.

## Nouvel utilisateur et droits admin
  1) Créer un nouvel utilisateur:
  
    sudo adduser <new_user>
    
  Lorsqu'un nouvel utilisateur est créé, un dossier à son nom est créé dans _/home/<new_user>_. La liste des utilisateurs existants se trouve dans _/etc/passwd_.
  
  2) Ajouter le dans la liste des utilisateurs sudo:
  
    sudo adduser <new_user> sudo
  
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

  L'adresse qu'on affiche avec _ip addr_ correspond à notre adresse privée; c'est le chemin entre la VM et le réseau (privée) de l'école. Elle est attribuée automatiquement par le service DHCP du réseau de l'école.
  
  On va configurer la VM pour fixer son adresse IP et lui attribuer un netmask de 30:
  
    sudo vim /etc/network/interfaces
    
  Dans le fichier, on modifie:
    
    allow-hotplug enp0s3
    face enp0s3 inet dhcp
    
  Par:
  
    auto enp0s3

  Puis on crée le fichier enp0s3:
  
    sudo vim /etc/network/interfaces.d/enp0s3
    
  Dans lequel on écrit:
  
    iface enp0s3 inet static
            address 10.11.200.90
            netmask 255.255.255.252
            gateway 10.11.254.254

  Pour l'adresse IP, on met l'IP qu'on obtient avec la commande _ip addr_. De même pour la **gateway** avec _netstat -nr_ sur notre machine réelle. La **gateway** est l'intermédiaire entre 2 réseaux, ici le réseau local de 42 et Internet.

  Puis on relance le service réseau de VM:
  
    service networking restart
    
  On peut vérifier le changement avec _ip addr_ et notre adress IP devrait être _10.11.200.90/30_.
  
## Service SSH
  SSH est un service de connexion à distance d'une machine à une autre. Par défaut ce service se fait à travers le **port 22**. Pour le modifier:
  
    sudo vim /etc/ssh/sshd_config
  
  On décommente et on change le numéro de port:
  
    Port 55555
    
  On relance le service SSH de la VM:
  
    service ssh restart
    
  À partir de là, on peut se connecter à notre VM avec (dans un terminal de notre machine réelle):
  
    ssh root@10.11.200.90 -p 55555
  
  On ouvre un 2ème terminal et dans ~/.ssh on génère une paire de clé SSH:
  
    ssh-keygen
    
  On copie la clé publique générée et on la donne:
  
    ssh-copy-id -i ~/.ssh/id_rsa.pub <user>@10.11.200.233 -p 55555
  
  Dans la VM, on active l'authentification par publickeys:
    
    PubkeyAuthentication yes
    
  On désactive l'authentification par mot de passe et l'authentification de l'utilisateur _root_:
  
    PasswordAuthentication no  
    PermitRootLogin no
    
## Firewall
   
    
    
  
  
  
  
    
  
