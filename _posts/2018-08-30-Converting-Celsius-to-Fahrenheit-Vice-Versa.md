---
layout: single
title: "Converting Celsius to Fahrenheit, Vice-Versa"
author_profile: true
date: 2018-08-30
comments: true
tags:
  - PowerShell
  - "temperature conversion"
category:
  - PowerShell
---

There are a few ways to converting Celsius to Fahrenheit, vice-versa, but here is a simple, advanced function that can do it for you.

Simply enter the known degrees in the -Value parameter then specify what you want to convert to:

**-Celsius** or **-Fahrenheit**

```Convert-Temperature -Value 33 -Fahrenheit```

The example above would return 91.4 Fahrenheit based on the -Value 33.

### BEGIN {} Block ###
This function supports pipeline input. Instead of checking if ```-Celsius``` or ```-Farhenheit``` was specified each time an object is processed in the PROCESS{} block, we do only one check in the BEGIN{} block (before any objects are processed in the pipeline).

Based on the switch specified (```-Celsius``` or ```-Fahrenheit```), we assign its corresponding mathematical formula with a placeholder (```tempValue```) to a variable, called ```$formula```. This variable is of type String so that we can do a ```Replace()``` on ```tempValue``` for the actual Value.

### PROCESS {} Block ###
As each Value in the pipeline is processed, the ```tempValue``` placeholder is replaced by the current Value object. This is done using ```Replace()``` as stated earlier.

Finally, Invoke-Expression is used to invoke the formula as a mathematical expression (instead of a string), and the result converted to a double with rounding.

Note: error handling has been left out intentionally:

```powershell
Function Convert-Temperature {

    <#
    .SYNOPSIS
    Converts between Celsius and Fahrenheit.
    
    .DESCRIPTION
    Converts temperatures between Celsius and Fahrenhiet, vice-versa.
    
    .PARAMETER Celsius
    Converts the Value to Celsius.
    
    .PARAMETER Fahrenheit
    Coverts the Value to Fahrenheit.
    
    #>
    [CmdletBinding()]
    param (
        [Parameter(
            Mandatory,
            Position          = 0,
            ValueFromPipeline = $true)]
        [double[]]
        $Value,

        [Parameter(ParameterSetName = 'Celsius')]
        [switch]
        $Celsius,

        [Parameter(ParameterSetName = 'Fahrenheit')]
        [switch]
        $Fahrenheit
    )

    BEGIN {
        # Store the formula in memory before any objects are processed
        if ($Fahrenheit.IsPresent) {
            [string]$formula = "(( 9 * tempValue) / 5) + 32"
        }
        else {
            [string]$formula = "(5 * (tempValue - 32)) / 9"
        }
    }

    PROCESS {

        foreach ($temperature in $Value) {

            [double]$result        = Invoke-Expression (($formula).Replace('tempValue', $temperature))
            [double]$roundedResult = [System.Math]::Round($result, 2)

            Write-Output $roundedResult
        }
    }
}
```
