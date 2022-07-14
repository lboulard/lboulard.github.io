## Upgrade PowerShell

PowerShell 5.1 is not maintenance only and documentation is no longer accessible.

### MSI installation
Upgrade in Windows 10 from PowerShell 5.1. Installation shall provided latest version from PowerShell 7.2 branch.

Run inside a PowerShell with administrative rights:

```powershell
Invoke-Expression "& { $(irm https://aka.ms/install-powershell.ps1) } -UseMSI"
```

Reference: <https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows>

### With `winget`

Or, using `winget` if installed.

```powershell
winget search powershell
winget install powershell
winget upgrade powershell
```

## Unrestricted local usage

```powershell
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
```

## Profile files locations

### PowerShell 5.1

`$psHome` is `$env:SystemRoot\System32\WindowsPowerShell\v1.0`.

| Profile Path | Scope |
|:------------|--------|
| `$Home\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1` | Current user, Current Host |
| `$Home\Documents\WindowsPowerShell\Profile.ps1` | Current User, All Hosts |
| `$PsHome\Microsoft.PowerShell_profile.ps1` | All Users, Current Host |
| `$PsHome\Profile.ps1` | All Users, All Hosts |

### PowerShell 7

`$psHome` is `$env:ProgramFiles\PowerShell\7`.

| Profile Path | Scope |
|:------------|--------|
| `$Home\Documents\PowerShell\Microsoft.PowerShell_profile.ps1` | Current user, Current Host |
| `$Home\Documents\PowerShell\Profile.ps1` | Current User, All Hosts |
| `$PsHome\Microsoft.PowerShell_profile.ps1` | All Users, Current Host |
| `$PsHome\Profile.ps1` | All Users, All Hosts |

## Install `posh-git`

```powershell
Set-PSRepository -Name PSGallery -InstallationPolicy Trusted
PowerShellGet\Install-Module posh-git -Scope CurrentUser -Force
```

Then later  to update module:

```powershell
PowerShellGet\Update-Module posh-git
```
