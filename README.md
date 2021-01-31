# CheatSheets_lib
 
# LDAP Installation and Config
### 1. Step - Login to root and change the Hostname

```urname@kali$sudo -i```

```root@kali$gedit /etc/hostname``` or ```$nano /etc/hostname```

write e.g. **server.ldap.com** CTRL+O,ENTER,CTRL+X

```root@kali$reboot```

check change

```urname@server$sudo -i```

```root@server$hostname```

output: 
```server.ldap.com```

### 2. Step - Install OpenLDAP
```root@server$apt-get install slapd ldap-utils -y``` or ```$yum install *openldap* -y```

give the Admin password for your server
and confirm

### 3. Step - Configure OpenLDAP

```root@server$nano /etc/ldap/ldap.conf```

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

```root@server$dpkg-reconfigure slapd```

Omit OpenLDAP server configuration:```No```

DNS domain name:```ldap.com```

Organization name:```ldap company``` 

admin pw: :```password```

confirm pw:```password```

(Database backend to use:```MDB```)

Do you want the database to be removed when slapd is purged?```No```

Move old database?```Yes```

(Allow LDAPv2 protocol?```No```)

### 5. Step - Check your Config

```root@server$ldapsearch -x``` or ```root@server$dpkg-reconfigure slapd```

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

```root@server$ldapsearch -x```

output:

```ldap_sasl_bind(SIMPLE): Can't contact LDAP server (-1)```

change ldap.conf

```root@server$nano /etc/ldap/ldap.conf```

restart the service

```root@server$ps aux |grep slapd```

```root@server$/etc/init.d/slapd restart```

```root@server$ps aux |grep slapd```

### 6. Step - install phpldapadmin

```root@server$apt-get install phpldapadmin -y```

### 7. Step - look at your ipv4 address

```urname@server$ifconfig```

look at inet addr for e.g.: 192.168.0.100

### 8. Step - Configure config.php

```root@server$nano /etc/phpldapadmin/config.php```

change those rows:

```$server->setvalue('server','name','Name of ur server');```

host = your ip address

```$server->setvalue('server','host','192.168.0.100');``` 

```$server->setvalue('server','base',array('dc=ldap,dc=com'));``` 

```$server->setvalue('login','bind_id','cn=admin,dc=ldap,dc=com');``` 

```// $config->custom->appearance['hide_template_warning'] - true;``` 

### 9. Step - restart service

```root@server$systemctl restart apache2```

### 10. Step - open website

```http://192.168.0.100/phpldapadmin/```

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

```gpg --keyserver pgpkeys.mit.edu --recv-key  ED444FF07D8D0BF6```
