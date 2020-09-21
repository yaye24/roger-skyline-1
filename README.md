# Roger-skyline-1
Sujet d’initiation à l’Administration Système et Réseau.

## Sommaire

## Introduction

  Le but est de s'initier aux bases l'administration système et réseau. Pour ça on va créer notre serveur à partir de 0.

  Le sujet nous demande d'utiliser Linux, un **système d'exploitation** (OS ou Operating System en anglais) au même titre que Windows ou Mac OS utilisé à 42. Comme c'est interdit de bidouiller les ordis pour changer l'OS, on passe par l'installation d'une **machine virtuelle** (VM ou Virtual Machine en anglais). Une machine virtuelle fonctionne comme une machine réelle (exemple: un ordinateur) mais n'existe pas physiquement, ça ressemble aux émulateurs de consoles pour ceux qui connaissent, mais là ce serait un émulateur d'OS. L'intérêt c'est de pouvoir choisir l'OS qu'on veut en plus de pouvoir travailler avec un environnement vierge.

## Création de la VM

  On utilise Virtual Box pour créer notre VM (la machine) et on va installer dessus la version **Debian (64-bits)** de Linux (l'OS).
  
  ![alt text](https://upload.wikimedia.org/wikipedia/commons/d/d5/Virtualbox_logo.png)

  1) Ouvrir Virtual Box, cliquer sur New

  2) Choisir le <nom_de_la_machine>, dossier où sera installé la VM = /sgoinfre/goinfre/Perso/<login>
  
     Type = Linux et Version = Debian (64-bits)
  
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
     
     L'image disque c'est comme un disque d'installation Debian, mais version virtuelle et comme notre VM, il n'existe pas physiquement.
     
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
  
     Linux est maintenant installé sur notre VM. Entrer Login = root et Password = toor.
     
Installer sudo avec:
     
    apt install sudo

Sudo permet aux utilisateurs non root d'utiliser des commandes admins (par ex: sudo adduser new_user). Pour que ça marche il faut que l'utilisateur non root soit ajouté dans la liste des utilisateurs sudo.
     
 ## Partie Réseau et Sécurité
 ### Utilisateur et droits admins
  Créer un nouvel utilisateur:
  
    adduser <new_user>
    
  Lorsqu'un nouvel utilisateur est créé, un dossier à son nom est créé dans /home/<new_user>
    
  Ajouter le dans la liste des utilisateurs sudo:
  
    adduser <new_user> sudo
    
  _**REMARQUE**_: pour supprimer un utilisateur ET son dossier de /home, on utilise: _deluser --remove-home_
  
  
    
  
