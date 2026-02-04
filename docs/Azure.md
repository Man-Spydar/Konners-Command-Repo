# Azure

Commands specific to working with Azure cloud resources and identity services  
(US Government / GCC High)

---

## **Microsoft Graph**

### Microsoft Graph PowerShell SDK
Needed for all modern Azure / Entra ID management via Graph.

??? info "Install Graph Module"
    Only needed if you have never installed MS-Graph
    ~~~powershell
    Install-Module -Name "Microsoft.Graph" -Force
    ~~~

~~~powershell
Import-Module "Microsoft.Graph"
~~~

---

### Connect to Microsoft Graph (US Gov)
Needed when managing Entra ID users, groups, and directory objects via Microsoft Graph.

~~~powershell
Connect-MgGraph `
  -Scopes "Group.ReadWrite.All","Directory.AccessAsUser.All" `
  -Environment USGov
~~~

!!! warning
    Exercise extreme caution when connecting to MS-Graph with "Write" permissions as any change you push will go into effect

??? note "Click for User/Group Read & Write"
    ~~~powershell
    Connect-MgGraph -Scopes "User.ReadWrite.All","Group.ReadWrite.All" -Environment USGov
    ~~~

!!! info
    A new tab in your default browser will open prompting you to sign in. You should use your "onmicrosoft" account as your other account will not have useful permissions.
---

### Verify Graph Connection Context
Used to confirm tenant, environment, and granted scopes.

~~~powershell
Get-MgContext
~~~

---

## Azure AD (Entra ID – Legacy Module)

### AzureAD Module (Import & Install)
Needed when performing legacy Entra ID operations not yet fully available in Microsoft Graph.

~~~powershell
Install-Module -Name "AzureAD" -Force
Import-Module "AzureAD" -UseWindowsPowerShell
~~~

!!! note
    This just installs and imports the module, you still need to **connect using the below command**

---

### Connect to Azure AD (US Gov)
Used for direct Azure AD authentication in GCC High.

~~~powershell
Connect-AzureAD -AzureEnvironmentName AzureUSGovernment
~~~

---

## Exchange Online

### Exchange Online Management Module
Needed when administering Exchange Online mailboxes, permissions, and transport rules.

~~~powershell
Install-Module -Name "ExchangeOnlineManagement" -Force
Import-Module "ExchangeOnlineManagement"
~~~

---

### Connect to Exchange Online (GCC High)
Used for managing Exchange Online in US Government tenants.

~~~powershell
Connect-ExchangeOnline -ExchangeEnvironmentName O365USGovGCCHigh
~~~

---

