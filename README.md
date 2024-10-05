# FujitsuEnterprisePostgres
Installasi Fujitsu Enterprise Postgres on Oracle Linux 8


```https://www.postgresql.fastware.com/knowledge-base/quick-start-guides
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



