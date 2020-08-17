---
layout: post
title: Use PowerShell Core in VS Code
tags: [Visual Studio Code, terminal.integrated.shell.windows, pwsh.exe]
---

The movement to PowerShell Core is gaining popularity, along with the wide-spreading use of Visual Studio Code; especially as Microsoft has confirmed Windows PowerShell will no longer have new releases (just support for the current).Here I will show you how to change the PowerShell executable in VS Code (on Windows), under Workspace Settings.

1. Go to **View**  > **Command Palette...** and select **Workspace Settings** (note: this will override User Settings)
2. In **Search Settings**, type **.exe**
3. Next to **"terminal.integrated.shell.windows":**, click the pencil (in whitespace on left) and select **Replace in Settings**
4. In the right pane, edit the double-quotes to include the path to the PowerShell Core executable (including double backslashes): **"terminal.integrated.shell.windows":"C:\Program Files\PowerShell Core\[version]\pwsh.exe"**
5. Save and reload VS Code
6. Confirm the PowerShell Core executable is now being used in VS Code (once the terminal finishes loading)

That's it. You are now officially using PowerShell Core to execute your code in Visual Studio Code!

**Update**: In step 6, the default path to PowerShell Core is **C:\Program Files\PowerShell\6\pwsh.exe**
