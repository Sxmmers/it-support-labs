# Windows Service Investigation

## Objective

Investigate the Windows Print Spooler service using PowerShell and determine its current state, startup configuration, dependencies, service account, and executable path.

## Environment

* Operating System: Windows 11
* Shell: PowerShell
* Service investigated: Print Spooler (`Spooler`)

## Investigation

### 1. Check the service status

```powershell
Get-Service -Name Spooler
```

Result:

```text
Status   Name     DisplayName
------   ----     -----------
Running  Spooler  Print Spooler
```

The service was running at the time of investigation.

### 2. Check status and startup configuration

```powershell
Get-Service -Name Spooler |
    Select-Object Name, Status, StartType
```

Result:

```text
Name     Status   StartType
----     ------   ---------
Spooler  Running  Automatic
```

The Print Spooler is configured to start automatically and was currently running.

### 3. Investigate dependencies

```powershell
Get-Service -Name Spooler |
    Select-Object -ExpandProperty ServicesDependedOn
```

The service reported the following dependencies:

* RPCSS — Remote Procedure Call (RPC)
* HTTP — HTTP Service

No services were returned by `DependentServices`, indicating that no services explicitly depend on the Print Spooler service on this system.

### 4. Inspect the underlying Windows service configuration

```powershell
Get-CimInstance Win32_Service -Filter "Name='Spooler'" |
    Select-Object Name, DisplayName, State, StartMode, StartName, PathName
```

Result:

```text
Name        : Spooler
DisplayName : Print Spooler
State       : Running
StartMode   : Auto
StartName   : LocalSystem
PathName    : C:\WINDOWS\System32\spoolsv.exe
```

## Findings

The Print Spooler was operating normally during the investigation.

The service:

* Was running
* Was configured for automatic startup
* Runs under the `LocalSystem` account
* Uses `C:\WINDOWS\System32\spoolsv.exe`
* Depends on RPCSS and HTTP
* Has no services that explicitly depend on it

## Troubleshooting Application

If a user reported that printing was not working, this investigation would establish whether the Print Spooler itself was a likely cause.

A technician could then continue investigating:

1. Service status
2. Service startup configuration
3. Service dependencies
4. Print queue
5. Printer connectivity
6. Printer drivers
7. Windows event logs
8. Network connectivity, if applicable

The investigation should be performed before making changes to the system.

## PowerShell Concepts Practiced

This lab demonstrated:

* `Get-Service`
* `Select-Object`
* `Format-List`
* `Get-Member`
* `Where-Object`
* `Sort-Object`
* `Export-Csv`
* `Get-CimInstance`
* PowerShell objects and properties
* Service dependencies
* Windows service configuration

## What I Learned

`Get-Service` provides a convenient way to work with Windows services as PowerShell objects. `Get-CimInstance` can provide additional Windows management information, including the service account and executable path.

Using PowerShell objects makes it possible to inspect, filter, sort, select, and export administrative information without manually navigating the Windows graphical interface.
