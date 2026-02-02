# PsExec

PsExec is a command-line utility from the Sysinternals suite that allows execution of processes on local or remote Windows systems by temporarily installing a service (`PSEXESVC`) on the target machine.

________________________

## Basic Usage

### Syntax
~~~powershell
PsExec \\COMPUTER [options] command [arguments]
~~~

### Local Execution
~~~powershell
PsExec cmd.exe
~~~

### Remote Execution
~~~powershell
PsExec \\PC01 cmd.exe
~~~

### Multiple Targets
~~~powershell
PsExec \\PC01,PC02,PC03 cmd.exe
~~~

!!! note
    Requires local administrator rights on the target system(s)

________________________

## Authentication Flags

### -u (Username)

Specifies the user account used for execution.

~~~powershell
PsExec \\PC01 -u DOMAIN\User cmd.exe
~~~

### -p (Password)

Specifies the password for the provided user.

~~~powershell
PsExec \\PC01 -u DOMAIN\User -p Password123 cmd.exe
~~~

!!! warning
    Passwords are passed in plaintext on the command line. Avoid in reusable scripts where possible.

________________________

## Privilege & Execution Context

### -s (SYSTEM)

Runs the command under the Local SYSTEM account.

~~~powershell
PsExec \\PC01 -s cmd.exe
~~~

Commonly used for:
- SYSTEM-level registry changes  
- Service or driver work  
- Tools that must run as SYSTEM  

### -h (Elevated)

Runs the process elevated on systems with UAC enabled.

~~~powershell
PsExec \\PC01 -h powershell.exe
~~~

Use when:
- UAC is enabled  
- You need full admin rights for the process  

### -i (Interactive)

Runs the process interactively on the remote desktop (visible to the logged-on user).

~~~powershell
PsExec \\PC01 -i notepad.exe
~~~

!!! note
    Use `-i` when you need a GUI application to appear on the user’s session (for example Notepad, installers, or other GUI tools).

________________________

## Execution Behavior Flags

### -d (Non-blocking / Don't Wait)

Does not wait for the process to exit (fire-and-forget).

~~~powershell
PsExec \\PC01 -d notepad.exe
~~~

### -w (Working Directory)

Sets the working directory for the process.

~~~powershell
PsExec \\PC01 -w C:\Temp cmd.exe
~~~

### -low (Low Priority)

Runs the process at low CPU priority.

~~~powershell
PsExec \\PC01 -low powershell.exe
~~~

________________________

## File Transfer Flags

### -c (Copy Executable)

Copies the specified program to the remote system before execution.

~~~powershell
PsExec \\PC01 -c C:\Tools\Diag.exe
~~~

The file is copied to the remote system and deleted when the process exits (unless you specify otherwise).

### -f (Force Overwrite)

Forces overwrite of the remote file if it already exists (used with `-c`).

~~~powershell
PsExec \\PC01 -c -f C:\Tools\Diag.exe
~~~

### -v (Version Check)

Copies the file only if the local version is newer than the remote version (used with `-c`).

~~~powershell
PsExec \\PC01 -c -v C:\Tools\Diag.exe
~~~

________________________

## Connection & Service Options

### -accepteula

Automatically accepts the Sysinternals EULA.  
Useful for scripting or first-time use on a system.

~~~powershell
PsExec -accepteula \\PC01 cmd.exe
~~~

### -n (Timeout)

Specifies the connection timeout in seconds.

~~~powershell
PsExec \\PC01 -n 10 cmd.exe
~~~

### -r (Service Name)

Specifies the name of the remote service PsExec creates, instead of the default `PSEXESVC`.

~~~powershell
PsExec \\PC01 -r TempExecSvc cmd.exe
~~~

________________________

## Environment & Session Options

### -e

Does not load the specified account’s profile or environment (runs with a minimal environment).

~~~powershell
PsExec \\PC01 -e cmd.exe
~~~

________________________

## Common Example Scenarios

### Open a SYSTEM Command Prompt on a Remote Machine

~~~powershell
PsExec \\PC01 -s cmd.exe
~~~

________________________

### Run PowerShell as SYSTEM (Detached)

Runs PowerShell as SYSTEM and does not wait for it to exit.

~~~powershell
PsExec \\PC01 -s -d powershell.exe
~~~

________________________

### Run a PowerShell Script on Multiple Machines

~~~powershell
PsExec \\PC01,PC02,PC03 -h powershell.exe -File C:\Scripts\Remediation.ps1
~~~

________________________

### Copy and Execute a Tool on a Remote Machine

~~~powershell
PsExec \\PC01 -c C:\Tools\NetDiag.exe
~~~

________________________

## Requirements & Network Notes

- Target system must allow **service creation** and **SMB/RPC** access.  
- TCP port **445 (SMB)** and related RPC traffic must be reachable.  
- Account used must have **local admin** rights on the target.  

!!! warning
    PsExec activity is often monitored or blocked by EDR and security tools because it is frequently abused for lateral movement.

________________________

## Quick Reference

### Most Common Pattern

Run an elevated SYSTEM PowerShell session on a remote machine with EULA auto-accepted:

~~~powershell
PsExec -accepteula \\PC01 -s -h powershell.exe
~~~

Key points:
- `-accepteula` → avoids the interactive license prompt  
- `-s` → run as SYSTEM  
- `-h` → elevate on UAC-enabled systems  

________________________
