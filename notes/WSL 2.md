#### Reduce virtual disk footprint

Inside WSL2:

```shell
sudo fstrim -v /
exit
```

In user `CMD.EXE` or PowerShell prompt:

```batch
wsl --shutdown
```

Find location of one of installations with those commands:

```powershell
Get-AppPackage -Name "TheDebianProject.DebianGNULinux"        # Debian GNU/Linux
Get-AppPackage -Name "CanonicalGroupLimited.UbuntuonWindows"  # Ubuntu 20.04
Get-AppPackage -Name "CanonicalGroupLimited.Ubuntu"           # Ubuntu
```

Then, in a PowerShell prompt with administrator rights (for example Debian GNU/Linux installation):

```powershell
Optimize-VHD -Path .\AppData\Local\Packages\TheDebianProject.DebianGNULinux_76v4gfsz19hv4\LocalState\ext4.vhdx -Mode Full
```

<https://docs.microsoft.com/en-us/powershell/module/hyper-v/optimize-vhd>
