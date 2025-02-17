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
[hugo@efrei-xmg4agau1 ~]$ sudo firewall-cmd --state
running
```
