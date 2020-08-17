---
layout: single
title: "Double-Click .command File to Execute PowerShell Core Script"
author_profile: true
date: 2018-03-09
comments: true
tags:
  - "PowerShell Core"
  - .command
  - bash
  - macOS
category:
  - PowerShell
---

Sometimes, double-clicking a file on the desktop to execute a script makes it much more convenient to accomplish something, even though it has its security implications. This post covers how to do that with Powershell Core and .commands files in macOS.

Note: To accomplish this in PowerShell Core 6.0.1+ is different that PowerShell Core 6.0.0. We'll go with PowerShell Core 6.0.1.

Simply create a new file ending in .command. This could be placed on the desktop.

At the beginning of the file, you need to start with the ```command``` keyword. This will be followed by the commands you want to execute, and any arguments. It's helpful to clear any output on the console, so we'll throw in the clear command:

```
command clear
```

Next, add **&&** which is used to execute the next operation if the previous one completed successfully:

```
command clear &&
```

Now, specify the PowerShell Core command, pwsh, so that it executes next:

```
command clear && pwsh
```

Now the fun part, appending the actual PowerShell code. Use the ```-Command``` parameter to tell PowerShell, "I have a command you need to execute," rather than a file (-File).

The entire command is enclosed in double-quotes. Inside the double-quotes, you use the invoke operator ( & ), followed by a set of curly braces ( { } ) wherein you place your actual PowerShell code:

```
command clear && pwsh -command "&{ Get-Process }"
```

Save your file and double-click it!

You can also try some other cmdlets or functions of your own inside the curly braces:

```
command clear && pwsh -Command "& {
    Get-Random
    Get-Process
}"
```

Executing this (truncated) code returns:

```
1969584494

Verb        AliasPrefix Group          Description                             
----        ----------- -----          -----------                             
Add         a           Common         Adds a resource to a container, or at...
Clear       cl          Common         Removes all the resources from a cont...
Close       cs          Common         Changes the state of a resource to ma...
Copy        cp          Common         Copies a resource to another name or ...
Enter       et          Common         Specifies an action that allows the u...
Exit        ex          Common         Sets the current environment or conte...
Find        fd          Common         Looks for an object in a container th...
Format      f           Common         Arranges objects in a specified form ...
```

Now you can double-click a .command file to execute a PowerShell script!
