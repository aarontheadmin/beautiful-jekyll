---
layout: single
title: "PowerShell Core: Fixing 'path not found' of Mounted SMB in macOS"
author_profile: true
date: 2018-03-01
comments: true
tags:
  - "PowerShell Core"
  - macos
  - smb
  - "path not found"
  - mount
category:
  - PowerShell
---

Automation on the Mac is great, when it works. And it can, in this case. In this post, we'll cover an issue where scripts resolving **SMB paths fail** with the **'path not found'** error message, yet those same **shares ARE accessible within the GUI**. Confusing and frustrating? This is critical if you're trying to automate data transfers to network locations using SMB.

# The Problem #
I've been working in a Mac environment where an Ant script transfers data to an SMB share on a local Windows server.

Sometimes, the Ant script would fail to transfer the data to the SMB share, and report ***"path not found"***. However, the share was mounted on the desktop, and one could access and modify data without any issues.

Re-running the Ant script could have happened all day, but it would still fail. Remounting the share did not fix it either. Further down will explain why.

As a workaround, I instructed our team to restart into "Safe Boot" to clear the system cache, which worked for a time until the issue resurfaced.

# Explaining the Fix #
There are different ways to deal with SMB shares but I haven't come across a "PowerShell Core" resource for doing it. So, let's dig in.

Essentially, the Ant ***script was hard-coded to look for the actual mount point /Volumes/netShareA to access its child directories, but it couldn't resolve that path***. It appeared that, anywhere in the GUI was able to resolve that path...or so we thought.

What's the difference?

By going to **Terminal** and typing **mount**, all the volumes mounted on the system are displayed.

And, ahah!

netShareA existed as an empty folder, AND a share existed with the name netShareA-1. This means the Ant script was looking at the empty folder, likely orphaned after an SMB reconnect. And no matter how many times we remount the share, the Ant script would never work!

So, instead of the old share being flushed out of /Volumes/ by the OS, it lingered as an empty directory, causing it to be named netShareA-1 upon re-connection. Restarting in Safe Boot flushed the empty folder out, allowing the native share name to be used, and hence, the Ant script would work.

# The Solution: 2 PowerShell Scripts #
There are a couple we can work around this:
1. **Build two scripts**; one for getting mounted volumes and one for getting the SMB shares based on what is mounted (faster response time);
2. Build one script to get only SMB shares by using ```mount | grep smb```  (slightly slower response time)

***Option 1*** will be used to work around this issue until Apple can fix it (fix targeted for macOS 10.12 but it still exists in High Sierra), or the OS flushes its cache.

## Script 1 - Get-MountedDevice ##
Purpose: ```Get-MountedDeviceGets``` everything that is mounted to the system and returns them as PowerShell objects:

```powershell
Function Get-MountedDevice {
    <#
    .SYNOPSIS
    Gets mounted devices.

    .DESCRIPTION
    Get-MountedDevice gets all mounted devices connected to the system, including network paths.

    .INPUTS
    None. You cannot pipe objects to Get-MountedDevice.

    .OUTPUTS
    System.Management.Automation.PSCustomObject. Get-MountedDevice returns a PSCustomObject 
    containing the Device, MountPoint, and FileSystem.

    .NOTES
    Author: Aaron Hardy
    Name:   Get-MountedDevice
    #>
    [CmdletBinding()]
    param ()

    # Load all mounted volumes into array.
    $MountedDevices = @(mount)

    # Filter used to return only smbfs volumes.
    $rgxAfterDevice = ' on /.*'
    $rgxBeforeMountPoint = '.* on '
    $rgxFileSystem = ' (.*'

    foreach ($mntDevice in $MountedDevices) {
        $Device = $mntDevice -replace $rgxAfterDevice
        $MountPoint = $mntDevice -replace $rgxBeforeMountPoint -replace $rgxFileSystem
        $FileSystem = [regex]::matches($mntDevice, $rgxFileSystem).Value

        $props = @{
            Device     = $Device.Trim()
            MountPoint = $MountPoint.Trim()
            FileSystem = $FileSystem.Trim()
        }

        New-Object -TypeName psobject -Property $props
    }
}
```

## Script 2 - Get-MacOsSmbShare ##
Purpose: ```Get-MacOsSmbShareGets``` the SMB shares with their correct paths and generates them as PowerShell objects:

```powershell
Function Get-MacOsSmbShare {
    <#
    .SYNOPSIS
    Gets SMB shares.

    .DESCRIPTION
    Get-MacOsSmbShare gets SMB shares mounted on the local system.

    .PARAMETER ShareName
    Specifies the share name to get.

    .INPUTS
    None. You cannot pipe objects to Get-MacOsSmbShare.

    .OUTPUTS
    System.Management.Automation.PSCustomObject. Get-MacOsSmbShare returns a PSCustomObject
    containing the Device, MountPoint, and FileSystem.

    .NOTES
    Author: Aaron Hardy
    Name: Get-MountedDevice
    #>
    [CmdletBinding()]
    param (
        [Parameter()]
        [string]$ShareName
    )

    # Filter used to return only smbfs volumes.
    $rgxVolSmbfs = '(smbfs,'

    if ($ShareName) {

        $Share = [regex]::Escape($ShareName)

        # RegEx to include numerically incremented share names
        $rgxShareSuffix = "(-(d{1}|d{2}))?$"

        # Valid path to share in /Volumes
        $SharePath = "/Volumes/$Share$rgxShareSuffix"

        $Mounts = Get-MountedDevice |
            Where-Object -FilterScript {
            ( $_.FileSystem -match $rgxVolSmbfs ) -and
            ( $_.MountPoint -match $SharePath)
        } # Where-Object
    }
    else {
        $Mounts = Get-MountedDevice |
            Where-Object -FilterScript {
            ( $_.FileSystem -match $rgxVolSmbfs )
        }
    } # else

    Write-Output $Mounts |
        Select-Object -Property MountPoint, Device, FileSystem

} # Get-MacOsSmbShare
```

Then to call it in any other script or the pscore command line:

```powershell
PS > (Get-MacOsSmbShare -ShareName netShareA).VolumePath
/Volumes/netShareA-1
```

In the case of this example, this would have returned **/Volumes/netShareA-1**.

That's it!

While there is much more that can be done to enhance this script, it has been a great solution to fixing broken SMB paths while using them for automation on a Mac, with no more transfers failing! I hope you can find it just as useful.
