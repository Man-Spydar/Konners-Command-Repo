# File Commands

Common file and permission troubleshooting commands.

---

## Quick Reference
- [Permission Issues](#permission-issues)
  - [Unblock files](#unblock-files)
  - [Digital signature status](#digital-sig-check)
  - [Zone-ID / MOTW](#zone-idmotw)

---

## Permission Issues

### Unblock Files
To unblock a single file:

```powershell
Unblock-File "PATH_TO_FILE"
```

To unblock all files recursively starting at the provided path/folder:

```powershell
Get-ChildItem "PATH_TO_FOLDER" | Unblock-File
```

---

### Digital Sig. Check
Checks whether a file is digitally signed.

```powershell
(Get-AuthenticodeSignature "PATH_TO_FILE").Status
```

!!! note
    Common values are **Valid** (signed) and **NotSigned** (signature missing).

---

### Zone-ID/MOTW
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

---
