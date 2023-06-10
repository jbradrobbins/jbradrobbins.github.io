---
layout: post
title: Accessing the apivoid screenshot API with PowerShell
subtitle: Capture images of those malicious websites with no risk
#gh-repo: daattali/beautiful-jekyll
#gh-badge: [star, fork, follow]
tags: [apivoid,powershell]
comments: true
---

This posts outlines how to utilize the apivoid screenshot api to capture high-quality images of websites.

In the first step we will define the apikey and url variable.
~~~
$apikey = ""

$url = 'https://www.cnet.com'
~~~
After defining the resource that we would like to caputure we can pass the url object to the apivoid endpoint for processing and define the varible for the results.

~~~
$url | ForEach-Object {

  $deviceuri = "https://endpoint.apivoid.com/screenshot/v1/pay-as-you-go/?key=$apikey&url=$url&full_page=true"
    
  $apivoidresults = Invoke-RestMethod -Method Get -uri $deviceuri

}
~~~

~~~
$domain_match = "^(?:https?:\/\/)?(?:[^@\/\n]+@)?(?:www\.)?([^:\/\n]+)"
$file = $url | Select-String -Pattern $domain_match | foreach-object { $_ -replace "https://", "" } | foreach-object { $_ -replace "\.", "-" }
~~~

~~~
$base64 = $apivoidresults.data.base64_file
$filename = 'C:\Users\' + $env:username + '\downloads\' + $file + '.png'
~~~

~~~
$bytes = [Convert]::FromBase64String($base64)
[IO.File]::WriteAllBytes($filename, $bytes)
~~~

~~~
start-process $filename
~~~

