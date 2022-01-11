# Mail-Server
# Introduction
In this practical work, we’ll learn how to setup a mail server using the Postfix mail server with the “Roundcube” webmail application and its dependencies on ubuntu.
# Set a Hostname and Create DNS Records for Domain
First, we are going to set a valid FQDN (Fully Qualified Domain Name) hostname for our server using the hostnamectl command 
```cpp
sudo hostnamectl set-hostname mail.taddist.com
```
Next,we need to add a MX and A records for our domain in our DNS control panel that guides other MTAs that our mail server mail.taddist.com domain is responsible for email delivery.
```cpp
MX record    @           mail.taddist.com
mail.taddist.com        192.168.1.2
```
# Installing Apache, MariaDB, and PHP on Ubuntu
In order to create a running mail server using “Roundcube”, we’ll have to install Apache2, MariaDB, and PHP packages first.
```cpp
sudo apt-get update
sudo apt-get upgrade
sudo apt install apache2 apache2-utils mariadb-server mariadb-client php7.4 libapache2-mod-php7.4 php7.4-mysql php-net-ldap2 php-net-ldap3 php-imagick php7.4-common php7.4-gd php7.4-imap php7.4-json php7.4-curl php7.4-zip php7.4-xml php7.4-mbstring php7.4-bz2 php7.4-intl php7.4-gmp php-net-smtp php-mail-mime php-net-idna2 mailutils
```
# Installing Postfix Mail Server on Ubuntu
Postfix is a mail transfer agent (MTA) which is the responsible software for delivering & receiving emails, it’s essential in order to create a complete mail server.
> you will be asked to choose the type of mail configuration, choose “Internet Site”
![Capture d’écran 2022-01-11 204312](https://user-images.githubusercontent.com/85891554/149012373-7c56f74a-ab7d-4739-82bd-3ed4afe012c6.png)
> Now we enter the fully qualified domain name

![Capture d’écran 2022-01-11 205204](https://user-images.githubusercontent.com/85891554/149012385-7ede9522-222b-458b-acef-1f98fd61a68a.png)
> Once Postfix installed, it will automatically start and creates a new /etc/postfix/main.cf file
```cpp
sudo systemctl status postfix
```
![Capture d’écran 2022-01-11 210503](https://user-images.githubusercontent.com/85891554/149013420-e699221f-d64f-46d4-a204-02817d6cf07f.png)
# Testing Postfix Mail Server on Ubuntu
to check that our mail server is connecting on port 25 we use the following command
```cpp
telnet gmail-smtp-in.l.google.com 25
```
results :
```cpp
Trying 74.125.200.27...
Connected to gmail-smtp-in.l.google.com.
Escape character is '^]'.
220 mx.google.com ESMTP k12si849250plk.430 - gsmtp
```
The above message indicates that the connection is successfully established
# Installing Dovecot IMAP and POP in Ubuntu
Dovecot is a mail delivery agent (MDA), it delivers the emails from/to the mail server .
```cpp
sudo apt-get install dovecot-imapd dovecot-pop3d
```
after the installation we restart the Dovecot service using the following command
```cpp
sudo systemctl restart dovecot
sudo systemctl status dovecot
```
![Capture d’écran 2022-01-11 210542](https://user-images.githubusercontent.com/85891554/149013434-4d8f07b8-cd54-4bce-a008-6e450573ef5b.png)
# Installing Roundcube Webmail in Ubuntu
Roundcube is the webmail server that we will be using to manage emails on our server
```cpp
wget https://github.com/roundcube/roundcubemail/releases/download/1.4.8/roundcubemail-1.4.8.tar.gz
tar -xvf roundcubemail-1.4.8.tar.gz
sudo mv roundcubemail-1.4.8 /var/www/html/roundcubemail
sudo chown -R www-data:www-data /var/www/html/roundcubemail/
sudo chmod 755 -R /var/www/html/roundcubemail/
```
we need to create a new database and user for Roundcube and grant all permission to a new user to write to the database
```cpp
sudo mysql -u root

MariaDB [(none)]> CREATE DATABASE roundcube DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
MariaDB [(none)]> CREATE USER roundcubeuser@localhost IDENTIFIED BY 'password';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON roundcube.* TO roundcubeuser@localhost;
MariaDB [(none)]> flush privileges;
MariaDB [(none)]> quit;
```
now we will import the initial tables to the Roundcube database
```cpp
sudo mysql roundcube < /var/www/html/roundcubemail/SQL/mysql.initial.sql
```
# Create an Apache Virtual Host for Roundcube Webmail
Create an apache virtual host for Roundcube webmail
```cpp
sudo nano /etc/apache2/sites-available/roundcube.conf
```
we will add the following configuration in it
```cpp
 <VirtualHost *:80>
  ServerName taddist.com
  DocumentRoot /var/www/html/roundcubemail/

  ErrorLog ${APACHE_LOG_DIR}/roundcube_error.log
  CustomLog ${APACHE_LOG_DIR}/roundcube_access.log combined

  <Directory />
    Options FollowSymLinks
    AllowOverride All
  </Directory>

  <Directory /var/www/html/roundcubemail/>
    Options FollowSymLinks MultiViews
    AllowOverride All
    Order allow,deny
    allow from all
  </Directory>

</VirtualHost>
````
we will enable this virtual host and reload the apache for the changes
```cpp
sudo a2ensite roundcube.conf
sudo systemctl reload apache2
```
now we can access the webmail by going to http://taddist.com/roundcubemail/installer/
![Capture d’écran 2022-01-11 213207](https://user-images.githubusercontent.com/85891554/149016970-2f75c9f2-c2cb-4780-b1cd-800fa6533df8.png)
Next, go to the Database settings and add the database details
![Capture d’écran 2022-01-11 212652](https://user-images.githubusercontent.com/85891554/149016989-393924ea-0cae-4a54-aee5-875a64bc31e3.png)
After making all the changes, create a config.inc.php file
![Capture d’écran 2022-01-11 213003](https://user-images.githubusercontent.com/85891554/149016997-9f99e560-e8bb-47a5-8fa9-5fbdf170ba71.png)
After finishing the installation and the final we need to delete the installer folder
```cpp
sudo rm /var/www/html/roundcubemail/installer/ -r
```
# Creating Mail Users
In order to start using the Roundcube webmail, we will have to create a new user
```cpp
sudo useradd zaza
```

```cpp
sudo passwd zaza12347
```
# Final test
Now we go to the login page and enter the user name and the password of the user
```cpp
http://taddist.com/roundcubemail/
```
![Capture d’écran 2022-01-11 214137](https://user-images.githubusercontent.com/85891554/149019500-b7e662d5-780c-46b0-b9ec-d7f2c914af1f.png)
here is the user interface
![Remini20220111214758457](https://user-images.githubusercontent.com/85891554/149019533-368a7ba8-0cd4-4e9f-8906-78092cc01737.jpg)
# THE END_______
