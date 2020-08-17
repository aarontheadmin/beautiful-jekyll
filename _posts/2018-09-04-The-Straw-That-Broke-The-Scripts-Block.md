---
layout: post
title: The Straw That Broke The Script's Block
tags: [PowerShell, InjectionHunter, macOS, Windows, Visual Studio Code, characterset]
---

This is a reminder to be mindful when copying and pasting code from an external source. I've been putting InjectionHunter to use and encountered "the straw that broke the ~~camel's back~~ script's block." It shows how well designed this module is (thanks [@Lee_Holmes!](https://twitter.com/Lee_Holmes))


| Straw | Script block |
--- | ---
| part of the decrement operator ```- -``` | a ```for()``` loop |


I decided to give InjectionHunter a whirl against a script module I wrote for Wirecast on macOS, but my playground for this was my Windows laptop with a current version of the module. Out of many, many script files, three items came up with the Severity of "Warning" and it ended with a ParseException. Really? InjectionHunter is bugged! Nope.

The ParseException was thrown once InjectionHunter hit a script file, Start-Countdown.ps1 (it counts backwards based on the time you specify):

![ParseException caught by InjectionHunter caused by incorrect decrement character in for loop](https://aarontheadmin.github.io/images/parseexception.png "ParseException Caught by InjectionHunter")

There were a few errors after this but for brevity sake, only the first one is shown here.

This ParseError was obviously pointing to one or more conflicting characters but the strange thing was that this script has been used in production for quite a while and has never thrown exceptions like this; it just worked as expected.

Opening the script file in Visual Studio Code (in Windows), I found the straw. Glancing at the for() loop on line 28 (as per the ParseException) didn't offer an obvious clue because everything appeared correct. But honing in on the decrement operator, the left "-" is slightly longer in size than the right "-":

![Visual Studio Code: For loop containing incorrect decrement character](https://aarontheadmin.github.io/images/vscode-bad-char.png "Visual Studio Code: For loop containing incorrect decrement character")

Copying and pasting into Notepad reflects the same thing:

![Notepad - For loop containing incorrect decrement character](https://aarontheadmin.github.io/images/notepad.png "Notepad - For loop containing incorrect decrement character")

It reminds me of the auto-formatting of hyphens in Microsoft Word when hitting a space afterwards. Clearly, there is no space added here.

By replacing this conflicting character with the correct one (manual typing), InjectionHunter completes without throwing the ParseException:

![Visual Studio Code: For loop containing fixed decrement character](https://aarontheadmin.github.io/images/fixed.png "Visual Studio Code: For loop containing fixed decrement character")

![InjectionHunter - No ParseException](https://aarontheadmin.github.io/images/injectionhunter-no-parseexception.png "InjectionHunter - No ParseException")

No more ParseException! With the correct-size straw, this <del>camel</del> script block can now function without a grunt.

So, whether you're following someone else's work or you copy/paste from an external source, always make sure the correct character set is used, and if you use InjectionHunter, maybe it will find anomalies like it did here.
