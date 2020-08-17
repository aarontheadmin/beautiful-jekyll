---
layout: single
title: "Get-Random with Excluded Numbers"
author_profile: true
date: 2017-11-13
comments: true
tags:
  - Get-Random
  - "number exclusions"
  - ArrayList
  - IndexOf()
category:
  - PowerShell
---

An interesting question came up about using the ```Get-Random``` cmdlet to return a random number but with a predefined list of numbers to exclude.

Even though ```Get-Random``` will return a random number, you can also specify a list or range of numbers for it to run against.

```powershell
PS C:\> Get-Random 1,2,3,4,5
```
...will return a random number between 1 and 5.

What if you want to exclude one or more specific numbers? ```Get-Random``` doesn't have this capability built in but we can still accomplish this with a little extra work.

Let's say I want to exclude numbers 1 and 3.

Here's how we'll do it:
1. Assign the entire range (1 to 5) to an ``ArrayList``
2. Assign the numbers we want to exclude (1 and 3) to another ```ArrayList```
3. Loop through the (second) ```ArrayList``` that contains the excluded numbers, and any that match what's in the first ```ArrayList```, remove it from that ```ArrayList```.  
This is accomplished by passing the excluded numbers to the ```IndexOf()``` method of the first ```ArrayList``` object, and invoking it within its ```RemoveAt()``` method. The example will shed more light.
4. With the first ```ArrayList``` now containing all numbers except for the exclusions, use the result with ```Get-Random```.

Here's the code (see below for explanation):

```powershell
# Create ArrayList for entire range
[System.Collections.ArrayList]$NumRange = @(1..5)

# Create ArrayList for excluded numbers
[System.Collections.ArrayList]$Exclusions = @(1,3)

# Loop through excluded numbers
foreach ($num in $Exclusions) {

    # If excluded number exists in entire range,
    if ($NumRange.Contains($num)) {
        # remove it from entire range
        $NumRange.RemoveAt($NumRange.IndexOf($num))
    }
}
```
Here's the output:

```powershell
PS C:\> $NumRange
2
4
5
```
Now, pipe the results to Get-Random:
```powershell
PS C:\> $NumRange | Get-Random
4
```

There you have it, using Get-Random and a range of numbers with exclusions.
