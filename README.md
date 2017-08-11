# LinuxServer
Notes about the Ubuntu Server

ssh root@46.163.78.221
passwd
adduser michael
passwd
su michael
exit
adduser michael sudo
su michael
sudo apt-get install nano
cd ~
touch .bash_aliases
nano .bash_aliases
!> copy past from github
nano .bashrc
remove comment inline
#force_color_prompt=yes
change line
        PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m$ '
to
        PS1='${debian_chroot:+($debian_chroot)}\[\033[01;31m\]\u@\h\[\033[00m\]:\[\033[01;33m\]\w\[\033[00m$\nDo: '
source .bashrc



Important files
sudo cat /etc/sudoers




root dir for www files
/var/www/vhosts/lvps46-163-78-221.dedicated.hosteurope.de


05082017
sudo apt-get install nano

find directory
find / -type d -name 'httpdocs'

OpenVPN:
cd downloads
Ubuntu/Debian:

Download and save the appropriate Debian package (.deb) file. Then go to the directory where you placed the downloaded file and enter the following command line, substituting the package filename:

dpkg -i openvpn-as-2.1.9-Ubuntu16.amd_64.deb

This should successfully install the Access Server package.

Note that if you ever have to remove the Access Server installation, the command to use is:

dpkg -r openvpn-as

Install obfsproxy
apt-get install obfsproxy

More about openvpn and obfsproxy
https://greycoder.com/openvpn-china/

More about stunnel
https://silvenga.com/openvpn-sheathing/

Install Tomcat8
https://www.digitalocean.com/community/tutorials/how-to-install-apache-tomcat-7-on-ubuntu-14-04-via-apt-get
sudo apt-get install tomcat8
sudo nano /etc/default/tomcat8
JAVA_OPTS="-Djava.security.egd=file:/dev/./urandom -Djava.awt.headless=true -Xmx512m -XX:MaxPermSize=256m -XX:+UseConcMarkSweepGC"
sudo service tomcat8 restart
DOES NOT WORK http://server_IP_address:8080
Set firewall rules in Plesk (only plesk is needed for firewall config

sudo apt-get install tomcat8-docs tomcat8-admin


sudo nano /etc/tomcasudo service tomcat8 restart

<user username="admin" password="j=i/HGyuawuc8(DBj.92pBt^" roles="manager-gui,admin-gui"/>

download geoserver.war
find the tomcat webapps folder

find / -type d -name 'webapps'
cp downloads/geoserver/geoserver.war /var/lib/tomcat8/webapps/.


Deleting, Removing, Purging


        apt-get remove packagename
        will remove the binaries, but not the configuration or data files of the package packagename.
        apt-get purge packagename, or
        apt-get remove --purge packagename
        will remove about everything regarding the package packagename, [...]
        Particularly useful when you want to 'start all over' with an application because you messed up the configuration.


PHP modules
php -v
php -m (list modules)
install zip module: apt-get install php7.0-zip


POSTGRES and POSTGIS
sudo add-apt-repository "deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main"
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get install postgresql-9.6

apt-get install postgis
sudo -u postgres -i
