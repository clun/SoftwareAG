# Install SAG Product on CentOS

This notes are meant to speed up fresh installation of the product on CentOS.

### OS Setting

* update OS
```bash
yum upgrade
```
* Create user
```bash
adduser webmethods -c "User tools webmethods"
passwd webmethods
```
usermod -aG wheel webmethods
[root@DLNXESBV2APPAPI01 ~]$ su webmethods -
 [webmethods@DLNXESBV2APPAPI01 root]$ groups
webmethods wheel
[webmethods@DLNXESBV2APPAPI01 root]$ sudo whoami

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:
    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.
[sudo] password for webmethods:
Root

sudo sysctl -a | fgrep kernel.shmmax
[sudo] password for webmethods:
kernel.shmmax = 18446744073692774399

•	[Prérequis technique] Vérification des paramètres de la „file descriptor“


webmethods@DLNXESBV2APPAPI01:~> sudo sysctl -a | grep ^fs.file*
fs.file-max = 793353
fs.file-nr = 1056       0       793353

webmethods@DLNXESBV2APPAPI01:~> sudo cat /proc/sys/fs/file-max
793353

Cette valeur est bien supérieure à 200000.

•	[Prérequis technique] Vérification des paramètres de la „ulimit“


webmethods@DLNXESBV2APPAPI01:~> ulimit -Hn
4096
webmethods@DLNXESBV2APPAPI01:~> ulimit -Sn
1024


As root:
[root@DLNXESBV2APPAPI01 ~]$ sudo echo "webmethods soft nofile 200000" >> /etc/security/limits.conf

[root@DLNXESBV2APPAPI01 ~]$ sudo echo "webmethods hard nofile 200000" >> /etc/security/limits.conf

[root@DLNXESBV2APPAPI01 ~]$ ulimit -n 200000


webmethods@DLNXESBV2APPAPI01:~> ulimit -n
200000
webmethods@DLNXESBV2APPAPI01:~> ulimit -Hn
200000
webmethods@DLNXESBV2APPAPI01:~> ulimit -Sn
200000


•	[Prérequis technique] Paramètres de l‘OS

webmethods@DLNXESBV2APPAPI01:~> uname -a
Linux DLNXESBV2APPAPI01.filhetallard.com 3.10.0-327.el7.x86_64 #1 SMP Thu Oct 29 17:29:29 EDT 2015 x86_64 x86_64 x86_64 GNU/Linux

64 bits ?
webmethods@DLNXESBV2APPAPI01:~> uname -m
x86_64

webmethods@DLNXESBV2APPAPI01:~>wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u111-b14/jdk-8u111-linux-x64.tar.gz"

