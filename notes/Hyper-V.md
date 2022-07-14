## Hyper-VM Debian/Ubuntu

```powershell
New-VMSwitch -SwitchName "vNat" -SwitchType Internal
Get-NetAdapter -Name "vEthernet (vNat)" | Format-List # We will use InterfaceIndex
Get-NetAdapter -Name "vEthernet (vNat)" | Select-Object InterfaceIndex
```
List IPv4/NetMask to avoid using a registered interface

```powershell
Get-WmiObject Win32_NetworkAdapterConfiguration |  `
  Where IPEnabled | `
  Select IPSubnet, DNSServerSearchOrder, IpAddress
```   

Use a free 192.168.xxx.0 LAN prefix

```powershell
New-NetIPAddress -IPAddress 192.168.250.1 -PrefixLength 24 -InterfaceAlias "vEthernet (vNat)"
New-NetNat -Name lbNat -InternalIPInterfaceAddressPrefix 192.168.250.0/24
```

Just for checking if a network IP is set

```powershell
Get-NetIPAddress -InterfaceAlias "vEthernet (vNat)"
Get-NetNat
```

- Run "`virtmgmt.msc`" to start Hyper-V manager.
- Click "Actions"/"Hyper-V parameters".
- Change Virtual Disks place if default disk is too small.

There is no DHCP, no DNS. Use static configuration inside guest VM.

Fix issue that default interface is no longer correct:

```powershell
# List with `Get-NetIPInterface`, check InterfaceMetric is low for default/external route
Get-NetIPInterface | Sort-Object InterfaceMetric
Get-NetIPInterface | Where-Object AddressFamily -eq "Ipv4" | Sort-Object InterfaceMetric

Set-NetIPInterface -InterfaceAlias "Ethernet" -AutomaticMetric enabled
Set-NetIPInterface -InterfaceAlias "vEthernet (vNat)" -InterfaceMetric 5000
```