---
layout: post
author: Shane Skiles
tags: [tiktok, video, download]
---

TikTok went offline in the US in January 2025. It was going to go offline again 
in April 2025, but it was given another chance again. For those of you who are 
content creators, and would like to download your videos, here is a PowerShell 
script to automate the process once you have requested your activity in json format.

```powershell
$data = ".\user_data_tiktok.json"
$outputdirectory = ".\TikTokVideos"

$parameters = Get-Content -Raw $data | ConvertFrom-Json

$parameters.Video.Videos.VideoList |
    ForEach{
        $sound = $_.Sound -split " - "
        $date = $_.Date
        $formatteddate = Get-Date $date -Format "yyyyMMdd"
        $likes = $_.Likes
        $filename = $sound[1] + "." + $sound[0] + ".$formatteddate.$likes.mov"
        
        $filename = $filename.Replace("/", "-").
            Replace("?", "").
            Replace("\", "").
            Replace(":", "").
            Replace("'", "").
            Replace("\", "-").
            Replace('"', "")

        $fullfilename = "$outputdirectory\$filename"

        Write-Output ("Downloading...$fullfilename")
        $exists = Test-Path -Path $fullfilename
        if ( !$exists ) 
        {
            try
            {
                $tmp = "$outputdirectory\tmp.mov"
                Invoke-WebRequest -Uri $_.Link -OutFile $tmp
                Rename-Item -Path $tmp -NewName $fullfilename
            }
            catch
            { 
                $fullfilename | Out-File -FilePath ("$outputdirectory\$formatteddate.$likes.txt")
            }
        }
    }
```