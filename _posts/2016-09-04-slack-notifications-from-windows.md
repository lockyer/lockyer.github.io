---
layout: post
title: Slack Notifications
published: true
---

I often execute long running scripts on my development machine and want to get notified upon completion. I'd prefer to do this without installing anything extra, because [nobody hates software more than software developers](https://blog.codinghorror.com/nobody-hates-software-more-than-software-developers/). I'm already running slack, so one practical way to do this is by exploiting [Slack Webhooks](https://testteamchatchannel.slack.com/apps/A0F7XDUAZ-incoming-webhooks). Unfortunately, all [Slack's examples for web hooks](https://api.slack.com/incoming-webhooks)  use [cURL](https://en.wikipedia.org/wiki/CURL) and I'm developing on Windows 10. Certianly, there are solutions for getting cURL on windows, but I'd prefer something that works on a fresh install. Surely there's a native solution.

This is where Powershell's `Invoke-RestMethod` comes in.

First lets set up the Incoming Webhooks integration in slack. Log into the web app, and click on your team's name in the top left corner. Select **Team settings** from the dropdown.
![TeamSettings.png]({{site.baseurl}}/assets/SlackNotifications/TeamSettings.png)
In the menu on the left select **Configure Apps**. In the **Apps Directory** search bar at the top search for **Incoming Webhooks**. 
![Search.png]({{site.baseurl}}/assets/SlackNotifications/Search.png)
Then hit **Add Configuration**.
![AddConfiguration.png]({{site.baseurl}}/assets/SlackNotifications/AddConfiguration.png)
In the dropdown select a default channel you'd like your automated posts to go to, don't worry, you can change this programmatically in the future. Next hit the big green button: **Add Incoming Webhooks integration**
![PostToChannel.png]({{site.baseurl}}/assets/SlackNotifications/PostToChannel.png)
The next page you get has a bunch of documentation and some settings you can adjust. Most of the settings here are just defaults which can be overridden by your scripts. The only thing we're interested in here at the moment is the **Webhook URL**. Copy it to your clipboard.
![URL.png]({{site.baseurl}}/assets/SlackNotifications/URL.png)
Go to the search bar in Windows 10 by hitting the Windows key and typing **Powershell ISE** to open the integrated powershell editor. Hit **File > New** to create your new script. Fill it with the following, but make sure you relplace the **-Uri** argument with your own **Webhook URL**

```powershell
$payload = @{
	text = 'Hello World'
}
$json = $payload | ConvertTo-Json
Invoke-RestMethod -Uri https://hooks.slack.com/services/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 -Method POST -Body $json
```

In **PowerShell ISE** hit the green **Run Script** button to test your script out, you should get **ok** as a return value, and you should see your message in **Slack**. Make sure you check the right channel!
![HelloWorld.png]({{site.baseurl}}/assets/SlackNotifications/HelloWorld.png)
You can do a lot more interesting stuff with this once you get the basics working, such as sending different notifications when builds fail or succeed. This script can be called on the command line with your **Webhook URL** and a `bool` to indicate success or failure:

```powershell
param (
	[Parameter(Mandatory=$true)][string]$uri,
	[Parameter(Mandatory=$true)][bool]$success
)

$message = 'Build Failed'
$emoji = ':rage:'

if($success)
{
	$message = 'Build Succeeded'
	$emoji = ':nerd_face:'
}

$payload = @{
	username='Compiler'
	text=$message
	icon_emoji=$emoji
}

$json = $payload | ConvertTo-Json
$response = Invoke-RestMethod -Uri $uri -Method POST -Body $json
```

I'd love to hear about the awesome things you automate after trying this out!
