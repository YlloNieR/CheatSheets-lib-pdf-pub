# How2 - Secure Linux Server
On Windows
## connect with Powershell
```Powershell
ssh USERNAMELINUX@10.10.10.1
```

## Enable Automatic Updates
#### manually way
```bash
sudo apt update && sudo apt dist-upgrade -y
```

#### Option 1
```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure --priority=low unattended-upgrades 
```

#### Option 2
```bash

```

## alternate login as root -> Create a new user
```bash
adduser USERNAMELINUX
```

#### Add User to sudo group
```bash
usermod -aG sudo USERNAMELINUX
```

## remove password prompt, set private key pair
```bash
mkdir ~/.ssh && chmod 700 ~/.ssh 
logout
```

#### create ssh key & send it to client by secure copy protocol
```Powershell
ssh-keygen -b 4096
scp $env:USERPROFILE/.ssh/id_rsa.pub USERNAMELINUX@10.10.10.1:~.ssh/authorized_keys
```
From Now on you don't need a password for ssh for USERNAMELINUX@10.10.10.1

#### lock the linux machine down
```Powershell
ssh USERNAMELINUX@10.10.10.1
```
```bash
sudo nano /etc/ssh/sshd_config   
```

on default looks like:

```nano
#       $OpenBSD: sshd_config,v 1.103 2018/04/09 20:41:22 tj Exp $

# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# This sshd was compiled with PATH=/usr/bin:/bin:/usr/sbin:/sbin

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

#Port 22
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::

#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none

# Logging
#SyslogFacility AUTH
#LogLevel INFO

# Authentication:

#LoginGraceTime 2m
#PermitRootLogin prohibit-password
#StrictModes yes
#MaxAuthTries 6
#MaxSessions 10

#PubkeyAuthentication yes

# Expect .ssh/authorized_keys2 to be disregarded by default in future.
#AuthorizedKeysFile     .ssh/authorized_keys .ssh/authorized_keys2

#AuthorizedPrincipalsFile none

#AuthorizedKeysCommand none
#AuthorizedKeysCommandUser nobody

# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
#HostbasedAuthentication no
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
#IgnoreUserKnownHosts no
# Don't read the user's ~/.rhosts and ~/.shosts files
#IgnoreRhosts yes

# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no

# Change to yes to enable challenge-response passwords (beware issues with
# some PAM modules and threads)
ChallengeResponseAuthentication no

# Kerberos options
#KerberosAuthentication no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes
#KerberosGetAFSToken no

# GSSAPI options
#GSSAPIAuthentication no
#GSSAPICleanupCredentials yes
#GSSAPIStrictAcceptorCheck yes
#GSSAPIKeyExchange no

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
UsePAM yes

#AllowAgentForwarding yes
#AllowTcpForwarding yes
#GatewayPorts no
X11Forwarding yes
#X11DisplayOffset 10
#X11UseLocalhost yes
#PermitTTY yes
PrintMotd no
#PrintLastLog yes
#TCPKeepAlive yes
#PermitUserEnvironment no
#Compression delayed
#ClientAliveInterval 0
#ClientAliveCountMax 3
#UseDNS no
#PidFile /var/run/sshd.pid
#MaxStartups 10:30:100
#PermitTunnel no
#ChrootDirectory none
#VersionAddendum none

# no default banner path
#Banner none

# Allow client to pass locale environment variables
AcceptEnv LANG LC_*

# override default of no subsystems
Subsystem       sftp    /usr/lib/openssh/sftp-server

# Example of overriding settings on a per-user basis
#Match User anoncvs
#       X11Forwarding no
#       AllowTcpForwarding no
#       PermitTTY no
#       ForceCommand cvs server
```

- change Port number
- set AdressFamily to IPv4 only
- disallow PermitRootLogin
- disallow PasswordAuthentification
- save changes
- restart daemon
```nano
...
Port 717
AdressFamily inet
...
PermitRootLogin prohibit-password
...
PasswordAuthentication no
PermitEmptyPasswords no
...

```
```bash
sudo systemctl restart sshd.service
```

#### check in ANOTHER terminal if all is ok
- test port 22
```Powershell
ssh USERNAMELINUX@10.10.10.1
```
should not work

```Powershell
ssh USERNAMELINUX@10.10.10.1 -p 717
```
should work without password prompt

## set a friewall
check your open ports
```bash
sudo ss -tupln
```
install uncomplicated firewall
```bash
sudo apt install ufw
```
check status
```bash
sudo ufw status
```
set your ports you want dou allow
```bash
sudo ufw allow 717
```
enable ufw
```bash
sudo ufw enable
```
check status
```bash
sudo ufw status
```

## disallow ping
```bash
sudo nano /etc/ufw/before.rules
```
```nano
...
# ok icmp codes for INPUT 
-A ufw-before-input -p icmp --icmp-type destination-unreachable -j ACCEPT
-A ufw-before-input -p icmp --icmp-type time-exceeded -j ACCEPT   
...
```
add line
```nano
...
# ok icmp codes for INPUT 
-A ufw-before-input -p icmp --icmp-type echo-request -j DROP
-A ufw-before-input -p icmp --icmp-type destination-unreachable -j ACCEPT
-A ufw-before-input -p icmp --icmp-type time-exceeded -j ACCEPT   
...
```
restart
```bash
sudo reload
```