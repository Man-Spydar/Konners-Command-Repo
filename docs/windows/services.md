# Services & Scheduled Tasks

## Overview

Windows Services and Scheduled Tasks control the majority of background behavior on a system.

Common use cases:

- Troubleshooting application failures
- Verifying security agents are running
- Restarting stuck services
- Identifying tasks that run at startup or on a schedule

PowerShell is the fastest and safest way to inspect and manage both.

## Windows Services

### Get-Service (Basic Query)

Lists all services on the system.

```powershell
Get-Service
```

Returns:

- Service name
- Display name
- Status (Running / Stopped)
- Startup type (limited info)

### Query a Specific Service by Name

```powershell
Get-Service -Name "Spooler"
```

Service names are not always the same as display names.

!!! tip
    If you only know part of the name, use wildcards:

    ```powershell
    Get-Service -Name "*print*"
    ```

### Query by Display Name

```powershell
Get-Service | Where-Object DisplayName -like "*print*"
```

Useful when:

- You see the service name in the Services GUI
- Vendor service names are unclear

### Check Service Status Quickly

```powershell
(Get-Service -Name "Spooler").Status
```

Returns a simple status value suitable for scripts or spot checks.

### Start, Stop, and Restart Services

Start a service:

```powershell
Start-Service -Name "Spooler"
```

Stop a service:

```powershell
Stop-Service -Name "Spooler"
```

Restart a service:

```powershell
Restart-Service -Name "Spooler"
```

!!! warning
    Stopping critical services can disconnect users or break system functionality.
    Always confirm impact before stopping services on production machines.

### Services That Fail to Start (Extended Info)

```powershell
Get-CimInstance Win32_Service |
Where-Object Name -eq "Spooler" |
Select-Object Name, State, StartMode, PathName
```

Useful for:

- Verifying executable path
- Identifying permission issues
- Checking startup configuration

### Common Service Troubleshooting Commands

#### List All Stopped Services

```powershell
Get-Service | Where-Object Status -eq "Stopped"
```

#### List Automatic Services That Are Not Running

```powershell
Get-Service |
Where-Object { $_.StartType -eq "Automatic" -and $_.Status -ne "Running" }
```

!!! note
    These services are expected to be running and are often the root cause of user issues.

#### Restart Multiple Related Services

Example (Print-related services):

```powershell
"Spooler","PrintNotify" | ForEach-Object {
    Restart-Service -Name $_ -ErrorAction SilentlyContinue
}
```

## Scheduled Tasks

### Get-ScheduledTask (Basic Query)

Lists all scheduled tasks.

```powershell
Get-ScheduledTask
```

Returns:

- Task name
- Task path
- State

### Find Tasks by Name

```powershell
Get-ScheduledTask -TaskName "*update*"
```

### Find Tasks by Path

```powershell
Get-ScheduledTask -TaskPath "\\Microsoft\\Windows\\*"
```

Task paths are critical — many system tasks share similar names.

### Check Task State

```powershell
Get-ScheduledTask -TaskName "ExampleTask" |
Select-Object TaskName, State
```

### View Task Details (Actions & Triggers)

```powershell
Get-ScheduledTask -TaskName "ExampleTask" |
Get-ScheduledTaskInfo
```

Returns:

- Last run time
- Last result code
- Next run time

### Run, Disable, or Enable Scheduled Tasks

Run a task manually:

```powershell
Start-ScheduledTask -TaskName "ExampleTask"
```

Disable a task:

```powershell
Disable-ScheduledTask -TaskName "ExampleTask"
```

Enable a task:

```powershell
Enable-ScheduledTask -TaskName "ExampleTask"
```

!!! warning
    Disabling system or security-related tasks can cause compliance failures or break updates.
    Only disable tasks when explicitly approved.

### Common Scheduled Task Troubleshooting

#### Tasks That Failed Last Run

```powershell
Get-ScheduledTask |
ForEach-Object {
    $info = Get-ScheduledTaskInfo $_.TaskName -ErrorAction SilentlyContinue
    if ($info.LastTaskResult -ne 0) {
        [PSCustomObject]@{
            TaskName = $_.TaskName
            LastRun  = $info.LastRunTime
            Result   = $info.LastTaskResult
        }
    }
}
```

#### Tasks Running Under Unexpected Accounts

```powershell
Get-ScheduledTask |
Select-Object TaskName, @{Name="RunAs";Expression={$_.Principal.UserId}}
```

Useful for:

- Credential-related failures
- Tasks failing after password changes
