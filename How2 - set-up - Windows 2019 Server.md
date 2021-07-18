# How2 - set-up - Windows 2019 Server


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
