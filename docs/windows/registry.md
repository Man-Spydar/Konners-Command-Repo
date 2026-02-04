# Registry

Registry tweaks for common user-facing issues.

---

## Quick Reference
- [Theme / Appearance](#theme--appearance)
  - [Dark mode fix](#dark-mode-fix)

---

## Theme / Appearance

### Dark Mode Fix
When switching to dark mode and certain UI menus are still light mode, use the following commands to fully transition to dark mode.

!!! note
    A full sign-out is required for changes to take effect.

```powershell
Set-ItemProperty HKCU:\Software\Microsoft\Windows\CurrentVersion\ThemeManager DllName "%SystemRoot%\Resources\Themes\Aero\Aero.msstyles" -Type ExpandString
Set-ItemProperty HKCU:\Software\Microsoft\Windows\CurrentVersion\ThemeManager "PrePolicy-DllName" "C:\Windows\Resources\Themes\Aero\Aero.msstyles"
```

!!! warning
    This must be run from the user's profile. Otherwise, load their hive and supply the same values there.

---
