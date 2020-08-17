---
layout: single
title: "Update PowerShell Core and .NET Core Packages Using Homebrew"
author_profile: true
date: 2018-03-18
comments: true
tags:
  - "PowerShell Core"
  - dotnetcore
  - Homebrew
  - upgrade
  - macOS
category:
  - PowerShell
---

With the recent CVE bulletin, Microsoft Security Advisory CVE-2018-0875: Hash Collision can cause Denial of Service, it warrants us to update PowerShell Core to 6.0.2 and .NET Core to 2.0.6.

This post covers updating PowerShell Core and .NET Core packages using Homebrew, the Microsoft-recommend method for installing/managing these packages. This method can be used any time you want to update either package:

1. Open **Terminal**.
2. Run ```brew update``` (It will appear nothing is happening but watch the window title bar)
3. Run ```brew cask upgrade powershell```
4. Run ```brew cask upgrade dotnet```
5. Once completed, run ```pwsh``` to load PowerShell. You should see 6.0.2 (or the newest version) at the top:

```powershell
PowerShell v6.0.2
Copyright (c) Microsoft Corporation. All rights reserved.

https://aka.ms/pscore6-docs
Type 'help' to get help.

PS />
```

6. You can also confirm by using **$PSVersionTable**:

```powershell
PS /> $PSVersionTable                                               

Name                           Value                                           
----                           -----                                           
PSVersion                      6.0.2                                           
PSEdition                      Core                                            
GitCommitId                    v6.0.2                                          
OS                             Darwin 17.4.0 Darwin Kernel Version 17.4.0: S...
Platform                       Unix                                            
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}                         
PSRemotingProtocolVersion      2.3                                             
SerializationVersion           1.1.0.1                                         
WSManStackVersion              3.0
```

Now, run **dotnet --info**  (two dashes) and you should see Version 2.0.6:

```powershell
PS /Users/aarontheadmin> dotnet --info                                                 

Microsoft .NET Core Shared Framework Host

  Version  : 2.0.6
  Build    : 74b1c703813c8910df5b96f304b0f2b78cdf194dYour packages are officially updated.
```
