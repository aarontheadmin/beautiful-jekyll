---
layout: single
title: "PowerShell Core: Updating PSModulePath from Windows PowerShell PSModulePath"
author_profile: true
date: 2017-11-11
comments: true
tags:
  - "powershell core"
  - psmodulepath
category:
  - PowerShell
---

The PowerShell gallery has a <a href="https://www.powershellgallery.com/packages/WindowsPSModulePath/1.0.0" target="_blank">script module</a> (credit: Steve Lee [MSFT]) you can install that appends your Windows PSModulePath contents to the PSModulePath in PowerShell Core. This lets you access modules in any of those Windows PSModulePaths within PowerShell Core, including your custom modules.

(Note: I've already commented on the gallery of what this post will cover. I've added it here in case one does not come across the PS Gallery.)

This script works but it adds all paths existing in Windows. This causes duplicate paths to exist in PSModulePath in PowerShell Core as some paths already exist in it as well.

Here is that script revised so that it adds only paths that don't exist in PowerShell Core $env:PSModulePath:

```powershell
Function Add-WindowsPSModulePath {

    if (! $IsWindows) {
        throw "This cmdlet is only supported on Windows"
    }

    $WindowsPSModulePath = [System.Environment]::GetEnvironmentVariable("psmodulepath",
        [System.EnvironmentVariableTarget]::Machine)

    $WinPaths = ($WindowsPSModulePaths -split ';')

    foreach ($path in $WinPaths) {

        if (-not ($env:PSModulePath).Contains($path)) {

            $env:PSModulePath += ";$path}"
        }
    }
}
```

You can copy this code into your profile.ps1 file so it will execute each time you open PowerShell Core:

```powershell
Function Add-WindowsPSModulePath {

    if (! $IsWindows) {
        throw "This cmdlet is only supported on Windows"
    }

    $WindowsPSModulePath = [System.Environment]::GetEnvironmentVariable("psmodulepath",
        [System.EnvironmentVariableTarget]::Machine)

    $WinPaths = ($WindowsPSModulePaths -split ';')

    foreach ($path in $WinPaths) {

        if (-not ($env:PSModulePath).Contains($path)) {

            $env:PSModulePath += ";$path}"
        }
    }
}

Add-WindowsPSModulePath
```
