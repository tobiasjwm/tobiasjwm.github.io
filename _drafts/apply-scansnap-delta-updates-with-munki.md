---
layout: post
title: Apply ScanSnap Delta Updates with Munki
tags: packaging munki
---

In my [last post][a] about building a ScanSnap Manager installer that installs successfully in all contexts, I mentioned that Fujitsu's deployment strategy includes delta updaters. As Frank Zappa says, [the torture never stops][b].

The good news is that Fujitsu is sticking with the same deployment strategy with their delta updates as with their main installer. Yes, this is bad news in that there are scripts assuming that the installer is run by a user sitting at the machine, logged into their account. There are wizards and pop ups and an assumption that the user has admin rights on the device. But our work is similar in that all the changes we need to make in the preinstall and postinstall scripts are essentially the same.

Our focus in this article will be on building our pkginfo for Munki to assure existing installs receive their updates so that Macs running Sierra and High Sierra work properly.<!--more--> See, the delta updates don't add any functionality, they are simply compatibility updates for the latest two macOS versions. And since we installed ScanSnap Manager without the AOU app, the user will never know why ScanSnap is behaving oddly because there is no mechanism in the app to prompt the user for available updates.

To get started, prepare your installers following the instructions in my [previous post][a].

For ease of explanation, we will use the excellent [MunkiAdmin][c] to add our customizations to the

And before we dig in, there is one other important issue to be aware of: the two delta updates available as of this writing are cascading. If you want a patched, working version of ScanSnap Manager on macOS High Sierra, you need to install the main package then _both_ of the deltas. Attempting to install the 6.3.70 update on the 6.3.50 base install throws the error `package requires 6.3.61 update`.





[a]:{{ site.baseurl }}{% post_url /_posts/2018-02-19-build-a-deployable-scansnap-manager-installer %}
[b]:https://youtu.be/iwkCyoC4s4k
[c]:https://github.com/hjuutilainen/munkiadmin/releases/