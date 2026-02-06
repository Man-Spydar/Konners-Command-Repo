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

## Adobe Acro

??? note "Silent Install Flags"
    Run this from the directory where the EXE lives:
    ```powershell
    Start-Process ".\*ACROBATINSTALLER*.exe" -ArgumentList "/sAll /rs /rps /msi EULA_ACCEPT=YES" -Wait
    ```