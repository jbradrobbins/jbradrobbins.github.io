---
layout: post
title: Accessing the apivoid screenshot API with PowerShell
subtitle: Automating Website Screenshots and Availability Checks with PowerShell and APIVoid
#gh-repo: daattali/beautiful-jekyll
#gh-badge: [star, fork, follow]
tags: [apivoid,powershell]
comments: true
---

PowerShell, with its ability to integrate various services and perform automation tasks, offers an efficient way to monitor websites and capture screenshots. In this blog post, we'll walk through a PowerShell script that leverages the APIVoid API to capture screenshots of a website and also check whether the website is active. This script can be particularly useful for security professionals, developers, and system administrators who need to monitor the status of websites regularly.

#### Prerequisites
Before running the script, ensure you have the following:

- APIVoid API Key: Sign up at APIVoid to obtain your API key.
- PowerShell: Ensure you are using PowerShell version 5.0 or later.

#### Here’s the PowerShell script:
~~~
$apikey =
$url = 'https://www.apivoid.com/'

$url | ForEach-Object {

  $deviceuri = "https://endpoint.apivoid.com/screenshot/v1/pay-as-you-go/?key=$apikey&url=$_&full_page=true"

  $apivoidresults = Invoke-RestMethod -Method Get -uri $deviceuri

  $domain = [uri]$url
  $file = $domain.Authority | foreach-object { $_ -replace "\.", "-" }
  $base64 = $apivoidresults.data.base64_file
  $filename = 'C:\Users\' + $env:username + '\downloads\' + $file + '.png'

  $bytes = [Convert]::FromBase64String($base64)
  [IO.File]::WriteAllBytes($filename, $bytes)

  If ($apivoidresults.error -eq "Url has no IP configured"){
    Write-Host -ForegroundColor Green "Url has no IP configured. Website is not active"
  }
  else{start-process $filename}

}
~~~

##### Script Breakdown
1. Setting the API Key and URL

- At the beginning of the script, you need to set your APIVoid API key and the URL of the website you want to capture.
~~~
$apikey =
$url = 'https://www.apivoid.com/'
~~~
- Replace the placeholder with your actual APIVoid API key.

2. Building the API Request

The script constructs the API request URL using the provided URL and API key. The full_page=true parameter ensures that a screenshot of the entire webpage is captured. 
~~~
$deviceuri = "https://endpoint.apivoid.com/screenshot/v1/pay-as-you-go/?key=$apikey&url=$_&full_page=true"

~~~

3. Making the API Call

The Invoke-RestMethod cmdlet is used to send the API request and store the response in the $apivoidresults variable.
~~~
$apivoidresults = Invoke-RestMethod -Method Get -uri $deviceuri

~~~
4. Extracting and Formatting the Domain Name

The script extracts the domain name from the URL and formats it by replacing dots (.) with hyphens (-). This formatted domain name is used to create a unique filename for the screenshot.
~~~
$domain = [uri]$url
$file = $domain.Authority | foreach-object { $_ -replace "\.", "-" }

~~~
5. Saving the Screenshot

The API response includes the screenshot as a Base64-encoded string. The script converts this string into bytes and saves it as a PNG file in the Downloads folder of the current user.
~~~
$base64 = $apivoidresults.data.base64_file
$filename = 'C:\Users\' + $env:username + '\downloads\' + $file + '.png'

$bytes = [Convert]::FromBase64String($base64)
[IO.File]::WriteAllBytes($filename, $bytes)

~~~
6. Checking Website Availability

Before opening the screenshot, the script checks if the API returned an error indicating that the website has no IP configured. This would mean that the website is not active.
~~~
If ($apivoidresults.error -eq "Url has no IP configured"){
  Write-Host -ForegroundColor Green "Url has no IP configured. Website is not active"
}
else{start-process $filename}

~~~

#### Running the Script
To run the script, follow these steps:

-Save the script to a .ps1 file.
-Open PowerShell as an administrator.
-Execute the script by running .\yourscript.ps1.

#### Use Cases
This script can be a powerful tool for:

-Monitoring Website Uptime: Regularly check the availability of important websites.
-Capturing Website States: Automate the process of taking screenshots for documentation or security reviews.
-Reporting: Easily include website screenshots in reports.

#### Conclusion
By combining the capabilities of PowerShell with the APIVoid service, you can automate the process of monitoring websites and capturing screenshots. This script can be customized further to suit your needs, such as scheduling it to run at regular intervals or extending it to work with multiple URLs. Start leveraging this script today to streamline your website monitoring tasks!

![Screenshot](/assets/img/apivoid-com.png){: .mx-auto.d-block :}
