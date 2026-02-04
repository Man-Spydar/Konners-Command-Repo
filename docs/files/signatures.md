# Digital Signatures

Checks for file signing status and signature metadata.

## Signature Status

Checks whether a file is digitally signed.

```powershell
(Get-AuthenticodeSignature "PATH_TO_FILE").Status
```

!!! note
    Common values are **Valid** (signed) and **NotSigned** (signature missing).
