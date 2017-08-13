# LinuxServer
__Notes about the Ubuntu Server at 46.163.78.221__

## Fresh system after reset

Get the initial password from KIS and navigate to the Plesk login screen. Log in as admin. Then you are automatically redirected to the page to create the Administrator account. Unter __Create Your Webspace__ add *mjendryke*.

## Access
simply start Anaconda shell and ``` activate webdev ``` and ```ssh root@46.163.78.221``` with the initial password from KIS. Change root password with ``` passwd ```.



## Add User
Change root password with
``` passwd ```
then add user michael with
``` adduser michael ```
and change password again with
``` passwd ```
then
``` su michael ```
and add michael to sudoers with
``` adduser michael sudo ```

## What to Install
        sudo apt-get install nano \

## Useful commands for general use
To find a directory

``` find / -type d -name 'httpdocs' ```

To find a file

``` find / -type f -name 'file' ```

### Create additional files for aliases and PATH
create file for aliases __.bash_aliases__ and copy paste content from __WARNING__ maybe just download the file from github directly

``` cd ~ && touch .bash_aliases && nano .bash_aliases ```

create file for PATH __.bash_profile__

``` cd ~ && touch .bash_profile && nano .bash_profile ```

and add

> set PATH so it includes user's private bin directories        
> PATH="$HOME/bin:$HOME/.local/bin:$PATH"       
> PATH="/usr/lib/postgresql/9.6/bin:$PATH"

Now modify __.bashrc__

``` nano .bashrc ```

remove comment in line ``` #force_color_prompt=yes ```
and change line ``` PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m$ '``` to
 ```PS1='${debian_chroot:+($debian_chroot)}\[\033[01;31m\]\u@\h\[\033[00m\]:\[\033[01;33m\]\w\[\033[00m$\nDo: '```

and add at the end of .bashrc file
> #Source bash_profile to set PATH      
> if [ -f ~/.bash_profile ]; then       
>        . ~/.bash_profile   
> fi   

Finally run ``` source .bashrc ``` and test ``` echo $PATH ``` if the changes have been made.

## POSTGRES and POSTGIS

for everything in this chapter make

``` su michael && cd ~ ```

first!

### Links
* https://askubuntu.com/questions/831292/how-to-install-postgresql-9-6-on-any-ubuntu-version
* https://tecadmin.net/install-postgresql-server-on-ubuntu/
* https://help.ubuntu.com/community/PostgreSQL

### Postgres
Install Postgres current version

```sudo apt-get install postgresql postgresql-contrib```

then the following command brings us right into __psql__

``` sudo -u postgres psql postgres ```

and make sure you have set a password for postgres

``` \password postgres ```

Exit psql with ```\q``` and you are __back to user michael__

__works!!!__


### Adding the repository (optional for version 9.6)
```bash
su michael && cd ~
sudo nano /etc/apt/sources.list
deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
```

#### 9.6
Install Postgres 9.6

```sudo apt-get install postgresql-9.6 postgresql-contrib-9.6```

Terminal returns:

> Success. You can now start the database server using:
        /usr/lib/postgresql/9.6/bin/pg_ctl -D /var/lib/postgresql/9.6/main -l logfile start

``` su postgres && cd ~ ``` to __start__ the server do exactly like it is said above

``` /usr/lib/postgresql/9.6/bin/pg_ctl -D /var/lib/postgresql/9.6/main -l logfile start ```

then simply ``` exit ```

#### Errors
> pg_ctl: could not open PID file "/var/lib/postgresql/9.6/main/postmaster.pid": Permission denied

did you ``` su postgres ``` ?

> could not change directory to "/root": Permission denied

did you ``` cd ~ ``` of postgres user?

### PostgreSQL Server status
```sudo service postgresql status```

should return

> postgresql.service - PostgreSQL RDBMS
>   Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)    
>   Active: active (exited) since Sat 2017-08-12 10:19:28 CEST; 30min ago       
>  Process: 4180 ExecReload=/bin/true (code=exited, status=0/SUCCESS)   
>  Process: 6367 ExecStart=/bin/true (code=exited, status=0/SUCCESS)    
>Main PID: 6367 (code=exited, status=0/SUCCESS)
you can also try this to see if postgres is running

``` ps -ef | grep postgres ```

> postgres  9819     1  0 00:23 ?        00:00:00 /usr/lib/postgresql/9.5/bin/postgres -D /var/lib/postgresql/9.5/main -c config_file=/etc/postgresql/9.5/main/postgresql.conf  
> postgres  9821  9819  0 00:23 ?        00:00:00 postgres: checkpointer process        
> postgres  9822  9819  0 00:23 ?        00:00:00 postgres: writer process      
> postgres  9823  9819  0 00:23 ?        00:00:00 postgres: wal writer process  
> postgres  9824  9819  0 00:23 ?        00:00:00 postgres: autovacuum launcher process
> postgres  9825  9819  0 00:23 ?        00:00:00 postgres: stats collector process     
> michael  14880  7755  0 00:36 pts/0    00:00:00 grep --color=auto postgres

or

``` ps -f -u postgres ```

### PostgreSQL Server ports

These commands should show that postgres is running Listening on port 5432

``` sudo netstat -ltnp | grep postgres ```
> tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN      9819/postgres         
> tcp6       0      0 ::1:5432                :::*                    LISTEN      9819/postgres

and

``` sudo lsof -n -u postgres | grep LISTEN ```

> postgres 9819 postgres    6u  IPv6         1752214672      0t0        TCP [::1]:postgresql (LISTEN)   
> postgres 9819 postgres    7u  IPv4         1752214673      0t0        TCP 127.0.0.1:postgresql (LISTEN)


the port should be identical to

``` cat /etc/postgresql/9.6/main/postgresql.conf | grep port ```

> port = 5432



### Interacting with the server via __SSH__ and __psql__

* https://www.postgresql.org/docs/9.5/static/tutorial-createdb.html

Change to postgres user

``` sudo -u postgres -i ``` or ``` su postgres ```

create a database (you are posrgres user now)

``` createdb mydb ```

check if the data base is there

``` psql ```

and then this command will list all the databases

``` \list ```

__psql__ will by default connect to postgres DB, You can change the DB by connecting to the one you want

``` psql -d mydb ```

or

``` psql ``` and then ``` \c mydb ``` (I perfer the second way)

quit psql with

``` \q ```

### Postgis

```
su michael      
cd ~
sudo apt-get install postgis
```
And create the extension for postgis after ``` psql ``` and ``` \c geodata ```

```CREATE EXTENSION postgis``` __WARNING__ do not do this is postgres DB

#### Configuration on Server side
* http://www.thegeekstuff.com/2014/02/enable-remote-postgresql-connection
* https://help.ubuntu.com/community/PostgreSQL

```su michael      
cd ~    ```

Open file __pg_hba.conf__

```sudo nano /etc/postgresql/9.5/main/pg_hba.conf```

and add the server user and database to the list of allowed hosts

```
host     all             all             BlackBox-PC             md5
host     all             all             0.0.0.0/0               md5
```

close file and restart the server with

``` sudo service postgresql restart ``` maybe * reload * is enough here

Open file __postgresql.conf__

``` sudo nano /etc/postgresql/9.5/main/postgresql.conf ```

and uncomment and change the line to accept all IPs

``` #listen_addresses = 'localhost' ```

to

``` listen_addresses = '*' ```

close file and restart the server with

``` sudo service postgresql restart ``` maybe * reload * is enough here




#### Configuration on Client side
On client side try
psql -U postgres -h 192.168.102.1
        does not work

On server server
        sudo nano /etc/postgresql/9.6/main/postgresql.conf
change
        #listen_addresses = 'localhost'
to
        listen_addresses='\*'
to listen to all IP addresses and interfaces

and do a required restart
        sudo /etc/init.d/postgresql restart
        sudo service postgresql status

then
        sudo nano /etc/postgresql/9.6/main/pg_hba.conf
add
        host    all         all         121.60.37.76    md5
and
        sudo /etc/init.d/postgresql reload

## Important files
* sudo users ```sudo cat /etc/sudoers```
* list of repositories ``` sudo cat /etc/apt/sources.list ```

## Useful commands
To list all Users ```cut -d: -f1 /etc/passwd``` or ```cat /etc/passwd```

List of ports
        sudo iptables -L
or
        sudo netstat -anp | grep post


root dir for www files
/var/www/vhosts/lvps46-163-78-221.dedicated.hosteurope.de


## OpenVPN
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
