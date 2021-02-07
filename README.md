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

&nbsp;&nbsp;&nbsp;&nbsp;```root@kali$ gedit /etc/hostname``` or ```root@kali$ nano /etc/hostname``` or ```root@kali$ vi /etc/hostname```

write e.g. **server.lingoworld.de** CTRL+O,ENTER,CTRL+X

&nbsp;&nbsp;&nbsp;&nbsp;```root@kali$ reboot```

check change

&nbsp;&nbsp;&nbsp;&nbsp;```urname@server$ sudo -i```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ hostname```

output: 

&nbsp;&nbsp;&nbsp;&nbsp;```server.lingoworld.de```

### 2. Step - install OpenLDAP

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ apt-get install slapd -y```

check if the server is already started

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ps aux |grep slapd```

if not start it

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ /etc/init.d/slapd restart```

### 3. Step - Reconfigure the slapd with the updated values

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ dpkg-reconfigure slapd```

&nbsp;&nbsp;&nbsp;&nbsp;Omit OpenLDAP server configuration:```No```

&nbsp;&nbsp;&nbsp;&nbsp;DNS domain name:```lingoworld```

&nbsp;&nbsp;&nbsp;&nbsp;Organization name:```de``` 

&nbsp;&nbsp;&nbsp;&nbsp;admin pw: :```secret```

&nbsp;&nbsp;&nbsp;&nbsp;confirm pw:```secret```

&nbsp;&nbsp;&nbsp;&nbsp;Do you want the database to be removed when slapd is purged?```No```

&nbsp;&nbsp;&nbsp;&nbsp;Move old database?```Yes```

### 4. Step - Check your Config

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapsearch -x``` or ```root@server$dpkg-reconfigure slapd```

output:

```bash
# extended LDIF
#
# LDAPv3
# base <dc=llingoworld,dc=de> (default) with scope subtree
# filter: (objectclass=*)
# requesting: All
# 

# lingoworld.de
dn: dc=llingoworld,dc=de
objectClass: top
objectClass: dcObject
objectClass: organization
o: ldap com
result: 0 success

# num Responses: 2
# numEntries: 1
```

---

## Troubleshooting

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapsearch -x```

output:

&nbsp;&nbsp;&nbsp;&nbsp;```ldap_sasl_bind(SIMPLE): Can't contact LDAP server (-1)```

restart the service

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ps aux |grep slapd```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ /etc/init.d/slapd restart```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ps aux |grep slapd```

---

### 5. Step - insert data

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/ldap/schema/cosine.ldif```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/ldap/schema/nis.ldif```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/ldap/schema/inetorgperson.ldif```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ nano backend.ligoworld.com```

```bash
# Load dynamic backend modules
dn: cn=module,cn=config
objectClass: olcModuleList
cn: module
olcModulepath: /usr/lib/ldap
olcModuleload: back_hdb

# Database settings
dn: olcDatabase=hdb,cn=config
objectClass: olcDatabaseConfig
objectClass: olcHdbConfig
olcDatabase: {1}hdb
olcSuffix: dc=lingoworld,dc=de
olcDbDirectory: /var/lib/ldap
olcRootDN: cn=admin,dc=lingoworld,dc=de
olcRootPW: secret
olcDbConfig: set_cachesize 0 2097152 0
olcDbConfig: set_lk_max_objects 1500
olcDbConfig: set_lk_max_locks 1500
olcDbConfig: set_lk_max_lockers 1500
olcDbIndex: objectClass eq
olcLastMod: TRUE
olcDbCheckpoint: 512 30
olcAccess: to attrs=userPassword by dn="cn=admin,dc=lingoworld,dc=de" write by anonymous auth by self write by * none
olcAccess: to attrs=shadowLastChange by self write by * read
olcAccess: to dn.base="" by * read
olcAccess: to * by dn="cn=admin,dc=lingoworld,dc=de" write by * read
```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapadd -Y EXTERNAL -H ldapi:/// -f frontend.lingoworld.de.ldif```

---

## Troubleshooting

```bash
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
adding new entry "dc=lingoworld,dc=de"
ldap_add: Insufficient access (50)
        additional info: no write access to parent
```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapadd -x -D cn=admin,dc=lingoworld,dc=de -w secret -H ldapi:/// -f frontend.lingoworld.de.ldif```

---

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapsearch -x```

---

check inserted data

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapsearch -xLLL -b dc=lingoworld,dc=de uid=john sn givenName cn```

```bash
dn: uid=john,ou=Developer,dc=lingoworld,dc=de
sn: Doe
givenName: John
cn: John Doe
```

check configuration

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapsearch -LLL -Y EXTERNAL -H ldapi:/// -b cn=config dn```

```bash
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
dn: cn=config

dn: cn=module{0},cn=config

dn: cn=module{1},cn=config

dn: cn=schema,cn=config

dn: cn={0}core,cn=schema,cn=config

dn: cn={1}cosine,cn=schema,cn=config

dn: cn={2}nis,cn=schema,cn=config

dn: cn={3}inetorgperson,cn=schema,cn=config

dn: olcDatabase={-1}frontend,cn=config

dn: olcDatabase={0}config,cn=config

dn: olcDatabase={1}mdb,cn=config

dn: olcDatabase={2}hdb,cn=config
```


### 6. Step - install phpldapadmin

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ apt-get install phpldapadmin -y```

### 6. Step - look at your ipv4 address

&nbsp;&nbsp;&nbsp;&nbsp;```urname@server$ ifconfig```

look at inet addr for e.g.: 192.168.0.100

### 7. Step - Configure config.php

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ nano /etc/phpldapadmin/config.php``` or ```root@server$ vi /etc/phpldapadmin/config.php```

change those rows:

&nbsp;&nbsp;&nbsp;&nbsp;```$server->setvalue('server','name','Name of ur server');```

host = your ip address

&nbsp;&nbsp;&nbsp;&nbsp;```$server->setvalue('server','host','192.168.0.100');``` 

&nbsp;&nbsp;&nbsp;&nbsp;```$server->setvalue('server','base',array('dc=llingoworld,dc=de'));``` 

&nbsp;&nbsp;&nbsp;&nbsp;```$server->setvalue('login','bind_id','cn=admin,dc=llingoworld,dc=de');``` 

&nbsp;&nbsp;&nbsp;&nbsp;```// $config->custom->appearance['hide_template_warning'] - true;``` 

### 8. Step - restart service

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ systemctl restart apache2```

### 9. Step - open website

&nbsp;&nbsp;&nbsp;&nbsp;```http://192.168.0.100/phpldapadmin/```

### 12. Step - add User
&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ useradd testuser1```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ passwd testuser1```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ useradd testuser2```

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ passwd testuser2```

### 13. Step - Migrate local users to LDAP
&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ nano /usr/share/openldap/migration/migrate_common.ph```

```bash
#71 $DEFAULT_MAIL_DOMAIN = "lingoworld.de";
...
#74 $DEFAULT_BASE = "dc=llingoworld,dc=de";
```
### 14. Step - convert passwd.file to ldif (LDAP Data Interchange Format) file 

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ /usr/share/openldap/migration/migrate_passwd.pl /etc/ldap/passwd.root /etc/ldap/root.ldif```

## Troubleshooting

```
root@server:/usr/share/migrationtools# ./migrate_base.pl 
Can't locate migrate_common.ph in @INC (did you run h2ph?) (@INC
contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.24.1
/usr/local/share/perl/5.24.1 /usr/lib/x86_64-linux-gnu/perl5/5.24
/usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.24
/usr/share/perl/5.24 /usr/local/lib/site_perl
/usr/lib/x86_64-linux-gnu/perl-base) at ./migrate_base.pl line 39.
```
&nbsp;&nbsp;&nbsp;&nbsp;``root@server$ nano migrate_passwd.pl``

&nbsp;&nbsp;&nbsp;&nbsp;```#41  require './migrate_common.ph';```

and

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ln -s /etc/migrationtools/migrate_common.ph /etc/perl/```

try again

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ /usr/share/migrationtools/migrate_passwd.pl /etc/ldap/passwd.root /etc/ldap/root.ldif```

---

### 14. Step - Update root.ldif file for the ldap Server

&nbsp;&nbsp;&nbsp;&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ nano /etc/ldap/root.ldif```

```
 #1 dn: uid=root,ou=People,dc=adminmart,dc=de
 #2 uid: root
 #3 cn: Manager
 #4 objectClass: account
```

---

### 15. Step - Create a domain ldif file (/etc/ldap/lingoworld.de.ldif)
&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ cat /etc/ldap/lingoworld.de.ldif```

```
dn: dc=llingoworld,dc=de
dc: ldap
description: LDAP Admin
objectClass: dcObject
objectClass: organizationalUnit
ou: rootobject 
dn: ou=People, dc=llingoworld,dc=de
ou: People
description: Users of ldap Server
objectClass: organizationalUnit
```
---


```root@server$ /etc/init.d/slapd restart```

### 16. Step - Import all users in to the LDAP
&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ldapadd -x -D "cn=Manager,dc=llingoworld,dc=de" -W -f  /etc/ldap/lingoworld.de.ldif```

---


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
