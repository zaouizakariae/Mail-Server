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
```
