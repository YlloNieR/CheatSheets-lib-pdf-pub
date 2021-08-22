## How2 - SMTP Server on Linux
sSMTP | postfix | sendmail

## Choose your port

**Port**  | **Zweck**                       |  **TLS** | **SSL**
----------|---------------------------------|----------|------
25        | Simple Mail Transfer Protocol   | optional | No
80        | Hypertext Transfer Protocol     | optional | No
465       | authenticates SMTP via SSL      | No       | Ja
587 / 588 | E-Mail transfer                 | optional | No
2525      | alternate                       | optional | No

587 recommended

## sSMTP
```bash
sudo apt-get update -y && sudo apt-get upgrade -y && sudo apt-get autoremove -y
sudo apt-get install ssmtp mailutils -y
```

## configure sSMTP
### Option 1
```bash
sudo nano /etc/ssmtp/ssmtp.conf
```
```nano
#
# Config file for sSMTP sendmail  
#
# Config file for sSMTP sendmail  
# Make this empty to disable rewriting. 
root=USER@gmx.de 
user=USER@gmx.de

# The place where the mail goes. The actual machine name is required no  
# MX records are consulted. Commonly mailhosts are named mail.domain.com 
mailhub=mail.gmx.net:465

# Where will the mail seem to come from? 
rewriteDomain=gmx.net    

# The full hostname 
hostname=gmx.net

# TLS | TTLS
UseTLS=YES 
# UseSTARTTLS=YES 

# Are users allowed to set their own From: address?
# YES - Allow the user to specify their own From: address 
# NO - Use the system generated From: address
FromLineOverride=No     

# Authentication
AuthUser=USER@gmx.com
AuthPass=PASSWORD
```
```bash
nano /etc/ssmtp/revaliases
```
```nano
# sSMTP aliases 
#
# Format: local_account:outgoing_address:mailhub 
#
# Example: root:your_login@your.domain:mailhub.your.domain[:port]
# where [:port] is an optional port number that defaults to 25.
root:USER@gmx.com:mail.gmx.net:465
pi:USER@gmx.com:mail.gmx.net:465
#www-data:USER@gmx.com:mail.gmx.net:465
```

### Option 2
```nano
#
# Config file for sSMTP sendmail  
#
# Config file for sSMTP sendmail  
# Make this empty to disable rewriting. 
root=USER@gmx.de 
pi=USER@gmx.de

# The place where the mail goes. The actual machine name is required no  
# MX records are consulted. Commonly mailhosts are named mail.domain.com 
mailhub=mail.gmx.net:587

# Where will the mail seem to come from? 
rewriteDomain=gmx.net    

# The full hostname 
hostname=gmx.net

# TLS | TTLS
UseTLS=YES 
UseSTARTTLS=YES 

# Are users allowed to set their own From: address?
# YES - Allow the user to specify their own From: address 
# NO - Use the system generated From: address
FromLineOverride=No     

# Authentication
AuthUser=USER@gmx.com
AuthPass=PASSWORD
AuthMethod=LOGIN
```
```bash
nano /etc/ssmtp/revaliases
```
```nano
# sSMTP aliases 
#
# Format: local_account:outgoing_address:mailhub 
#
# Example: root:your_login@your.domain:mailhub.your.domain[:port]
# where [:port] is an optional port number that defaults to 25.
root:USER@gmx.com:mail.gmx.net:587
pi:USER@gmx.com:mail.gmx.net:587
#www-data:USER@gmx.com:mail.gmx.net:465
```

#### test your configuration
###### Option 1
```
ssmtp -v echo@tu-berlin.de
```
- press CTRL + d

###### Option 2
```
echo "Mail-Content" | mail -s "Subject" USER2@gmail.com
```

