---
layout: post
title: Accessing the apivoid screenshot API with PowerShell
subtitle: Capture images of those malicious websites with no risk
#gh-repo: daattali/beautiful-jekyll
#gh-badge: [star, fork, follow]
tags: [apivoid,powershell]
comments: true
---

Automating Website Screenshots with PowerShell and APIVoid

In this blog post, we will explore how to automate the process of taking website screenshots using PowerShell and the APIVoid service. This script fetches a screenshot of a specified URL, saves it locally, and opens the image for you to view. Let's break down the code and understand each part of this automation.

Prerequisites
Before running the script, ensure you have the following:

APIVoid API Key: Sign up at APIVoid to get your API key.

PowerShell: Ensure you are running PowerShell version 5.0 or later.

Here's the complete PowerShell script:
~~~
$apikey = ""

$url = 'https://www.apivoid.com'
~~~
After defining the resource that we would like to capture we can pass the url object to the apivoid endpoint for processing and define the varible for the results.

~~~
$url | ForEach-Object {

  $deviceuri = "https://endpoint.apivoid.com/screenshot/v1/pay-as-you-go/?key=$apikey&url=$url&full_page=true"
    
  $apivoidresults = Invoke-RestMethod -Method Get -uri $deviceuri

}
~~~
In the next section, we will create a varible that will hold the file name for website image that is created with the apivoid endpoint.  
~~~
$domain_match = "^(?:https?:\/\/)?(?:[^@\/\n]+@)?(?:www\.)?([^:\/\n]+)"
$file = $url | Select-String -Pattern $domain_match | foreach-object { $_ -replace "https://", "" } | foreach-object { $_ -replace "\.", "-" }
~~~
The next step defines the base64 data that will translated to the image and the destination filename.
~~~
$base64 = $apivoidresults.data.base64_file
$filename = 'C:\Users\' + $env:username + '\downloads\' + $file + '.png'
~~~
Below the magic of .net allows us to convert the base64 stream to the .png image type.
~~~
$bytes = [Convert]::FromBase64String($base64)
[IO.File]::WriteAllBytes($filename, $bytes)
~~~
And last but not least we open the image file for viewing with the start-process commmand.
~~~
start-process $filename
~~~

![Screenshot](/assets/img/apivoid-com.png){: .mx-auto.d-block :}
