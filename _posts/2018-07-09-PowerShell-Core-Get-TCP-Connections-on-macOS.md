---
layout: post
title: "PowerShell Core: Get TCP Connections on macOS"
tags: [PowerShell Core, tcp, macOS, lsof]
---

Getting active TCP connections can tell about what might be going on between applications and systems, and even help with network troubleshooting. As of PowerShell Core 6.0.2, there is no built-in cmdlet for getting TCP connections (unlike in Windows PowerShell - Get-NetTCPConnection) but with a little work, we can author our own function to get similar information on macOS.

Scroll down to see the entire script, or continue reading to understand each component.

# Step 1 - Use bash to get TCP connections #
The first step is to use a bash command that will get TCP connections and then assign it to a variable, $connCommand. This is accomplished by using ```lsof```  and ```grep```:

```powershell
$connCommand = 'lsof -i -P | grep -i "ESTABLISHED"'
```

## Command explained: ##
```lsof```  is a Unix command that lists all open files and the processes that opened them  
```-i```  lists IP sockets  
```-P```  indicates that port numbers should be used and not the port names  
```|```  pipes lsof output to the grep command  

```grep```  is a command for Unix-like OSes that searches plain text datasets that match a regular expression  
```-i```  indicates to ignore case  
```"Established"```  is the regular expression we're looking to match  

# Step 2 - Use Invoke-Expression #
We can use ```Invoke-Expression``` to execute the code in $connCommand which will return a collection of objects; let's use a variable for that collection, ```$tcpConnection```:

```powershell
$tcpConnection = Invoke-Expression -Command $connCommand
```

# Step 3 - Use foreach to Build Objects #
Next, iterate through the $tcpConnection  collection and use the -split operator to split at any one (or more) whitespace characters into separate objects. For this function, we want the 9 properties that the bash command returns, so the output needs 9 "splits":

```powershell
foreach ($conn in $tcpConnection) {
    $data = $conn -split 's+', 9
    ... # more code to come
}
```

Still inside the foreach loop, we use a $props variable to reference a hashtable consisting of the 9 properties and their values and then create a new PSObject using $props:

```powershell
$props = @{
    Command = $data[0]
    PID = $data[1]
    User = $data[2]
    FD = $data[3]
    Type = $data[4]
    Device = $data[5]
    SizeOffset = $data[6]
    Node = $data[7]
    Name = $data[8]
}
    
New-Object -TypeName psobject -Property $props }
```

If you were to run the bash command ```lsof -i -P```  by itself without piping to ```grep``` , it would output a table with headers in this order: Command, PID, User, FD, Type, Device, SizeOffset, Node, and Name; hence, the order of properties in the $props hashtable.

That's it for the "piece by piece" approach. Now, for the entire thing in one function:

```powershell
Function Get-TCPConnection {
    <#
    .SYNOPSIS
    Gets established TCP network connections.
    
    .DESCRIPTION
    Gets established TCP network connections.
    
    .EXAMPLE
    PS /> Get-TCPConnection
    
    SizeOffset : 0t0
    Device     : 0x95a801d5eeaa641
    PID        : 715
    FD         : 72u
    Type       : IPv4
    Node       : TCP
    Command    : firefox
    User       : MacUser
    Name       : 10.0.3.30:65323->23.100.72.34:443 (ESTABLISHED)
    
    The example above lists one of many (truncated output) established TCP connections.
    
    .INPUTS
    None
    
    .OUTPUTS
    System.Management.Automation.PSCustomObject
    #>
    
    [CmdletBinding()]
    param ()
    
    $connCommand = 'lsof -i -P | grep -i "ESTABLISHED"'
    $tcpConnection = Invoke-Expression -Command $connCommand
    
    foreach ($conn in $tcpConnection) {
    
        $data = $conn -split 's+', 9
        $props = @{
            Command    = $data[0]
            PID        = $data[1]
            User       = $data[2]
            FD         = $data[3]
            Type       = $data[4]
            Device     = $data[5]
            SizeOffset = $data[6]
            Node       = $data[7]
            Name       = $data[8]
        }
        
        New-Object -TypeName psobject -Property $props
    }
}
```

Copy the entire function into the PowerShell console and run it:
```powershell
PS /> Get-TCPConnection

SizeOffset : 0t0
FD         : 3u
Command    : Desktop
User       : ahardy
Device     : 0x95a801d5eea9381
Type       : IPv4
PID        : 386
Name       : localhost:49183->localhost:13823 (ESTABLISHED)
Node       : TCP

SizeOffset : 0t0
FD         : 57u
Command    : firefox
User       : ahardy
Device     : 0x95a801d5c388901
Type       : IPv4
PID        : 31155
Name       : macbookpro:56558->ip-198-71-233-204.ip.secureserver.net:80 (ESTABLISHED)
Node       : TCP
```

And that's all there is to it.

Hopefully, you will find this simple function useful to get active TCP connections on your computer. This is an interim solution until an official cmdlet is added to PowerShell Core.
