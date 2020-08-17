---
layout: post
title: Get All Dates In A Given Month
tags: [powershell, datetime, month]
---

This post shows you how to use PowerShell to get actual dates for a given month, not just how many days are in that month.

For instance, using the .NET static method [DateTime]::DaysInMonth() only returns the number of days:

```powershell
PS C:\> [DateTime]::DaysInMonth(2017, 9)
30
```

"Great, I know that September has 30 days but I need all the dates so I can check some data against each date of the month. How do I do that?" Let's take a look.

```powershell
# Declare variables
$year, $month = 2017, 9

# Get total number of days in the specified month
$TotalDays = [DateTime]::DaysInMonth($year, $month)
```
Let's confirm we have the correct amount of days for September:

```powershell
PS C:\> $TotalDays
30
```

Next, we can pipe each number from 1 to 30 (30 in $TotalDays) to ForEach-Object. Each value (1 through 30) will represent the argument for the -Day parameter as each object passes through the loop:

```powershell
PS C:\> 1..$TotalDays | ForEach-Object {
>> Get-Date -Year $year -Month $month -Day $_ }
September 1, 2017 12:00:00 PM
September 2, 2017 12:00:00 PM
September 3, 2017 12:00:00 PM
September 4, 2017 12:00:00 PM
September 5, 2017 12:00:00 PM
September 6, 2017 12:00:00 PM
September 7, 2017 12:00:00 PM
September 8, 2017 12:00:00 PM
September 9, 2017 12:00:00 PM
September 10, 2017 12:00:00 PM
September 11, 2017 12:00:00 PM
September 12, 2017 12:00:00 PM
September 13, 2017 12:00:00 PM
September 14, 2017 12:00:00 PM
September 15, 2017 12:00:00 PM
September 16, 2017 12:00:00 PM
September 17, 2017 12:00:00 PM
September 18, 2017 12:00:00 PM
September 19, 2017 12:00:00 PM
September 20, 2017 12:00:00 PM
September 21, 2017 12:00:00 PM
September 22, 2017 12:00:00 PM
September 23, 2017 12:00:00 PM
September 24, 2017 12:00:00 PM
September 25, 2017 12:00:00 PM
September 26, 2017 12:00:00 PM
September 27, 2017 12:00:00 PM
September 28, 2017 12:00:00 PM
September 29, 2017 12:00:00 PM
September 30, 2017 12:00:00 PM
```

There you have it! Now you have all the dates for the month that can be used to pipe to other cmdlets or advanced functions, or whatever else you need to do.
