# Azure Cloud Admin PowerShell

Use these commands to prepare an admin workstation and connect to cloud services through PowerShell.

## Microsoft Graph PowerShell SDK

??? info "Install Graph Module"
    Only needed if you have never installed MS-Graph.
    ```powershell
    Install-Module -Name "Microsoft.Graph" -Force
    ```

```powershell
Import-Module "Microsoft.Graph"
```

### Connect to Microsoft Graph (US Gov)

```powershell
Connect-MgGraph `
  -Scopes "Group.ReadWrite.All","Directory.AccessAsUser.All" `
  -Environment USGov
```

!!! warning
    Exercise extreme caution when connecting to MS-Graph with "Write" permissions as any change you push will go into effect.

??? note "Click for User/Group Read & Write"
    ```powershell
    Connect-MgGraph -Scopes "User.ReadWrite.All","Group.ReadWrite.All" -Environment USGov
    ```

!!! info
    A new tab in your default browser will open prompting you to sign in. You should use your "onmicrosoft" account as your other account will not have useful permissions.

---

### Getting LAPS Passwords (MS-Graph Required)

This section is specifically for use powershell to directly pull a device's LAPS password from Azure

??? info "Requirements (MS-Graph)"
    You must import and connect to MS-graph in powershell before you an pull a device's LAPS password. WHen prompted you must use your `onmicrosoft` account to authenticate
    
    ```powershell
    Import-Module "Microsoft.Graph"
    Connect-MgGraph `
        -Scopes "Group.ReadWrite.All","Directory.AccessAsUser.All" `
        -Environment USGov
    ```

    !!! warning
        The above command will only work if you have already installed the MS-graph module prior to running them

```powershell
get-lapsaadpassword -deviceid COMPUTER_NAME -includepassword -asplaintext
```

!!! note "-IncludePassword Argument"
    The -AsPlainText argument will have no effect without using the new -Includepassword argument

## Azure AD (Entra ID – Legacy Module)

```powershell
Install-Module -Name "AzureAD" -Force
Import-Module "AzureAD" -UseWindowsPowerShell
```

!!! note
    This only installs/imports the module. You still need to connect using the command below.

```powershell
Connect-AzureAD -AzureEnvironmentName AzureUSGovernment
```

---

## Exchange Online Management Module

```powershell
Install-Module -Name "ExchangeOnlineManagement" -Force
Import-Module "ExchangeOnlineManagement"
```

```powershell
Connect-ExchangeOnline -ExchangeEnvironmentName O365USGovGCCHigh
```
