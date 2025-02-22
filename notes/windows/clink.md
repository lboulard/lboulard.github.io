# Fancy Clink configuration

Clink try to bring Bash's powerful command line editing in `cmd.exe` of Windows.

- Clink homepage: <https://chrisant996.github.io/clink/>
- Clink documentation: <https://chrisant996.github.io/clink/clink.html>

This document describes installation and customization based on popular script found in Clink
documentation:

- Popular scripts: <https://chrisant996.github.io/clink/clink.html#popular-scripts>

When combined with [Microsoft Windows Teminal](wt.md), you can obtain a modern
look for old, venerable and clumsy `cmd.exe`.

[![Alt text](../images/WT_Clink.png)](wt.md)

## Prerequisites

In order to use Clink extensions, external tools are required, and, USER
environment variables shall be modified to find those tools.

Project pages for installed tools:

- Fuzzy Finder (`fzf`): <https://github.com/junegunn/fzf>
- File finder (`fd`): <https://github.com/sharkdp/fd>

### Programs installation folder

Scripts and command line use an environment variables `LBPROGRAMS` to reference
a folder to store user installation of tools and programs.

To define system wide `LBPROGRAMS` variable, use `SETX` in `cmd.exe`:

```dosbatch
SETX LBPROGRAMS "C:\lb\Programs"
```

### External tools installation

#### Fuzzy finder (`fzf`)

Quite handy tools to find a file or folder (or even a element in user data).

Download
[fzf-0.30.0-windows_amd64.zip](<https://github.com/junegunn/fzf/releases/download/0.30.0/fzf-0.30.0-windows_amd64.zip>).
Then, extract `fzf.exe` to `%LBPROGRAMS%\bin` folder using PowerShell:

```dosbatch
PowerShell -NoProfile -Command "Expand-Archive $Env:LBPROGRAMS\bin -LiteralPath fzf-0.30.0-windows_amd64.zip"
```

#### File finder (`fd`)

By default `fzf` use `DIR` command from `cmd.exe` to extract file list. File
finder `fd` is much faster on Windows and can be used with `fzf`.

Download
[fd-v8.4.0-x86_64-pc-windows-msvc.zip](https://github.com/sharkdp/fd/releases/download/v8.4.0/fd-v8.4.0-x86_64-pc-windows-msvc.zip).
Then extract `fd.exe` (located at `fd-v8.4.0-x86_64-pc-windows-msvc\fd.exe` in
archive) to `%LBPROGRAMS%\bin\fd.exe` location.


### Environment variables

Create global environment variable to change FZF to use `fd` for faster file search on Windows.

```dosbatch
SETX FZF_ALT_C_COMMAND "fd -t d"
SETX FZF_CTRL_T_COMMAND "fd -t f"
SETX FZF_DEFAULT_OPTS "--layout=reverse --inline-info"
```

Modify `PATH` using Windows configuration tool for USER environment variables.
You shall add `%LBPROGRAMS%\bin` to `PATH` USER variable.

## How Clink will be run

Create a file called `%LBPROGRAMS%\bin\profile.cmd` and run it at each console creation.

```dosbatch
PATH %LBPROGRAMS%\clink;%PATH%
clink inject -q --profile=%LBPROGRAMS%\clink_profile
```

Then create a profile in Windows Terminal using this command line as startup:

```dosbatch
cmd.exe /k "%LBPROGRAMS%\bin\profile.cmd"
```

Before this profile can be used, we need install Clink and all extensions. Then
configure them to obtain the fancy look of previous screenshot.

## Installation layout

| Location | Description |
|----------|-------------|
| `%LBPROGRAMS%\clink`         | Junction to latest clink installation |
| `%LBPROGRAMS%\clink.1.3.36`  | Clink installation                    |
| `%LBPROGRAMS%\clink_modules` | Clink extensions                      |
| `%LBPROGRAMS%\clink_profile` | Configuration files                   |

`%LBPROGRAMS%\clink` is file system junction to `%LBPROGRAMS%\clink.1.3.36`.
It is easier to update Clink to latest version by changing junction that
editing script files.

## Clink installation

Download
[clink.1.3.36.32d0bc.zip](https://github.com/chrisant996/clink/releases/download/v1.3.36/clink.1.3.36.32d0bc.zip).
Then, extract to `%LBPROGRAMS%\clink.1.3.36` folder using PowerShell:

```dosbatch
PowerShell -NoProfile -Command "Expand-Archive $Env:LBPROGRAMS\clink.1.3.36 -LiteralPath clink.1.3.36.32d0bc.zip"
```

Finally, use file system junction to link to current installation of Clink.

```dosbatch
MKLINK /J "%LBPROGRAMS%\clink" "%LBPROGRAMS%\clink.1.3.36"
```

> Let's try Clink, it already works.

As firs try of installation, Open `cmd.exe` from start menu and type
`profile.cmd`  — of course, as previously described, you shall have configure
`%LBPROGRAMS%\bin` in `PATH` USER environment variables. You can also try
previously setup profile `Clink` in Windows Terminal if installed.

Do not expect nothing fancy as configuration or extensions installation are
missing. Clink history between session for `cmd.exe` is already working.

## Clink customization

> TODO what re modified options ?

### Extensions in use

- clink-fzf: <https://github.com/chrisant996/clink-fz>
- clink-completions: <https://github.com/vladimir-kotikov/clink-completions>
- clink-flexprompt: <https://github.com/chrisant996/clink-flex-prompt>

### Quick setup

```dosbatch
MKDIR "%LBPROGRAMS%\clink_modules"
CD "%LBPROGRAMS%\clink_modules"

git clone https://github.com/vladimir-kotikov/clink-completions.git clink-completions
git clone https://github.com/chrisant996/clink-flex-prompt.git clink-flex-prompt
git clone https://github.com/chrisant996/clink-fzf.git clink-fzf

clink installscripts "%LBPROGRAMS%\clink_modules\clink-completions"
clink installscripts "%LBPROGRAMS%\clink_modules\clink-flex-prompt"
clink installscripts "%LBPROGRAMS%\clink_modules\clink-fzf"
```

### Clink fzf plugin configuration

Default keyboard shortcuts of fzf conflict with well used keyboard shortcut
command line shortcut of inputrc behaviour. So create or augment
`&LBPROGRAMS%\clink_profile\_inputrc` with this new keyboard shortcuts.

```conf
# Default key bindings for fzf with Clink.
"\S-\C-f":     "luafunc:fzf_file"       # Ctrl+T lists files recursively; choose one or multiple to insert them.
"\C-r":        "luafunc:fzf_history"    # Ctrl+R lists history entries; choose one to insert it.
"\M-c":        "luafunc:fzf_directory"  # Alt+C lists subdirectories; choose one to 'cd /d' to it.
"\M-b":        "luafunc:fzf_bindings"   # Alt+B lists key bindings; choose one to invoke it.
"\e[27;5;32~": "luafunc:fzf_complete"   # Ctrl+Space uses fzf to filter match completions.
```

### Clink flex prompt plugin configuration

Unfortunately, you have read directly source code of project to find
configuration of modules. Project is well written but this is a bit cumbersome
to read source code to find configuration options.

Create file `%LBPROGRAMS%\clink_profile\flexprompt_config.lua` with this content:

```lua
flexprompt = flexprompt or {}
flexprompt.settings = flexprompt.settings or {}
flexprompt.settings.tails = "flat"
flexprompt.settings.style = "rainbow"
flexprompt.settings.spacing = "compact"
flexprompt.settings.charset = "unicode"
flexprompt.settings.connection = "solid"
flexprompt.settings.left_prompt = "{battery:breakright}{histlabel}{cwd}{python:always:color=38;5;33,48;5;220}{git}"
flexprompt.settings.right_prompt = "{overtype}{exit}{duration}{time:format=%a %H:%M}"
flexprompt.settings.symbols =
{
    prompt =
    {
        ">",
        winterminal = "❯",
    },
}
flexprompt.settings.flow = "concise"
flexprompt.settings.powerline_font = true
flexprompt.settings.left_frame = "round"
flexprompt.settings.heads = "pointed"
flexprompt.settings.frame_color = "darkest"
flexprompt.settings.lines = "two"
flexprompt.settings.separators = "pointed"
flexprompt.settings.right_frame = "none"
flexprompt.settings.use_8bit_color = true

-- Replaces %HOME% with ~ in the cwd module.
flexprompt.settings.use_home_tilde = true

-- flexprompt does not reference correct environment variable
flexprompt.python_virtual_env_variable = "VIRTUAL_ENV_PROMPT"
```

## Update clink

This DOS batch script will install downloaded Clink ZIP archive found project
home age (<https://chrisant996.github.io/clink/>) or in releases page of GitHub
project (<https://github.com/chrisant996/clink/releases>).

```dosbatch
@SETLOCAL
@ECHO OFF
CD /D "%~dp0"

SET VERSION=notfound
SET ARCHIVE=
FOR %%f IN ("clink.*.zip") DO (
  FOR /F "tokens=2-4 delims=." %%i IN ("%%f") DO CALL :version "%%i" "%%j" "%%k" "%%f"
)
ECHO/VERSION=%VERSION%
IF %VERSION%==notfound GOTO :NagUser
ECHO/ARCHIVE=%ARCHIVE%

ECHO ON
PowerShell.exe -NoProfile -Command^
 "Expand-Archive -LiteralPath $env:ARCHIVE -DestinationPath $env:LBPROGRAMS\clink.$env:VERSION -Verbose -Force"
IF EXIST "%LBPROGRAMS%\clink" RD "%LBPROGRAMS%\clink"
MKLINK /J "%LBPROGRAMS%\clink" "%LBPROGRAMS%\clink.%VERSION%"
@ECHO OFF
@GOTO :NagUser

:version
SET "X=000000000%~1"
SET "Y=000000000%~2"
SET "Z=000000000%~3"
SET "__VERSION=%X:~-8%.%Y:~-8%.%Z:~-8%"
IF NOT "%_VERSION%"=="" (
 IF "%__VERSION%" LEQ "%_VERSION%" GOTO :EOF
)
SET "_VERSION=%__VERSION%"
SET "VERSION=%~1.%~2.%~3"
SET "ARCHIVE=%~4"
GOTO :EOF

:NagUser
:: Pause if not interactive
ECHO %cmdcmdline% | FIND /i "%~0" >NUL
IF NOT ERRORLEVEL 1 PAUSE
```
