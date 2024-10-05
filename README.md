# FujitsuEnterprisePostgres
Installasi Fujitsu Enterprise Postgres on Oracle Linux 8



```
https://www.postgresql.fastware.com/knowledge-base/quick-start-guides
```

```
[root@ABDUMR ~]# hostnamectl
   Static hostname: ABDUMR.localdomain
         Icon name: computer-vm
           Chassis: vm
        Machine ID: aee3bd3429a84e97959e8d0649ae25be
           Boot ID: e82b3b84cc1f409e8fe3144b9acdad5a
    Virtualization: oracle
  Operating System: Oracle Linux Server 8.8
       CPE OS Name: cpe:/o:oracle:linux:8:8:server
            Kernel: Linux 5.15.0-101.103.2.1.el8uek.x86_64
      Architecture: x86-64
```

```
[root@ABDUMR ~]# lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda           8:0    0   60G  0 disk
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0   59G  0 part
  ├─ol-root 252:0    0 36.4G  0 lvm  /
  ├─ol-swap 252:1    0  4.9G  0 lvm  [SWAP]
  └─ol-home 252:2    0 17.8G  0 lvm  /home
sdb           8:16   0   60G  0 disk
sr0          11:0    1 1024M  0 rom
```

```
[root@ABDUMR ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             2.2G     0  2.2G   0% /dev
tmpfs                2.3G     0  2.3G   0% /dev/shm
tmpfs                2.3G  9.1M  2.2G   1% /run
tmpfs                2.3G     0  2.3G   0% /sys/fs/cgroup
/dev/mapper/ol-root   37G  6.8G   30G  19% /
/dev/mapper/ol-home   18G  1.6G   17G   9% /home
/dev/sda1           1014M  344M  671M  34% /boot
tmpfs                453M   12K  453M   1% /run/user/42
tmpfs                453M     0  453M   0% /run/user/0
```

```
[root@ABDUMR ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:          4.4Gi       587Mi       1.9Gi        10Mi       1.9Gi       3.6Gi
Swap:         4.8Gi          0B       4.8Gi
```

Perintah fdisk /dev/sdb digunakan untuk mengelola partisi pada perangkat penyimpanan di Linux.
```
[root@ABDUMR ~]# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xf55d1076.

Command (m for help): [n]
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): [p]
Partition number (1-4, default 1):
First sector (2048-125829119, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-125829119, default 125829119):

Created a new partition 1 of type 'Linux' and of size 60 GiB.

Command (m for help): [w]

The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

```
Perintah pvcreate /dev/sdb1 digunakan untuk menginisialisasi partisi (dalam hal ini, /dev/sdb1) sebagai Physical Volume (PV) untuk Logical Volume Manager (LVM) di Linux. LVM adalah metode untuk mengelola ruang penyimpanan secara lebih fleksibel daripada menggunakan partisi tradisional.
```
[root@ABDUMR ~]# pvcreate /dev/sdb1
  Physical volume "/dev/sdb1" successfully created.
```
Perintah vgcreate database_vg /dev/sdb1 digunakan untuk membuat Volume Group (VG) baru di dalam sistem Logical Volume Manager (LVM)
```
[root@ABDUMR ~]# vgcreate database_vg /dev/sdb1
  Volume group "database_vg" successfully created
```
Perintah lvcreate -l 100%FREE -n database_lv database_vg digunakan untuk membuat Logical Volume (LV) baru bernama database_lv dalam Volume Group (VG) bernama database_vg.
```
[root@ABDUMR ~]# lvcreate -l 100%FREE -n database_lv database_vg
  Logical volume "database_lv" created.
```
Perintah mkfs.ext4 /dev/database_vg/database_lv digunakan untuk memformat Logical Volume (LV) database_lv, yang terletak di dalam Volume Group database_vg, dengan sistem file ext4.
```
[root@ABDUMR ~]# mkfs.ext4 /dev/database_vg/database_lv
mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 15727616 4k blocks and 3932160 inodes
Filesystem UUID: 0b241990-fc6e-4e52-a4d2-4a1b6f7ff642
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424

Allocating group tables: done
Writing inode tables: done
Creating journal (65536 blocks): done
Writing superblocks and filesystem accounting information: done

```
Perintah mount /dev/database_vg/database_lv /opt digunakan untuk mount (mengaitkan) Logical Volume (LV) database_lv, yang berada dalam Volume Group (VG) database_vg, ke direktori /opt.
```
[root@ABDUMR ~]# mount /dev/database_vg/database_lv /opt
```
File /etc/fstab adalah file konfigurasi di Linux yang berisi daftar perangkat penyimpanan (partisi, Logical Volume, disk, dll.) yang akan di-mount secara otomatis saat sistem melakukan booting.
```
[root@ABDUMR ~]# vim /etc/fstab
/dev/database_vg/database_lv /opt  ext4 defaults 0 0
```

```
[root@ABDUMR ~]# lsblk
NAME                        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                           8:0    0   60G  0 disk
├─sda1                        8:1    0    1G  0 part /boot
└─sda2                        8:2    0   59G  0 part
  ├─ol-root                 252:0    0 36.4G  0 lvm  /
  ├─ol-swap                 252:1    0  4.9G  0 lvm  [SWAP]
  └─ol-home                 252:2    0 17.8G  0 lvm  /home
sdb                           8:16   0   60G  0 disk
└─sdb1                        8:17   0   60G  0 part
  └─database_vg-database_lv 252:3    0   60G  0 lvm  /opt
sr0                          11:0    1 1024M  0 rom
```

```
[root@ABDUMR ~]# df -h
Filesystem                           Size  Used Avail Use% Mounted on
devtmpfs                             2.2G     0  2.2G   0% /dev
tmpfs                                2.3G     0  2.3G   0% /dev/shm
tmpfs                                2.3G  9.1M  2.2G   1% /run
tmpfs                                2.3G     0  2.3G   0% /sys/fs/cgroup
/dev/mapper/ol-root                   37G  6.8G   30G  19% /
/dev/sda1                           1014M  344M  671M  34% /boot
/dev/mapper/ol-home                   18G  1.6G   17G   9% /home
tmpfs                                453M   12K  453M   1% /run/user/42
tmpfs                                453M     0  453M   0% /run/user/0
/dev/mapper/database_vg-database_lv   59G   24K   56G   1% /opt

```
```
[root@ABDUMR ~]# yum install redhat-lsb -y
[root@ABDUMR ~]# yum install epel-release -y
[root@ABDUMR ~]# yum install redhat-lsb -y
[root@ABDUMR ~]# rpm -q redhat-lsb
redhat-lsb-4.1-47.0.1.el8.x86_64
```
```
[root@ABDUMR ~]# mkdir -p /media/dvd
[root@ABDUMR ~]# mkdir -p /opt/database/inst1
[root@ABDUMR ~]# mkdir -p /opt/database/backup
[root@ABDUMR ~]# mkdir -p /opt/database/pg_tblspc
```
```
[root@ABDUMR ~]# useradd -m fepuser
[root@ABDUMR ~]# passwd fepuser
Changing password for user fepuser.
New password:
BAD PASSWORD: The password fails the dictionary check - it is based on a dictionary word
Retype new password:
passwd: all authentication tokens updated successfully.

```
```
[root@ABDUMR ~]# chown -R fepuser /opt/database/inst1
[root@ABDUMR ~]# chown -R fepuser /opt/database/backup
[root@ABDUMR ~]# chown -R fepuser /opt/database/pg_tblspc
```
```
[root@ABDUMR ~]# ls /home/admin/Downloads/
FujitsuEnterprisePostgres.iso
```
```
[root@ABDUMR ~]# mount -t iso9660 -r -o loop /home/admin/Downloads/FujitsuEnterprisePostgres.iso /media/dvd
[root@ABDUMR ~]# cd /media/dvd
[root@ABDUMR dvd]# ls
CIR       COMMON      manual       parser   readme.txt       sample  silent.sh
CLIENT64  install.sh  OSS_Licence  PGPOOL2  readme_utf8.txt  SERVER  WEBADMIN
```
```
[root@ABDUMR dvd]# ./install.sh

The following products can be installed:
1: FUJITSU Enterprise Postgres Advanced Edition (64bit) 14 SP1
2: FUJITSU Enterprise Postgres Client (64bit) 14 SP1
3: FUJITSU Enterprise Postgres WebAdmin 14 SP1
4: FUJITSU Enterprise Postgres Pgpool-II 14 SP1

Select the product to be installed.
Note: If installing the Server, it is strongly recommended to install WebAdmin.                                          
To select multiple products, separate using commas (,). (Example: 1,2)
[number,all,q](The default value is 1,2,3): [all]

Selected product
  FUJITSU Enterprise Postgres Advanced Edition (64bit) 14 SP1
  FUJITSU Enterprise Postgres Client (64bit) 14 SP1
  FUJITSU Enterprise Postgres WebAdmin 14 SP1
  FUJITSU Enterprise Postgres Pgpool-II 14 SP1

Do you want to install the above product?
y: Proceed to the next step
n: Select the product again
q: Quit without installing
[y,n,q](The default value is y): [y]

==============================================================================

Product to be installed
  FUJITSU Enterprise Postgres Advanced Edition (64bit) 14 SP1
    New installation
  FUJITSU Enterprise Postgres Client (64bit) 14 SP1
    New installation
  FUJITSU Enterprise Postgres WebAdmin 14 SP1
    New installation
  FUJITSU Enterprise Postgres Pgpool-II 14 SP1
    New installation

Installation directory information
  FUJITSU Enterprise Postgres Advanced Edition (64bit) 14 SP1
    /opt/fsepv14server64
  FUJITSU Enterprise Postgres Client (64bit) 14 SP1
    /opt/fsepv14client64
  FUJITSU Enterprise Postgres WebAdmin 14 SP1
    /opt/fsepv14webadmin
  FUJITSU Enterprise Postgres Pgpool-II 14 SP1
    /opt/fsepv14pgpool-II

Setup information
  WebAdmin setup: Execute
  Web server port number: 27515
  WebAdmin internal port number: 27516

Start installation using the above information?
y: Start the installation
c: Change the information
q: Quit without installing
[y,c,q](The default value is y): [y]


==============================================================================
Starting installation.

FUJITSU Enterprise Postgres Advanced Edition (64bit) 14 SP1 Installation

Installation is complete.

FUJITSU Enterprise Postgres Client (64bit) 14 SP1 Installation

Installation is complete.

FUJITSU Enterprise Postgres WebAdmin 14 SP1 Installation

Installation is complete.

FUJITSU Enterprise Postgres Pgpool-II 14 SP1 Installation

Installation is complete.

Starting setup.

Sets up WebAdmin.

Setup is complete.

Installed successfully.

```
```
[root@ABDUMR dvd]# ls -l /opt
total 4
dr-xr-x---.  4 root root   48 Oct  5 12:47 FJSVcir
drwxr-xr-x.  3 root root 4096 Oct  5 12:48 FJSVqstl
drwxr-xr-x. 12 root root  126 Jul  4  2022 fsepv14client64
drwxr-xr-x.  9 root root   91 Oct  5 12:49 fsepv14pgpool-II
drwxr-xr-x. 14 root root  147 Jul  4  2022 fsepv14server64
drwxr-xr-x. 13 root root  137 Oct  5 12:48 fsepv14webadmin
```
```
[root@ABDUMR dvd]# su - fepuser
[fepuser@ABDUMR ~]$ vi .bash_profile

PATH=/opt/fsepv14server64/bin:$HOME/.local/bin:$HOME/bin:$PATH
MANPATH=/opt/fsepv14server64/share/man:$MANPATH
LD_LIBRARY_PATH=/opt/fsepv14server64/lib:$LD_LIBRARY_PATH
export PATH
export MANPATH
export LD_LIBRARY_PATH

[fepuser@ABDUMR ~]$ . ./.bash_profile
```
```
[fepuser@ABDUMR ~]$ initdb -D /mnt/db/inst1 --lc-collate="C" --lc-ctype="C" --encoding=UTF8
The files belonging to this database system will be owned by user "fepuser".
This user must also own the server process.

The database cluster will be initialized with locales
  COLLATE:  C
  CTYPE:    C
  MESSAGES: en_US.UTF-8
  MONETARY: en_US.UTF-8
  NUMERIC:  en_US.UTF-8
  TIME:     en_US.UTF-8
The default text search configuration will be set to "english". (15541)

Data page checksums are disabled. (18153)

fixing permissions on existing directory /mnt/db/inst1 ... ok (15516)
creating subdirectories ... ok (15516)
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ...  (19842)Asia/Jakarta
creating configuration files ... ok (15516)
running bootstrap script ... ok (15516)
performing post-bootstrap initialization ... ok (15516)
syncing data to disk ... ok (15516)

initdb: warning: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    pg_ctl -D /mnt/db/inst1 -l logfile start
```
```
[fepuser@ABDUMR ~]$ pg_ctl -D /mnt/db/inst1 start -l logfile
waiting for server to start.... done
server started
```
```
[fepuser@ABDUMR ~]$ psql -d postgres
psql (14.0)
Type "help" for help.
```
```

postgres=# \l+
                                                             List of databases
   Name    |  Owner  | Encoding | Collate | Ctype |  Access privileges  |  Size   | Tablespace |                Descripti
on
-----------+---------+----------+---------+-------+---------------------+---------+------------+-------------------------
-------------------
 postgres  | fepuser | UTF8     | C       | C     |                     | 8979 kB | pg_default | default administrative c
onnection database
 template0 | fepuser | UTF8     | C       | C     | =c/fepuser         +| 8825 kB | pg_default | unmodifiable empty datab
ase
           |         |          |         |       | fepuser=CTc/fepuser |         |            |
 template1 | fepuser | UTF8     | C       | C     | =c/fepuser         +| 8825 kB | pg_default | default template for new
 databases
           |         |          |         |       | fepuser=CTc/fepuser |         |            |
(3 rows)
```

```
postgres=# create database dashboard;
CREATE DATABASE
postgres=# \l
                           List of databases
   Name    |  Owner  | Encoding | Collate | Ctype |  Access privileges
-----------+---------+----------+---------+-------+---------------------
 dashboard | fepuser | UTF8     | C       | C     |
 postgres  | fepuser | UTF8     | C       | C     |
 template0 | fepuser | UTF8     | C       | C     | =c/fepuser         +
           |         |          |         |       | fepuser=CTc/fepuser
 template1 | fepuser | UTF8     | C       | C     | =c/fepuser         +
           |         |          |         |       | fepuser=CTc/fepuser
(4 rows)

```

```
[root@ABDUMR fsepv14webadmin]# cd /opt/fsepv14webadmin/sbin/
[root@ABDUMR sbin]# ls
WebAdminPs  WebAdminSetup  WebAdminStart  WebAdminStop
[root@ABDUMR sbin]# ./WebAdminSetup

*********************
*** WebAdminSetup ***
*********************

enter port number of Web Server (default: 27515):
->

enter Internal port number for WebAdmin(default: 27516):
->

Start WebAdmin automatically when system starting ? [y,n] (default: y)
-> y

### information ###

port number of Web Server : 27515
Internal port number for WebAdmin    : 27516
WebAdmin autostart             : yes

Confirm the information ? [y,n] y


### stopping WebAdmin ###

### WebAdmin was stopped ###

### making configuration files ###

### editing /etc/services ###

### starting WebAdmin ###


### finished ###
```

```
[root@ABDUMR sbin]# sudo firewall-cmd --permanent --add-port=27515/tcp
success
[root@ABDUMR sbin]# sudo firewall-cmd --reload
success
```

```
http://192.168.100.14:27515/
```





