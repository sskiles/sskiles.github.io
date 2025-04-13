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
$data&nbsp;=&nbsp;".\user_data_tiktok.json"
$outputdirectory&nbsp;=&nbsp;".\TikTokVideos"

$parameters&nbsp;=&nbsp;Get-Content&nbsp;-Raw&nbsp;$data&nbsp;|&nbsp;ConvertFrom-Json

$parameters.Video.Videos.VideoList&nbsp;|
&nbsp;&nbsp;&nbsp;&nbsp;ForEach{
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$sound&nbsp;=&nbsp;$_.Sound&nbsp;-split&nbsp;"&nbsp;-&nbsp;"
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$date&nbsp;=&nbsp;$_.Date
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$formatteddate&nbsp;=&nbsp;Get-Date&nbsp;$date&nbsp;-Format&nbsp;"yyyyMMdd"
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$likes&nbsp;=&nbsp;$_.Likes
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$filename&nbsp;=&nbsp;$sound[1]&nbsp;+&nbsp;"."&nbsp;+&nbsp;$sound[0]&nbsp;+&nbsp;".$formatteddate.$likes.mov"
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$filename&nbsp;=&nbsp;$filename.Replace("/",&nbsp;"-").
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Replace("?",&nbsp;"").
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Replace("\",&nbsp;"").
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Replace(":",&nbsp;"").
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Replace("'",&nbsp;"").
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Replace("\",&nbsp;"-").
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Replace('"',&nbsp;"")

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$fullfilename&nbsp;=&nbsp;"$outputdirectory\$filename"

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Write-Output&nbsp;("Downloading...$fullfilename")
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$exists&nbsp;=&nbsp;Test-Path&nbsp;-Path&nbsp;$fullfilename
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if&nbsp;(&nbsp;!$exists&nbsp;)&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;try
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$tmp&nbsp;=&nbsp;"$outputdirectory\tmp.mov"
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Invoke-WebRequest&nbsp;-Uri&nbsp;$_.Link&nbsp;-OutFile&nbsp;$tmp
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Rename-Item&nbsp;-Path&nbsp;$tmp&nbsp;-NewName&nbsp;$fullfilename
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;catch
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$fullfilename&nbsp;|&nbsp;Out-File&nbsp;-FilePath&nbsp;("$outputdirectory\$formatteddate.$likes.txt")
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}
&nbsp;&nbsp;&nbsp;&nbsp;}
```