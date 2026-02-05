# Appx / UWP Commands

## What are Appx packages?

Appx packages (also called UWP or Microsoft Store apps) are modern Windows applications that are installed and managed differently than traditional MSI or EXE programs.

Key characteristics:

- Installed per-user or provisioned system-wide
- Managed primarily through PowerShell
- Commonly used for built-in Windows apps and Store apps

These apps are not meant to be manually modified or removed through the file system.

## Where Appx packages are stored

Appx packages are registered to user profiles and stored in protected system locations.

Common locations:

- `C:\Program Files\WindowsApps` (highly restricted)
- User profile registrations (not directly browsable)

!!! note
    You should never manually modify files in these locations.
    PowerShell cmdlets are the supported and safe method.

## `Get-AppxPackage` (discovery and inspection)

Lists Appx packages installed for the current user.

```powershell
Get-AppxPackage
```

Returns information such as:

- `Name`
- `PackageFullName`
- `Version`
- `InstallLocation`

### Filter by name (most important)

You will almost never know the full package name. Wildcards are expected.

```powershell
Get-AppxPackage -Name "*example*"
```

Notes:

- `-Name` is case-insensitive
- Wildcards (`*`) are supported
- Use partial keywords to locate unknown packages

!!! tip
    Start broad (for example `"*photo*"`, `"*calc*"`, or `"*xbox*"`) and narrow down once you see results.

### View packages for all users

```powershell
Get-AppxPackage -AllUsers
```

Use this when:

- An app exists on the system but not for the logged-in user
- Troubleshooting shared or lab machines

!!! warning
    `-AllUsers` requires an elevated PowerShell session.

## `Reset-AppxPackage` (fix broken apps)

Resets an Appx package for the current user.

This action:

- Clears app data
- Re-registers the app
- Does **not** uninstall the app

### Reset a specific app

```powershell
Get-AppxPackage -Name "*example*" | Reset-AppxPackage
```

!!! note
    Resetting an app removes user-specific app data (settings, cached data, sign-ins).
    
    This is usually the first and safest fix for Store apps that will not open or behave incorrectly.

## `Remove-AppxPackage` (use with caution)

Removes an Appx package for the current user only.

```powershell
Get-AppxPackage -Name "*example*" | Remove-AppxPackage
```

Important behavior:

- Only removes the app for the current user
- The app may reappear if it is provisioned system-wide

!!! warning
    Removing built-in Windows apps can break features or future updates.
    Do not remove apps unless you understand the impact.

## Provisioned Appx packages (why apps come back)

Some Appx packages are provisioned into Windows images so they are automatically installed for new user profiles.

This is why:

- An app may return after removal
- New users receive apps that were previously removed for others

Common related cmdlets (advanced usage):

```powershell
Get-AppxProvisionedPackage
Remove-AppxProvisionedPackage
```

!!! note
    Provisioned package changes affect future users, not existing profiles.

## Common Appx package names

### Common built-in apps

Examples:

- `Microsoft.WindowsCalculator`
- `Microsoft.Windows.Photos`
- `Microsoft.WindowsStore`
- `Microsoft.MicrosoftEdge.Stable`
- `Microsoft.WindowsNotepad`

### Commonly removed or troublesome apps

Examples:

- `Microsoft.XboxApp`
- `Microsoft.XboxGamingOverlay`
- `Microsoft.GetHelp`
- `Microsoft.Getstarted`
- `Microsoft.MicrosoftSolitaireCollection`

!!! tip
    Always verify package names with:

    ```powershell
    Get-AppxPackage -Name "*keyword*"
    ```

    Package names can change between Windows versions.

## Quick troubleshooting flow

- App will not open → `Reset-AppxPackage`
- App missing → `Get-AppxPackage -AllUsers`
- App keeps returning → Provisioned package exists
- Store apps broken system-wide → Escalate
