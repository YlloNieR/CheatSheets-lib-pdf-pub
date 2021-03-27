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
# base <dc=lingoworld,dc=de> (default) with scope subtree
# filter: (objectclass=*)
# requesting: All
# 

# lingoworld.de
dn: dc=lingoworld,dc=de
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

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ nano backend.lingoworld.com```

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
```bash
# Create top-level object in domain
dn: dc=lingoworld,dc=de
objectClass: top
objectClass: dcObject
objectclass: organization
o: lingoworld
dc: lingoworld
description: lingoworld Example

# Admin user.
dn: cn=admin,dc=lingoworld,dc=de
objectClass: simpleSecurityObject
objectClass: organizationalRole
cn: admin
description: LDAP administrator
userPassword: secret

dn: ou=Developer,dc=lingoworld,dc=de
objectClass: organizationalUnit
ou: Developer

dn: ou=groups,dc=lingoworld,dc=de
objectClass: organizationalUnit
ou: groups

dn: uid=john,ou=developer,dc=lingoworld,dc=de
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
uid: john
sn: Doe
givenName: John
cn: John Doe
displayName: John Doe
uidNumber: 1000
gidNumber: 10000
userPassword: secret
gecos: John Doe
loginShell: /bin/bash
homeDirectory: /home/john
shadowExpire: -1
shadowFlag: 0
shadowWarning: 7
shadowMin: 8
shadowMax: 999999
shadowLastChange: 10877
mail: john.doe@lingoworld.de
postalCode: 31000
l: Toulouse
o: Example
mobile: +33 (0)6 xx xx xx xx
homePhone: +33 (0)5 xx xx xx xx
title: System Administrator
postalAddress: 
initials: JD

#example group member
dn: cn=example,ou=groups,dc=lingoworld,dc=de
objectClass: posixGroup
cn: example
gidNumber: 10000
```

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

### 7. Step - Set server address

### Option 1 you take your current IPv4 address

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ ifconfig```

look at inet addr for e.g.: 192.168.0.100

### Option 2 you set a new one

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ nano /etc/hosts```

add your ipv4 adress for e.g.: 
```
127.0.0.1 localhost
127.0.1.1 server.lingoworld.de
```

### 8. Step - Configure config.php

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ nano /etc/phpldapadmin/config.php``` or ```root@server$ vi /etc/phpldapadmin/config.php```

change those rows:

&nbsp;&nbsp;&nbsp;&nbsp;```$server->setvalue('server','name','Name of ur server');```

host = your ip address

```php
// $config->custom->appearance['hide_template_warning'] - true;         #161
...
$server->setvalue('server','host','192.168.0.100');                     #293
...
$server->setvalue('server','base',array('dc=lingoworld,dc=de'));        #300
...
$server->setvalue('login','bind_id','cn=admin,dc=lingoworld,dc=de');    #326     

```

### 9. Step - restart service

&nbsp;&nbsp;&nbsp;&nbsp;```root@server$ systemctl restart apache2```

### 10. Step - open website

&nbsp;&nbsp;&nbsp;&nbsp;```http://192.168.0.100/phpldapadmin/```

