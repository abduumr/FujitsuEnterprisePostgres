# FujitsuEnterprisePostgres
Installasi Fujitsu Enterprise Postgres on Oracle Linux 8



```
https://www.postgresql.fastware.com/knowledge-base/quick-start-guides
```

```
[root@ABDUMR ~]# hostnamectl
   Static hostname: ABDUMRt.localdomain
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
```
[root@ABDUMR ~]# pvcreate /dev/sdb1
  Physical volume "/dev/sdb1" successfully created.
```
```
[root@ABDUMR ~]# vgcreate db_vg /dev/sdb1
  Volume group "db_vg" successfully created
```
```
[root@ABDUMRt ~]# lvcreate -l 100%FREE -n db_lv db_vg
  Logical volume "db_lv" created.
```
```
[root@ABDUMR ~]# mkfs.ext4 /dev/db_vg/db_lv
mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 15727616 4k blocks and 3932160 inodes
Filesystem UUID: d6ae70a5-5b60-449d-8d1a-e30df9f56be8
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424

Allocating group tables: done
Writing inode tables: done
Creating journal (65536 blocks): done
Writing superblocks and filesystem accounting information: done

```
```
[root@ABDUMR ~]# mkdir /mnt/db
[root@ABDUMR ~]# mount /dev/db_vg/db_lv /mnt/db
```
```
[root@ABDUMR ~]# vim /etc/fstab
/dev/db_vg/db_lv /mnt/db ext4 defaults 0 0
```

```
[root@ABDUMR ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   60G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   59G  0 part
  ├─ol-root     252:0    0 36.4G  0 lvm  /
  ├─ol-swap     252:1    0  4.9G  0 lvm  [SWAP]
  └─ol-home     252:2    0 17.8G  0 lvm  /home
sdb               8:16   0   60G  0 disk
└─sdb1            8:17   0   60G  0 part
  └─db_vg-db_lv 252:3    0   60G  0 lvm  /mnt/db
sr0              11:0    1 1024M  0 rom
```

```
[root@ABDUMR ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 2.2G     0  2.2G   0% /dev
tmpfs                    2.3G     0  2.3G   0% /dev/shm
tmpfs                    2.3G  9.1M  2.2G   1% /run
tmpfs                    2.3G     0  2.3G   0% /sys/fs/cgroup
/dev/mapper/ol-root       37G  6.8G   30G  19% /
/dev/mapper/ol-home       18G  1.6G   17G   9% /home
/dev/sda1               1014M  344M  671M  34% /boot
tmpfs                    453M   12K  453M   1% /run/user/42
tmpfs                    453M     0  453M   0% /run/user/0
/dev/mapper/db_vg-db_lv   59G   24K   56G   1% /mnt/db

```

```
[root@ABDUMR ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 2.2G     0  2.2G   0% /dev
tmpfs                    2.3G     0  2.3G   0% /dev/shm
tmpfs                    2.3G  9.1M  2.2G   1% /run
tmpfs                    2.3G     0  2.3G   0% /sys/fs/cgroup
/dev/mapper/ol-root       37G  6.8G   30G  19% /
/dev/mapper/ol-home       18G  1.6G   17G   9% /home
/dev/sda1               1014M  344M  671M  34% /boot
tmpfs                    453M   12K  453M   1% /run/user/42
tmpfs                    453M     0  453M   0% /run/user/0
/dev/mapper/db_vg-db_lv   59G   24K   56G   1% /mnt/db

```
```
-isi
```
```
-isi
```
```
-isi
```
```
-isi
```
```
-isi
```
```
-isi
```
```
-isi
```





























































