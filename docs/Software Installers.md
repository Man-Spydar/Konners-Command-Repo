# Pre-Made Software Install Commands (PS-EXEC)

### CMAKE
??? note "PS-EXEC Installer"
    ~~~powershell
    $ComputerName = "COMP_01"
    $RemoteMsi = "LOCATION_OF_INSTALLER"
    psexec "\\$ComputerName" -s -h cmd /c "msiexec /i $RemoteMsi INSTALL_ROOT=C:\Apps\CMAKE ALLUSERS=1 ADD_CMAKE_TO_PATH=System /qn /norestart /L*v $LogFile"
    ~~~
