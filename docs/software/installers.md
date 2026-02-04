# MSI & EXE Installs

Pre-made installers and packaging commands.

## CMAKE

??? note "PS-EXEC Installer"
    ```powershell
    $ComputerName = "COMP_01"
    $RemoteMsi = "LOCATION_OF_INSTALLER"
    psexec "\\$ComputerName" -s -h cmd /c "msiexec /i $RemoteMsi INSTALL_ROOT=C:\Apps\CMAKE ALLUSERS=1 ADD_CMAKE_TO_PATH=System /qn /norestart /L*v $LogFile"
    ```

---

## Cloud Admin PowerShell Modules

### Microsoft Graph PowerShell SDK

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
Connect-MgGraph \
  -Scopes "Group.ReadWrite.All","Directory.AccessAsUser.All" \
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

### Azure AD (Entra ID – Legacy Module)

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

### Exchange Online Management Module

```powershell
Install-Module -Name "ExchangeOnlineManagement" -Force
Import-Module "ExchangeOnlineManagement"
```

```powershell
Connect-ExchangeOnline -ExchangeEnvironmentName O365USGovGCCHigh
```
