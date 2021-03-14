
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

Burger Menu > Manage Palette > install > Search for "Dashboard"> Select "node-red-dashboard" > install