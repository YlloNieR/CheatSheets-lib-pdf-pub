# configure Cisco switch
## connect to switch by PuTTY
- get PuTTY [PuTTY](https://www.putty.org/)
- connect by 
  - RS-232 cable or 
  - RJ45 cable at the back of switch which is named CONSOLE or
  - USB to RS-232 cable
- look for a new USB Serial Port connection like (COM4) - devmgmt.msc
- set PuTTY
#### USB to RS-232 cable
  - Session > Serial line - COM4 | Speed 9600 | choose Serial
  - Serial > Serial line to connect to - COM4 | Speed 9600 | Data bits 8 | Stop bits 1 | Parity None Flow Control XON/XOFF

#### RS-232 cable
  - Session > Serial line - COM1 | Speed 115200 | choose Serial
  - Serial > Serial line to connect to - COM1 | Speed 115200 | Data bits 8 | Stop bits 1 | Parity None Flow Control XON/XOFF

- connect with PuTTY
- login by default (cisco|cisco)
```cisco
User Name:cisco
Password:******
```

## configure Hostname
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname Switch1
```
## configure Mgmt IP Address
```cisco
Switch1(config)# interface vlan 1
Switch1(config)# ip address 192.168.5.25 255.255.255.0
Switch1(config)# exit
```
## configure IP Default-Gateway
```cisco
Switch1(config)# ip default-gateway 192.168.5.254
```
## configure Local User & Password
```cisco
Switch1(config)# username user1 password cisco
```
## configure Enable Secret
hashes password for login
```cisco
Switch1(config)# enable secret cisco
```
## configure Console & VTY (Virtual teletype) Logins to Router / Switch
```cisco
Switch1(config)# line console 0
Switch1(config-line)# password cisco
Switch1(config-line)# exit
```
Line vty 0 4 = 5 simultaneous virtual connections
Line vty 0 15 = 16 simultaneous virtual connections Maximum allowed values
```cisco
Switch1(config)# line vty 0 4
Switch1(config-line)# password cisco
Switch1(config-line)# login
Switch1(config-line)# exit
```
## configure Exec-Timeout
configures inactive session timeout in min for the vty 0 port
```cisco
Switch1(config)# line vty 0 15
Switch1(config)# exec-timeout 0 30
Switch1(config)# exit
```
## configure Pasword Encryption
```cisco
Switch1(config)# service password-encryption
Switch1(config)# exit
```
## check your configuration
```cisco
Switch1# show running-config
```
## configure Copy Run Start
```cisco
Switch1# copy running-config startup-config
Switch1# exit
```
## configure VLAN on port
VLAN1 default for router
VLAN 30 for DC
```cisco
Switch1# show vlan

Switch1# conf t
Switch1# int <port number>
Switch1# switchport mode access
Switch1# switchport access vlan <ID>
Switch1# no shut
Switch1# end

Switch1# show vlan
```

# configure Firewall / Router
wf.msc
```powershell
Get-NetAdapter -Name <Ethernet Name>
Rename-NetAdapter -Name <Ethernet Name> -NewName <new Ethernet Name>

Get-NetIPConfiguration -InterfaceAlias <new Ethernet Name> -Detailed

Get-NetIPInterface
```
set IP
```powershell
$ipParams = @{
InterfaceIndex = 8
IPAddress = "192.168.2.50"
PrefixLength = 24
AddressFamily = "IPv4"
}
New-NetIPAddress @ipParams
```
