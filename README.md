---
page_type: sample
products:
- office-365
languages:
- csharp
title: Microsoft Teams C# Helloworld Sample
description: Microsoft Teams "Hello world" application for .NET/C#
extensions:
  contentType: samples
  platforms:
  - CSS
  createdDate: 10/16/2017 10:02:21 PM
---

# Demo script

git clone https://github.com/nkramer/graphbot.git -b signalr
(note branch)

Q&A Tracker is a tab that uses resource-specific consent to read messages and get notified of new messages (Webhooks). It also installs a bot but you should ignore that for now. It should not be used in the production tenant until some additional security work is done in the app.

For reasons I haven't figured out Webhooks aren't working in the Azure-deployed version, so you'll need to run from ngrok.

Q&A Tracker comes in several flavors:
- (Azure) -- this is the main one you should use. 
- (Azure, NoRSC) -- uses the Q+A Tracker graph app w/ user delegated permissions
- (ngrok) -- runs off my local machine :)

You can find all of these versions in the tenant app catalog.

All versions of the app require you to establish your identity by logging in with user delegated permissions.  For the RSC version, its an app that has only the openid permission. It's not an especially clever implementation of tab auth -- it's not even silent sign-on, much less SSO. But there's no reason other than time in the day that we couldn't add those. 

Graph app ids:
- RSC uses "Q+A Tracker" with application permissions, and does authn with "HelloBot" user delegated.
- NoRSC uses the "Q+A Tracker" graph app w/ user delegated permissions.

Both appids are registered in the TEST_TEST_IP_TEST tenant, owned by nkramer & billbl.

Webhook functionality is fragile:
- You can only set up one subscription at a time per tenant! So don't run this app in two separate channels within 10 minutes of each other.
- Subscriptions only last for 10 minutes, to help mitigate the above.
- If the subscription set up fails for some reason, it will fail silently so you can at least demo reading messages. 
- When a new message comes in, it will reload the Q&A tab – even if the tab was not in the same channel as the message. 
- The non-RSC version doesn't support webhooks, because we don't have user delegated webhooks.

Demo setup:
- https://teams.microsoft.com , launch the webclient.
- Log in to @teamsip.onmicrosoft.com. Any account will do.
- switch to R1.5 to avoid showing unrelated UI changes we aren't ready to talk about.
- Create a new team so there's not too much clutter in the general channel.
- Add some questions. Q&A Tracker will only look at root messages (not replies), it only looks at the most recent 30 messages (including control messages), and it only looks at messages that contain a question mark.
- Like some of those questions -- questions will be ranked by reaction count.
- Create a second window, logged into the same account or a different account that's a member of the team you just created
- For the best demo, install your app to the LOB catalog rather than sideload on stage.
- If you have to sideload, you can do it from the Team | Manage Team | Apps | Upload a custom app screen. 
- For maximum sanity, uninstall your app before you install a new version.

Demo steps:
- Click + Pin a tab button. Pick "Q&A Tracker (Azure)". Search in tenant app catalog (Built for TEST_TEST_IP_TEST) if necessary.
- See app details V3. Scroll to bottom of dialog, talk about the new RSC permissions.
- Click Add.
- Uncheck "Post to the channel about this tab" to reduce the clutter in your channel.
- Click Save.
- If tab errors out, refresh – we've have some live sites today due to a CSA deployment.
- Mark one or two questions as answered so the app looks more real
- go to your other window, and type in a new question. (Must be a root message, must contain a question mark)
- Wait ~5 seconds for it to show up in Q&A Tracker. (Most of this is webhook – despite Q&A Tracker rerunning from scratch everything on both client and server with every new message, the webhook is the slow part.)

Please don't share the source code yet – it has security issues, is very messy, very inefficient, and doesn't represent best practices for tab auth and webhook set up.