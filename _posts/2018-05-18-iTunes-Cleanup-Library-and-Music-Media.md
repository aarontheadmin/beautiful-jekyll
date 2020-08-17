---
layout: post
title: iTunes Cleanup (Library and Music Media)
tags: [PowerShell Core, iTunes, macOS]
---

I've been writing a PowerShell module that handles the automation of live streaming on an iMac running macOS 10.13 using Wirecast, iTunes and some other tools.

One of requirements is that the iTunes music library and any associated music media files are automatically deleted once iTunes has done its part. The use of iTunes for just anything is restricted in this environment, so deleting everything in it is safe.

**IMPORTANT:** This script completely removes the **iTunes Library.itl** file. All data in your iTunes Library will be deleted. 

Here is the script that takes care of everything:

```powershell
Function Invoke-iTunesCleanup {
    [CmdletBinding(
        SupportsShouldProcess = $true,
        ConfirmImpact = 'High'
    )]
    param ()

    $iTunesPath = "/Users/$env:USER/Music/iTunes"
    $ilt = 'iTunes Library.itl'
    $iTunesLibrary = Join-Path -Path $iTunesPath -ChildPath $ilt
    $MusicPath = 'iTunes Media/Music'
    $iTunesMusic = Join-Path -Path $iTunesPath -ChildPath $MusicPath

    if ($PSCmdlet.ShouldProcess("iTunes")) {
        # Delete the entire Music folder
        try {
            Remove-Item -Path $iTunesMusic -Recurse -Force -ErrorAction Stop
            Write-Host "Deleted $iTunesMusic" -ForegroundColor Green
            $completed = $true
        }
        catch {
            Write-Host "Could not delete $iTunesMusic" -ForegroundColor Red
        }


        # Delete the iTunes .itl file that contains library info
        try {
            Remove-Item -Path $iTunesLibrary -Force -ErrorAction Stop
            Write-Host "Deleted $iTunesLibrary" -ForegroundColor Green
            $completed = $true
        }
        catch {
            Write-Host "Could not delete $iTunesLibrary" -ForegroundColor Red
        }

        if ($completed) {
            Write-Host "iTunes cleanup complete." -ForegroundColor Green
        }
    }
}
```

You may need to close and re-open iTunes to see the music library empty. Also, confirm that **/Users/username/Music/iTunes/iTunes Media/Music** no longer exists (this is where the music media files were stored).

You can enhance this script by parameterizing the paths but being targeted for iTunes, the paths shouldn't really change.
