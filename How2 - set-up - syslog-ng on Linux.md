# How2 - set-up - syslog-ng on Linux
## device 
OS: Raspbian GNU/Linux 10 (buster)

[syslog-ng documentation](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/53)

## change to root
```bash
sudo -i
```

## install syslog-ng
```bash
apt-get install syslog-ng -y
```

## check status
```bash
systemctl status syslog-ng.service
```

## nice to know commands start service
```bash
systemctl start syslog-ng.service
systemctl restart syslog-ng.service
systemctl stop syslog-ng.service
```
---

## Troubleshooting
```bash
Failed to start System Logger Daemon
systemctl status syslog-ng.service
	...
	Active: failed
	...
```

# Configure /usr/lib/systemd/system/syslog-ng.service
```bash
nano /usr/lib/systemd/system/syslog-ng.service
```

```bash
[Unit]
Description=System Logger Daemon
Documentation=man:syslog-ng(8)
After=network.target

[Service]
Type=notify
ExecStart=/usr/sbin/syslog-ng -F -p /var/run/syslogd.pid
ExecReload=/bin/kill -HUP $MAINPID
EnvironmentFile=-/etc/default/syslog-ng
EnvironmentFile=-/etc/sysconfig/syslog-ng
StandardOutput=journal
StandardOutput=journal
Restart=on-failure

[Install]
WantedBy=multi-user.target
Alias=syslog.service
```
---

## show syslog Logfile
```bash
tail -f /var/log/syslog
```

## how test syslog
### send logfile from local0
```bash
logger -p local0.info "my test message"
```

### which port does syslog listening to 
```bash
netstat -peanut | grep syslog-ng
```

```bash
logger -p local0.info --server 127.0.0.1 --tcp --port 601 "my message"
```


### test with python
```bash
chmod u+x pypilogger.py
```

## if you do any changes on config file use reload
```bash
systemctl reload syslog-ng.service
```

**systemctl restart syslog-ng.service sets logfile empty**

## create logrotate
create own rotation file
```bash
nano /etc/logrotate.d/myapp
```

```bash
#Logrotate for app
/var/log/myapp.log {
    copytruncate
    dateext
    dateformat _%d-%m-%Y
    extension .log
    daily
    missingok
    rotate 15
}
```

### check rotation
```bash
nano /etc/crontab
```

```bash
logrotate -d /etc/logrotate.conf 2> >(grep myapp.log -A 4)
```