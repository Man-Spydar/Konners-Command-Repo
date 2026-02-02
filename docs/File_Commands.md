# File Commands
## Permission Issues
### Unblock Files
To unblock a singular file
```powershell
unblock-file "PATH_TO_FILE"
```
To unblock all files recursively starting at initially provided path/folder
```powershell
Get-childitem "PATH_TO_FOLDER" | unblock-file
```
________________________
### Digital Sig. Check

Checks whether a file is digitally signed.

```powershell
(Get-AuthenticodeSignature "PATH_TO_FILE").Status
```

!!! note
    Will return **"True"** if signed and **"False"** if signature is missing

________________________
### Zone-ID/MOTW:
This will return the source of the file:

~~~powershell
Get-Content -Path "PATH_TO_FILE" -Stream Zone.Identifier
~~~

??? note "Click Here for Zone-ID Mappings"
    **1** = Local Intranet |
    **2** = Trusted Sites |
    **3** = Internet |
    **4** = Restricted
________________________