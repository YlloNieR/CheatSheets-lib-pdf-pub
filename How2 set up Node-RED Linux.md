
## install Node-RED
```bash
apt-get install nodered
```

```bash
node-red-start
node-red-stop
node-red-restart
node-red-log 
```

## Autostart on boot
```bash
sudo systemctl enable nodered.service
```

## Default config
```bash
127.0.0.1:1880
```

on pi IP you get access from another client of the same network

## How to install addons e.g. Dashboard

Burger Menu > Manage Palette > install > Search for "Dashboard"> Select "node-red-dashboard" > install


## if you want to change the default config
```bash
nano /etc/hostname
```
from @raspberry to @hostnameThatYouWant

```bash
usermod -l newUserName oldUserName
```
from pi to usernameYouWant

```bash
usermod -aG sudo usernameYouWant
```
from user to superuser

```bash
sudo adduser usernameYouWant video
```
adds usernameYouWant to videogroup

```bash
nano /etc/hosts
127.0.0.1   localhost
...
...
192.168.0.10    ipThatYouWant
```
