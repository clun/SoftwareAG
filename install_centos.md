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
* Grant user as sudoer
```bash
visudo
usermod -aG wheel webmethods
su webmethods -
groups
```
* Edit .bash_profile of user
```bash
export PATH
export PS1="\[\033[34m\]\u\[\033[00m\]@\[\033[32m\]\h\[\033[00m\]:\[\033[33m\]\w\[\033[00m\]> "
export CLICOLOR=1
export LSCOLORS=dxfxcxdxbxegedabagacad
```
* Check shared-memory (must be greater than 629145600)
```bash
sudo sysctl -a | fgrep kernel.shmmax
```
* Check File Descriptor (must be greater than 200000)
```bash
sudo sysctl -a | grep ^fs.file*
sudo cat /proc/sys/fs/file-max
```
* Check „ulimit“ and change 
```bash
ulimit -Hn

ulimit -Sn

sudo echo "webmethods soft nofile 200000" >> /etc/security/limits.conf

sudo echo "webmethods hard nofile 200000" >> /etc/security/limits.conf

ulimit -n 200000
```

* Install JDK8
```bash
cd /opt/

wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-

cookie" "http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz"

tar xzf jdk-8u131-linux-x64.tar.gz

cd /opt/jdk1.8.0_131/

alternatives --install /usr/bin/java java /opt/jdk1.8.0_131/bin/java 2

alternatives --config java
```

