# TpLinuxRockyFev

Part I : Rocky install

1. Install instructions

2. Proofs

🌞 Prouvez que le schéma de partitionnement a bien été appliqué

```[hugo@efrei-xmg4agau1 ~]$ lsdsk
-bash: lsdsk: command not found
[hugo@efrei-xmg4agau1 ~]$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda           8:0    0   30G  0 disk
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0   21G  0 part
  ├─rl-root 253:0    0   10G  0 lvm  /
  ├─rl-swap 253:1    0    1G  0 lvm  [SWAP]
  ├─rl-var  253:2    0    5G  0 lvm  /var
  └─rl-home 253:3    0    5G  0 lvm  /home
sr0          11:0    1 1024M  0 rom
```

🌞 Mettre en évidence la ligne de configuration sudo qui concerne le groupe wheel
```
[hugo@efrei-xmg4agau1 ~]$ sudo cat /etc/sudoers | grep '%wheel'
%wheel  ALL=(ALL)       ALL
# %wheel        ALL=(ALL)       NOPASSWD: ALL
```

🌞 Prouvez que votre utilisateur est bien dans le groupe wheel
```
[hugo@efrei-xmg4agau1 ~]$ groups
hugo wheel
```

🌞 Prouvez que la langue configurée pour l'OS est bien l'anglais
```
[hugo@efrei-xmg4agau1 ~]$ echo $LANG
en_US.UTF-8
```

🌞 Prouvez que le firewall est déjà actif
```
[hugo@efrei-xmg4agau1 ~]$ sudo systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
     Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; pr>
     Active: active (running) since Mon 2025-02-17 08:04:26 EST; 22min ago
       Docs: man:firewalld(1)
   Main PID: 684 (firewalld)
      Tasks: 2 (limit: 11092)
     Memory: 45.5M
        CPU: 559ms
     CGroup: /system.slice/firewalld.service
             └─684 /usr/bin/python3 -s /usr/sbin/firewalld --nofork --nopid

Feb 17 08:04:25 localhost systemd[1]: Starting firewalld - dynamic firewall>
Feb 17 08:04:26 localhost systemd[1]: Started firewalld - dynamic firewall >
lines 1-13/13 (END)
```

Part II : Networking
Le réseau c'est la porte d'entrée pour toutes les autres machines. C'est le seul moyen d'être attaqué à distance.
Maîtriser au mieux le réseau d'une machine est donc primordial pour prétendre en renforcer la sécurité.


Part II : Networking


🌞 Attribuer l'adresse IP 10.1.1.11/24 à la VM


🌞 Attribuer le nom node1.tp1.b3 à la VM


🌞 Déterminer la liste des programmes qui écoutent sur un port TCP


🌞 Déterminer la liste des programmes qui écoutent sur un port UDP


🌞 Pour chacun des ports précédemment repérés...


🌞 Fermez tous les ports inutilement ouverts dans le firewall



🌞 Pour toutes les applications qui sont en écoute sur TOUTES les adresses IP


