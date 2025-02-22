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

1. Basic networking conf

 A Static IP

🌞 Attribuer l'adresse IP 10.1.1.11/24 à la VM
```
[hugo@efrei-xmg4agau1 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:b6:06:87 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 84718sec preferred_lft 84718sec
    inet6 fe80::a00:27ff:feb6:687/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:9e:b1:f3 brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.11/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe9e:b1f3/64 scope link
       valid_lft forever preferred_lft forever
```

B. Hostname

🌞 Attribuer le nom node1.tp1.b3 à la VM
```
[hugo@efrei-xmg4agau1 ~]$ sudo hostnamectl set-hostname node1.tp1.b3
[sudo] password for hugo:
[hugo@efrei-xmg4agau1 ~]$ hostnamectl status
 Static hostname: node1.tp1.b3
       Icon name: computer-vm
         Chassis: vm 🖴
      Machine ID: 940f3e6c47fa4a3e862f2be4588fb79d
         Boot ID: 79341a9a6b7540c6ab1b068aca00dfb4
  Virtualization: oracle
Operating System: Rocky Linux 9.5 (Blue Onyx)
     CPE OS Name: cpe:/o:rocky:rocky:9::baseos
          Kernel: Linux 5.14.0-503.23.2.el9_5.x86_64
    Architecture: x86-64
 Hardware Vendor: innotek GmbH
  Hardware Model: VirtualBox
Firmware Version: VirtualBox
```


2. Listening ports
   
🌞 Déterminer la liste des programmes qui écoutent sur un port TCP
```
[hugo@efrei-xmg4agau1 ~]$ sudo ss -tulnp | grep 'tcp'
tcp   LISTEN 0      128          0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=719,fd=3))
tcp   LISTEN 0      128             [::]:22           [::]:*    users:(("sshd",pid=719,fd=4))
```

🌞 Déterminer la liste des programmes qui écoutent sur un port UDP
```
[hugo@efrei-xmg4agau1 ~]$ sudo ss -tulnp | grep 'udp'
udp   UNCONN 0      0          127.0.0.1:323       0.0.0.0:*    users:(("chronyd",pid=687,fd=5))
udp   UNCONN 0      0              [::1]:323          [::]:*    users:(("chronyd",pid=687,fd=6))
```
3. Firewalling
   
🌞 Pour chacun des ports précédemment repérés...
```
[hugo@efrei-xmg4agau1 ~]$ sudo firewall-cmd --list-all
[sudo] password for hugo:
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

🌞 Fermez tous les ports inutilement ouverts dans le firewall
```
[hugo@efrei-xmg4agau1 ~]$ sudo firewall-cmd --remove-service cockpit
success
[hugo@efrei-xmg4agau1 ~]$ sudo firewall-cmd --remove-service dhcpv6-client
success
[hugo@efrei-xmg4agau1 ~]$ sudo firewall-cmd --remove-service ssh
success
[hugo@efrei-xmg4agau1 ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services:
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```


🌞 Pour toutes les applications qui sont en écoute sur TOUTES les adresses IP

dans /etc/ssh/sshd_config

```
ListenAddress 10.1.1.11
```

```
[hugo@efrei-xmg4agau1 ~]$ sudo ss -tulnp
Netid   State    Recv-Q   Send-Q       Local Address:Port       Peer Address:Port   Process
udp     UNCONN   0        0                127.0.0.1:323             0.0.0.0:*       users:(("chronyd",pid=687,fd=5))
udp     UNCONN   0        0                    [::1]:323                [::]:*       users:(("chronyd",pid=687,fd=6))
tcp     LISTEN   0        128              10.1.1.11:22              0.0.0.0:*       users:(("sshd",pid=1407,fd=3))

```


Part III : Storage is still disks in 2025

1. LVM

🌞 Afficher l'état actuel de LVM

```
[hugo@efrei-xmg4agau1 ~]$ sudo pvscan
[sudo] password for hugo:
  PV /dev/sda2   VG rl_efrei-xmg4agau1   lvm2 [19.00 GiB / 4.00 MiB free]
  Total: 1 [19.00 GiB] / in use: 1 [19.00 GiB] / in no VG: 0 [0   ]
[hugo@efrei-xmg4agau1 ~]$ sudo lvscan
  ACTIVE            '/dev/rl_efrei-xmg4agau1/home' [3.00 GiB] inherit
  ACTIVE            '/dev/rl_efrei-xmg4agau1/root' [10.00 GiB] inherit
  ACTIVE            '/dev/rl_efrei-xmg4agau1/var' [<5.00 GiB] inherit
  ACTIVE            '/dev/rl_efrei-xmg4agau1/swap' [1.00 GiB] inherit
[hugo@efrei-xmg4agau1 ~]$ sudo vgscan
  Found volume group "rl_efrei-xmg4agau1" using metadata type lvm2

```

```
[hugo@efrei-xmg4agau1 ~]$ df -Th
Filesystem                           Type      Size  Used Avail Use% Mounted on
devtmpfs                             devtmpfs  4.0M     0  4.0M   0% /dev
tmpfs                                tmpfs     888M     0  888M   0% /dev/shm
tmpfs                                tmpfs     355M  5.0M  350M   2% /run
/dev/mapper/rl_efrei--xmg4agau1-root ext4      9.8G  1.3G  8.0G  14% /
/dev/sda1                            xfs       957M  313M  645M  33% /boot
/dev/mapper/rl_efrei--xmg4agau1-var  ext4      4.9G  215M  4.4G   5% /var
/dev/mapper/rl_efrei--xmg4agau1-home ext4      2.9G   48K  2.8G   1% /home
tmpfs                                tmpfs     178M     0  178M   0% /run/user/1000

```

🌞 Déterminer le type de système de fichiers

```
[hugo@efrei-xmg4agau1 ~]$ sudo blkid /dev/mapper/rl_efrei--xmg4agau1-root
/dev/mapper/rl_efrei--xmg4agau1-root: UUID="60960449-2ca3-4c46-929e-87c426bf7852" TYPE="ext4"
[hugo@efrei-xmg4agau1 ~]$ sudo blkid /dev/mapper/rl_efrei--xmg4agau1-home
/dev/mapper/rl_efrei--xmg4agau1-home: UUID="479c7f2a-32f5-48fd-a868-3943a878d08a" TYPE="ext4"

```

2. HELP my partition is full
🌞 Remplissez votre partition /home

```
[hugo@efrei-xmg4agau1 ~]$ dd if=/dev/zero of=/home/bingo/bigfile bs=4M count=2500
dd: error writing '/home/hugo/bigfile': No space left on device
696+0 records in
695+0 records out
2916237312 bytes (2.9 GB, 2.7 GiB) copied, 17.6197 s, 166 MB/s

```
🌞 Constater que la partition est pleine

```
[hugo@efrei-xmg4agau1 ~]$ df -h
Filesystem                            Size  Used Avail Use% Mounted on
devtmpfs                              4.0M     0  4.0M   0% /dev
tmpfs                                 888M     0  888M   0% /dev/shm
tmpfs                                 355M  5.0M  350M   2% /run
/dev/mapper/rl_efrei--xmg4agau1-root  9.8G  1.3G  8.0G  14% /
/dev/sda1                             957M  313M  645M  33% /boot
/dev/mapper/rl_efrei--xmg4agau1-var   4.9G  215M  4.4G   5% /var
/dev/mapper/rl_efrei--xmg4agau1-home  2.9G  2.8G     0 100% /home
tmpfs                                 178M     0  178M   0% /run/user/1000

```
🌞 Agrandir la partition

```
[hugo@efrei-xmg4agau1 ~]$ sudo lvextend -L +10G /dev/rl_efrei-xmg4agau1/home
  Insufficient free space: 2560 extents needed, but only 1 available
[hugo@efrei-xmg4agau1 ~]$ sudo lvextend -l +100%FREE /dev/rl_efrei-xmg4agau1/home
  Size of logical volume rl_efrei-xmg4agau1/home changed from 3.00 GiB (768 extents) to 3.00 GiB (769 extents).
  Logical volume rl_efrei-xmg4agau1/home successfully resized.

```


```
[hugo@efrei-xmg4agau1 ~]$ sudo resize2fs /dev/rl_efrei-xmg4agau1/home
[sudo] password for hugo:
resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/rl_efrei-xmg4agau1/home is mounted on /home; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 1
The filesystem on /dev/rl_efrei-xmg4agau1/home is now 787456 (4k) blocks long.

[hugo@efrei-xmg4agau1 ~]$ df -h
Filesystem                            Size  Used Avail Use% Mounted on
devtmpfs                              4.0M     0  4.0M   0% /dev
tmpfs                                 888M     0  888M   0% /dev/shm
tmpfs                                 355M  5.0M  350M   2% /run
/dev/mapper/rl_efrei--xmg4agau1-root  9.8G  1.3G  8.0G  14% /
/dev/sda1                             957M  313M  645M  33% /boot
/dev/mapper/rl_efrei--xmg4agau1-var   4.9G  215M  4.4G   5% /var
/dev/mapper/rl_efrei--xmg4agau1-home  2.9G  2.8G  1.9M 100% /home
tmpfs                                 178M     0  178M   0% /run/user/1000

```
🌞 Remplissez votre partition /home
➜ Eteignez la VM et ajoutez lui un disque de 40G
🌞 Utiliser ce nouveau disque pour étendre la partition /home de 20G


```
[hugo@efrei-xmg4agau1 ~]$ sudo vgextend rl_efrei-xmg4agau1 /dev/sdb1
  WARNING: adding device /dev/sdb1 with idname t10.ATA_VBOX_HARDDISK_VBdd91d800-d692a3c3 which is already used for /dev/sdb.
  Volume group "rl_efrei-xmg4agau1" successfully extended
[hugo@efrei-xmg4agau1 ~]$ lsblk
NAME                         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                            8:0    0   20G  0 disk
├─sda1                         8:1    0 1021M  0 part /boot
└─sda2                         8:2    0   19G  0 part
  ├─rl_efrei--xmg4agau1-root 253:0    0   10G  0 lvm  /
  ├─rl_efrei--xmg4agau1-swap 253:1    0    1G  0 lvm  [SWAP]
  ├─rl_efrei--xmg4agau1-home 253:2    0    3G  0 lvm  /home
  └─rl_efrei--xmg4agau1-var  253:3    0    5G  0 lvm  /var
sdb                            8:16   0   40G  0 disk
└─sdb1                         8:17   0   20G  0 part
sr0                           11:0    1 1024M  0 rom
[hugo@efrei-xmg4agau1 ~]$ sudo lvextend -L +20G /dev/rl_efrei-xmg4agau1/home
  Insufficient free space: 5120 extents needed, but only 5119 available
[hugo@efrei-xmg4agau1 ~]$ sudo lvextend -L +19G /dev/rl_efrei-xmg4agau1/home
  Size of logical volume rl_efrei-xmg4agau1/home changed from 3.00 GiB (769 extents) to 22.00 GiB (5633 extents).
  Logical volume rl_efrei-xmg4agau1/home successfully resized.

```

```
[hugo@efrei-xmg4agau1 ~]$ sudo resize2fs /dev/rl_efrei-xmg4agau1/home
resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/rl_efrei-xmg4agau1/home is mounted on /home; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 3
The filesystem on /dev/rl_efrei-xmg4agau1/home is now 5768192 (4k) blocks long.

[hugo@efrei-xmg4agau1 ~]$ lsblk
NAME                         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                            8:0    0   20G  0 disk
├─sda1                         8:1    0 1021M  0 part /boot
└─sda2                         8:2    0   19G  0 part
  ├─rl_efrei--xmg4agau1-root 253:0    0   10G  0 lvm  /
  ├─rl_efrei--xmg4agau1-swap 253:1    0    1G  0 lvm  [SWAP]
  ├─rl_efrei--xmg4agau1-home 253:2    0   22G  0 lvm  /home
  └─rl_efrei--xmg4agau1-var  253:3    0    5G  0 lvm  /var
sdb                            8:16   0   40G  0 disk
└─sdb1                         8:17   0   20G  0 part
  └─rl_efrei--xmg4agau1-home 253:2    0   22G  0 lvm  /home
sr0                           11:0    1 1024M  0 rom
```

3. Prepare another partition
Cette partition contiendra des fichiers HTML pour des sites web (fictifs).
🌞 Créez une nouvelle partition

```
[hugo@efrei-xmg4agau1 ~]$ sudo pvcreate /dev/sdb2
  WARNING: adding device /dev/sdb2 with idname t10.ATA_VBOX_HARDDISK_VBdd91d800-d692a3c3 which is already used for /dev/sdb.
  Physical volume "/dev/sdb2" successfully created.
[hugo@efrei-xmg4agau1 ~]$ sudo vgextend rl_efrei-xmg4agau1 /dev/sdb2
  WARNING: adding device /dev/sdb2 with idname t10.ATA_VBOX_HARDDISK_VBdd91d800-d692a3c3 which is already used for /dev/sdb.
  Volume group "rl_efrei-xmg4agau1" successfully extended
[hugo@efrei-xmg4agau1 ~]$ sudo vgs
  VG                 #PV #LV #SN Attr   VSize  VFree
  rl_efrei-xmg4agau1   3   4   0 wz--n- 58.99g 20.99g
[hugo@efrei-xmg4agau1 ~]$ sudo lvcreate -L 20G -n web rl_efrei-xmg4agau1
  Logical volume "web" created.
```
























