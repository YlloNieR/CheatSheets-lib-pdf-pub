# CheatSheets_lib
### Java
- Class
- Conditional statements
- Enum
- Exceptions
- Exceptions
- For Loops
- Gerneral information
- Heredity
- Imports / Java-packages
- Lists
- Methods
- Numbers
- Operators
- Override
- Strings
- UML / Class diagrams
- While Loops

### Linux Commands
- Gerneral commands
- manipulate files
- Navigation commands
- Network commands
- Path details
- Permission / privileges
- Shortcuts
- System commands

### SQL 
- Change attributes
- Check changes
- Codd's rules
- create
- Data types
- Delete / Drop
- General information
- Insert
- Strings
 
### Windows commands
- Network commands
- Path of commands
- Search
- Useful commands
 
 
# LDAP Installation and Config
### 1. Step - Login to root and change the Hostname

&nbsp;&nbsp;&nbsp;&nbsp;```urname@kali$ sudo -i```

&nbsp;&nbsp;&nbsp;&nbsp;```root@kali$ gedit /etc/hostname``` or ```root@kali$$ nano /etc/hostname```

write e.g. **server.ldap.com** CTRL+O,ENTER,CTRL+X

&nbsp;&nbsp;&nbsp;&nbsp;```root@kali$ reboot```

check change

&nbsp;&nbsp;&nbsp;&nbsp;```urname@server$ sudo -i```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ hostname```

output: 

&nbsp;&nbsp;&nbsp;&nbsp;```server.ldap.com```

### 2. Step - Install OpenLDAP

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ apt-get install slapd ldap-utils -y``` or ```root@server$ yum install *openldap* -y```

give the Admin password for your server
and confirm

### 3. Step - Configure OpenLDAP

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ nano /etc/ldap/ldap.conf```

```
#  
# LDAP Defaults
# 

# See ldap.conf(5) for details
# This file should be world readable but not writable.

BASE dc=ldap,dc=com
URI ldap://localhost:389

# SIZELIMIT 12
# TIMELIMIT 15
# DEREF never

# TLS certificates
TLS_CACERT /etc/ssl/certs/ca-certificates.crt
``` 

### 4. Step - Reconfigure the slapd with the updated values

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ dpkg-reconfigure slapd```

&nbsp;&nbsp;&nbsp;&nbsp;Omit OpenLDAP server configuration:```No```

&nbsp;&nbsp;&nbsp;&nbsp;DNS domain name:```ldap.com```

&nbsp;&nbsp;&nbsp;&nbsp;Organization name:```ldap company``` 

&nbsp;&nbsp;&nbsp;&nbsp;admin pw: :```password```

&nbsp;&nbsp;&nbsp;&nbsp;confirm pw:```password```

&nbsp;&nbsp;&nbsp;&nbsp;(Database backend to use:```MDB```)

&nbsp;&nbsp;&nbsp;&nbsp;Do you want the database to be removed when slapd is purged?```No```

&nbsp;&nbsp;&nbsp;&nbsp;Move old database?```Yes```

&nbsp;&nbsp;&nbsp;&nbsp;(Allow LDAPv2 protocol?```No```)

### 5. Step - Check your Config

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapsearch -x``` or ```root@server$dpkg-reconfigure slapd```

output:

```
# extended LDIF
#
# LDAPv3
# base <dc=ldap,dc=com> (default) with scope subtree
# filter: (objectclass=*)
# requesting: All
# 

# ldap.com
dn: dc=ldap,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
o: ldap com
result: 0 success

# num Responses: 2
# numEntries: 1
```

## Troubleshooting

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapsearch -x```

output:

&nbsp;&nbsp;&nbsp;&nbsp;```ldap_sasl_bind(SIMPLE): Can't contact LDAP server (-1)```

change ldap.conf

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ nano /etc/ldap/ldap.conf```

restart the service

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ps aux |grep slapd```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ /etc/init.d/slapd restart```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ps aux |grep slapd```

### 6. Step - install phpldapadmin

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ apt-get install phpldapadmin -y```

### 7. Step - look at your ipv4 address

&nbsp;&nbsp;&nbsp;&nbsp;```urname@server$ ifconfig```

look at inet addr for e.g.: 192.168.0.100

### 8. Step - Configure config.php

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ nano /etc/phpldapadmin/config.php```

change those rows:

&nbsp;&nbsp;&nbsp;&nbsp;```$server->setvalue('server','name','Name of ur server');```

host = your ip address

&nbsp;&nbsp;&nbsp;&nbsp;```$server->setvalue('server','host','192.168.0.100');``` 

&nbsp;&nbsp;&nbsp;&nbsp;```$server->setvalue('server','base',array('dc=ldap,dc=com'));``` 

&nbsp;&nbsp;&nbsp;&nbsp;```$server->setvalue('login','bind_id','cn=admin,dc=ldap,dc=com');``` 

&nbsp;&nbsp;&nbsp;&nbsp;```// $config->custom->appearance['hide_template_warning'] - true;``` 

### 9. Step - restart service

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ systemctl restart apache2```

### 10. Step - open website

&nbsp;&nbsp;&nbsp;&nbsp;```http://192.168.0.100/phpldapadmin/```

# How to import keys from a keyserver using gpg in debian? / Public key error / Fix apt-get update “the following signatures couldn’t be verified because the public key is not available”

problem:

```
[username@kali ~]$ sudo apt-get update
Ign http://security.ubuntu.com trusty-security InRelease
Get:1 http://security.ubuntu.com trusty-security Release.gpg [933 B]
...
Fetched 21.9 MB in 14s (1,537 kB/s)
Reading package lists... Done
W: GPG error: http://security.ubuntu.com trusty-security Release: The following signatures couldn't be verified 
because the public key is not available: NO_PUBKEY 40946E2F437D05B5
```

on kali

&nbsp;&nbsp;&nbsp;&nbsp;```$ sudo gpg --keyserver pgpkeys.mit.edu --recv-key  40946E2F437D05B5```

OR try this server:

&nbsp;&nbsp;&nbsp;&nbsp;```$ sudo gpg --keyserver pool.sks-keyservers.net --recv-keys 40946E2F437D05B5```

&nbsp;&nbsp;&nbsp;&nbsp;```$ sudo gpg -a --export 40946E2F437D05B5 | sudo apt-key add -```

on ubuntu

&nbsp;&nbsp;&nbsp;&nbsp;```$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 40946E2F437D05B5```
