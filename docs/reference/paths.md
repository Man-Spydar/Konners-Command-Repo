# Common Paths

Frequently used Windows paths and locations.

## Bookmarks

### Chrome Bookmarks
Chrome bookmarks are located in the below folder and are named `bookmarks` & `bookmarks.bak`
```text
"%LocalAppData%\Local\Google\Chrome\User Data\Default"
```
!!! Warning "Non-Guest/Signed-In Browsers
    If the user has signed into a profile, find the corresponding profile's folder in `User Data` instead of `default`

---

### Edge Bookmarks
Edge bookmarks are located in the below folder and are named `bookmarks` & `bookmarks.bak`
```text
"%LocalAppData%\Local\Microsoft\Edge\User Data\Default"
```
!!! Warning "Non-Guest/Signed-In Browsers
    If the user has signed into a profile, find the corresponding profile's folder in `User Data` instead of `default`

---

## Applications

### Manage Engine

#### Bin - Manage Engine

```text
"C:\Program Files (x86)\ManageEngine\UEMS_Agent\bin"
```

!!! info "Binary Tools"
    Tools such as `Agent_Troubleshooting_Tool.exe` can be found here (useful for troubleshooting DS issues), as well as other executables ME uses.

---

#### Software Repo - Manage Engine

```text
"C:\Program Files (x86)\ManageEngine\UEMS_Agent\swrepository\swuploads"
```

!!! info "swrepository info"
    Executables and installers from pushed configuration packages will be stored here. Can be useful when needing to manually install a stuck SW push.

---