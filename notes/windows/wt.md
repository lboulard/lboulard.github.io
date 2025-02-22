# Install and configure Windows Terminal

- Project: <https://github.com/microsoft/terminal/>
- Reference: <https://docs.microsoft.com/en-us/powershell/module/appx/?view=win10-ps>

## Requirement

Universal runtime is required before installing Windows Terminal.
Once runtime installed, you can discard installation package from disk.

```dosbatch
▶Powershell -Command Invoke-WebRequest^
 -Uri "https://aka.ms/Microsoft.VCLibs.x64.14.00.Desktop.appx"^
 -OutFile Microsoft.VCLibs.x64.14.00.Desktop.appx
▶Powershell -Command Add-AppxPackage -Path "Microsoft.VCLibs.x64.14.00.Desktop.appx"
▶DEL Microsoft.VCLibs.x64.14.00.Desktop.appx
```

## Download

Download directly using PowerShell:

```dosbatch
▶Powershell -Command Invoke-WebRequest^
 -Uri "https://github.com/microsoft/terminal/releases/download/v1.18.3181.0/Microsoft.WindowsTerminal_1.22.10352.0_8wekyb3d8bbwe.msixbundle"^
 -OutFile Microsoft.WindowsTerminal_1.22.10352.0_8wekyb3d8bbwe.msixbundle
▶PowerShell -Command "(Get-FileHash Microsoft.WindowsTerminal_1.22.10352.0_8wekyb3d8bbwe.msixbundle -Algo SHA256).Hash"
FA08F1E5C41F7003BBE659444C6FE5E3F59F77730AB482DB44DEA8087C999225
```

## Install Windows Terminal

Run inside a vanilla `CMD.EXE` or PowerShell instance.

Install:

```dosbatch
▶Powershell -Command Add-AppxPackage -Path "Microsoft.WindowsTerminal_1.22.10352.0_8wekyb3d8bbwe.msixbundle"
```

## Remove Windows Terminal

Remove:

```dosbatch
▶Powershell -Command Remove-AppxPackage -Package "Microsoft.WindowsTerminal_1.22.10352.0_8wekyb3d8bbwe"
```

## More information

Information:

```dosbatch
▶Powershell -Command Get-AppPackage -name "Microsoft.WindowsTerminal"
```

File `settings.json` for Windows Terminal can be found in
`%LOCALAPPDATA%\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\LocalState`.

Custom icon can be used using URI like <ms-appdata:///roaming/console-blue-32.png>.
Location is in
`%LOCALAPPDATA%\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\RoamingState`.
