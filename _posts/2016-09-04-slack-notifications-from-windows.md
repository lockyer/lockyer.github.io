---
published: false
---
## Slack Notifications from Windows
I often execute long running scripts on my development machine want to get notified upon completion. I'd prefer to do this without installing anything extra, because [nobody hates software more than software developers](https://blog.codinghorror.com/nobody-hates-software-more-than-software-developers/). I'm already running slack, so one practical way to do this is by exploiting [Slack Webhooks](https://avah.slack.com/apps/A0F7XDUAZ-incoming-webhooks). Unfortunately, all [Slack's examples for web hooks](https://api.slack.com/incoming-webhooks)  use [cURL](https://en.wikipedia.org/wiki/CURL) and I'm developing on Windows 10. Certianly, there are solutions for getting cURL on windows, I'd prefer something that works on a fresh install, surely there's a native solution.

This is where Powershell's Invoke-RestMethod comes in: 

> 	param (
> 	    [Parameter(Mandatory=$true)][string]$uri,
> 	    [Parameter(Mandatory=$true)][bool]$success
> 	 )
> 	
> 	$message = 'Build Failed'
> 	$emoji = ':rage:'
> 	
> 	if($success)
> 	{
> 		$message = 'Build Succeeded'
> 		$emoji = ':nerd_face:'
> 	}
> 	
> 	$payload = @{
> 		username='Visual Studio'
> 		text=$message
> 		icon_emoji=$emoji
> }
> 
> $json = $payload | ConvertTo-Json
> $response = Invoke-RestMethod -Uri $uri -Method POST -Body $json -ContentType 'application/json'

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
