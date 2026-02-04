# Unblocking & Zone-ID

Commands for clearing Mark of the Web (MOTW) flags and inspecting Zone-ID metadata.

## Unblock Files

To unblock a single file:

```powershell
Unblock-File "PATH_TO_FILE"
```

To unblock all files recursively starting at the provided path/folder:

```powershell
Get-ChildItem "PATH_TO_FOLDER" | Unblock-File
```

---

## Zone-ID / MOTW

Returns the source zone for the file (Mark of the Web).

```powershell
Get-Content -Path "PATH_TO_FILE" -Stream Zone.Identifier
```

??? note "Zone-ID mappings"
    | Zone ID | Source |
    | --- | --- |
    | 1 | Local Intranet |
    | 2 | Trusted Sites |
    | 3 | Internet |
    | 4 | Restricted |
