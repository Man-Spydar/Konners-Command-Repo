# Registry Edits and Fixes

### Dark Mode Fix
When switching to dark mode and certain UI menus are still lightmode, the following commands can be used to fully transition to dark mode.

!!! note
    A full sign-out is require for changes to take effect

~~~powershell
Set-ItemProperty HKCU:\Software\Microsoft\Windows\CurrentVersion\ThemeManager DllName "%SystemRoot%\Resources\Themes\Aero\Aero.msstyles" -Type ExpandString
Set-ItemProperty HKCU:\Software\Microsoft\Windows\CurrentVersion\ThemeManager "PrePolicy-DllName" "C:\Windows\Resources\Themes\Aero\Aero.msstyles"
~~~

!!! warning
    This must be ran from the users profile, otherwise you can navigate to their hive (if loaded) and supply the same values.

---
