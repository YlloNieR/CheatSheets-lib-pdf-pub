# Troubleshoots

## How to import keys from a keyserver using gpg in debian? / Public key error / Fix apt-get update “the following signatures couldn’t be verified because the public key is not available”
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

---
