# FujitsuEnterprisePostgres
Installasi Fujitsu Enterprise Postgres on Oracle Linux 8



```
https://www.postgresql.fastware.com/knowledge-base/quick-start-guides
```


```
[root@ABDUMRt ~]# lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda           8:0    0   60G  0 disk
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0   59G  0 part
  ├─ol-root 252:0    0 36.4G  0 lvm  /
  ├─ol-swap 252:1    0  4.9G  0 lvm  [SWAP]
  └─ol-home 252:2    0 17.8G  0 lvm  /home
sdb           8:16   0   60G  0 disk
sr0          11:0    1 1024M  0 rom

[root@ABDUMRt ~]# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xf55d1076.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-125829119, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-125829119, default 125829119):

Created a new partition 1 of type 'Linux' and of size 60 GiB.

Command (m for help): w

The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

```
yum install redhat-lsb -y

yum install epel-release -y

yum install redhat-lsb -y

rpm -q redhat-lsb 



yum install alsa-lib

[root@hostname ~]# yum install llvm
[root@hostname ~]# yum install lz4-libs 
[root@hostname ~]# yum install pam



[root@hostname ~]# mkdir -p /media/dvd
[root@hostname ~]# mkdir -p /database/inst1
[root@hostname ~]# mkdir /backup
[root@hostname ~]# mkdir /pg_tblspc




[root@hostname ~]# useradd -m fepuser
[root@hostname ~]# passwd fepuser



[root@hostname ~]# chown -R fepuser /database/inst1
[root@hostname ~]# chown -R fepuser /backup
[root@hostname ~]# chown -R fepuser /pg_tblspc




[root@hostname ~]# mount -t iso9660 -r -o loop /home/admin/Downloads/FUJITSU_Enterprise_Postgres_version_FullVersion.iso /media/dvd


[root@hostname ~]# cd /media/dvd
[root@hostname ~]# ./install.sh





[root@hostname ~]# ls -l /opt



[root@hostname ~]# su - fepuser
[fepuser@hostname ~]$ vi .bash_profile



PATH=/opt/fsepv14server64/bin:$HOME/.local/bin:$HOME/bin:$PATH
MANPATH=/opt/fsepv14server64/share/man:$MANPATH
LD_LIBRARY_PATH=/opt/fsepv14server64/lib:$LD_LIBRARY_PATH
export PATH
export MANPATH
export LD_LIBRARY_PATH
~












[fepuser@TEST ~]$ . ./.bash_profile

[fepuser@TEST ~]$ initdb -D /database/inst1 --lc-collate="C" --lc-ctype="C" --encoding=UTF8




















[fepuser@hostname ~]$ pg_ctl -D /database/inst1 start -l logfile
[fepuser@hostname ~]$ psql -d postgres
postgres=# \l+

[fepuser@TEST ~]$ curl -kv http://localhost:27515








[root@TEST ~]# cd /opt/fsepv14webadmin/sbin/

[root@TEST sbin]# ll

[root@TEST sbin]# ./WebAdminSetup



