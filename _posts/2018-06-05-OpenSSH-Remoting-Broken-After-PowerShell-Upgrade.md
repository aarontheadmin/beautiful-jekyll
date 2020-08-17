---
layout: post
title: OpenSSH Remoting Broken After PowerShell Upgrade
tags: [PowerShell Core, remoting, Win32_OpenSSH, macOS, Windows, PSSessionOpenFailed]
---

PowerShell Remoting in PowerShell Core has transformed the way Windows can communicate with other operating systems like Linux and macOS, and accomplish tasks using PowerShell and Bash as though you're physically present on that disparate system. This has been accomplished by combining PowerShell Core and the Win32 port of OpenSSH.

Because following the Microsoft Docs step-by-step does work (in my many experiences), this post will not discuss the setup of Win32_OpenSSH. Rather, it will focus on when upgrading PowerShell Core (on macOS and/or Windows) causes Win32_OpenSSH to break and return errors upon trying to connect to remote Windows computers.

# Scenario #
You just updated PowerShell Core on Windows and/or macOS from 6.0.x to 6.0.2. Perhaps you did this manually or by using a package manager like Chocolatey or Homebrew. You try to connect to remote computers using a PowerShell session; after supplying the prompt with a password, you are greeted with an error message:

```powershell
PS /Users/aarontheadmin> $s = New-PSSession -HostName paw-01 -UserName aarontheadmin -SSHTransport
 aarontheadmin@paw-01's password:
 New-PSSession : [paw-01] The SSH client session has ended with error message: The SSH transport process has abruptly terminated causing this remote session to break.
 At line:1 char:6
 + $s = New-PSSession -HostName paw-01 -UserName aarontheadmin -SSHTransport
 + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 + CategoryInfo : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
 + FullyQualifiedErrorId : PSSessionOpenFailed
 ```
 
Now, let's look into a solution to fix this problem!

# Solution #
What has been the common fix for me? A reinstall? No. Update the sshd_config file and restart the services/daemons.
 
The initial setup of OpenSSH requires a Subsystem entry for PowerShell in the sshd_config file. There are two items that need to be updated:
 
1. Version number (in the path)
2. The path to PowerShell (pwsh)

## In Windows: ##
1. Go to **C:\ProgramData\ssh**
2. Open **sshd_config** (may need to copy to Desktop for editing, then copy back)
3. Near end of document, an entry for ```powershell``` should match as follows:  
   ```Subsystem    powershell    C:/Program Files/PowerShell/6/pwsh.exe -sshs -NoLogo -NoProfile```
4. Make necessary changes and save the file (copy back to C:\ProgramData\ssh if copying from Desktop).
5. In PowerShell, run **Restart-Service sshd, ssh-agent**

## In macOS: ##
1. Open Terminal
2. Using your favorite editor, edit the sshd_config file:  
  **sudo nano /private/etc/ssh/sshd_config**
3. Near end of document, an entry for PowerShell should match as follows:  
 ```Subsystem        powershell        /usr/local/bin/pwsh -sshs -NoLogo -NoProfile```
4. Save changes and exit the editor.
5. Using Terminal, restart the OpenSSH daemon:  
 **sudo launchctl stop com.openssh.sshd**  
 **sudo launchctl start com.openssh.sshd**
 
It can be very inconvenient but sometimes removing all public keys and/or the known_hosts file can dissolve many issues.

## Wrapping Up ##
By following these steps and ensuring the correct information exists in the OpenSSH configuration on Windows and/or macOS, a lot of connectivity issues can be fixed and your PowerShell remoting back in business!
