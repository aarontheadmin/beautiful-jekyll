---
layout: post
title: Netstat as PowerShell Objects to Identify Port Usage
tags: [Windows PowerShell, netstat, Windows]
---

This post covers how to dump netstat output into Windows PowerShell objects for easier management and investigation purposes. This can be helpful if you're trying to find out what port is being used with a given IP address on current TCP connections.

You can use netstat to report TCP connections including the ports and PIDs but not the process name associated with the PID. And, the output is ugly and doesn't offer much flexibility.

A solution is to wrap netstat in a PowerShell command so that it can also report the process name bound to a given port:

```powershell
Function Get-TCPPortConnection {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [uint16]
        $Port = 80
    )

    $NetStatCmd = netstat -ano | findstr :$Port

    $NetStatCmd |
        ConvertFrom-String -PropertyName Null, Protocol, LocalAddress, ForeignAddress, State, PID |
        Select-Object -Property Protocol, LocalAddress, ForeignAddress, State, PID,
        @{
            Name = 'ProcessName';
            Expression = {
                Get-Process -PID $_.PID |
                    Select-Object -ExpandProperty ProcessName
            }
        }
}
```

This code snippet will take the output of netstat and turn it into PowerShell objects. It includes the actual process name so you don't have to manually cross-reference the PID. This makes it much easier to find out what port is being used and where.
