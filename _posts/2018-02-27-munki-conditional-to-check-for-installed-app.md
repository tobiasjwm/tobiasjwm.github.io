---
layout: post
title: A Munki Conditional to Check for Installed App
excerpt_separator: <!--more-->
tags: munki bash luggage
---
We use [Box][a] for file storage and sharing. Box has an excellent file management experience in the browser with the addition of [Box Edit][b] for interacting with native apps like Microsoft Word. But old habits die hard and employees love their Finder and so we have [Box Sync][c] scattered throughout a few offices. But Sync's days are numbered as Box has been running a public beta of their new [Box Drive][d] app which offers a hybrid webdav/[FUSE][e] experience that presents like a local file server connection. 

## The Problem

There are a couple of issues to consider as we look at deploying Box Drive. The showstopper at this point is the guidance driectly from Box that Drive and Sync _cannot_ co-exist on the same Mac. Also, there is the matter of the "beta" label. The headline feature keeping Box Drive in beta is offline file access, which many Sync users "cannot live without". 

As we move to a more employee-centric self-service model, I want to make Drive available as an optional install in Managed Software Center, but I don't want to offer it to my Box Sync users yet. I cannot assume they will read the app description. I cannot even assume putting up a pre-install alert informing the employee that they _will lose offline access_ will result in a full understanding of the implications of moving at this point in time. My solution is to check the local machine for the existence of Box Sync.app and prevent it from being offered to those employees.<!--more-->

## The Solution

Although I can prevent installation where Sync is already present (and for safety, we do), the best experience is for us to simply hide this from Sync users. And to acomplish that, we are going to use an [admin provided condition][f].

On each `managedsoftwareupdate` run, Munki will check for scripts in `/usr/local/munki/conditions/` and run these before starting software checks. These scripts should be crafted to write values into ConditionalItems.plist in your Managed Installs directory. Any conditionals included in your manifests will then be checked against the values in this plist. With this in mind, we just need to craft a file test that writes a boolean value to the plist and then check for the value we require to determine if we display the optional or not.

A quick check in with my favorite group of misfits over at the MacAdmins Slack and @eholtam threw me an example bash script. And with that, I had a simple bash script to check for the app and write a true or false value for Munki to check.

	#!/bin/bash
	
	# variables
	plistLocation="/Library/Managed Installs/ConditionalItems"
	applicationLocation="/Applications/Box Sync.app/Contents/MacOS/Box Sync"
	prefName=isBoxSyncInstalled

	#	file test
	if [ -e "${applicationLocation}" ]
    	then
       		defaults write "${plistLocation}" "${prefName}" -bool true
    	else
    		defaults write "${plistLocation}" "${prefName}" -bool false
	fi
	plutil -convert xml1 "${plistLocation}".plist

Make this script executable and place it in `/usr/local/munki/conditions/`. For ease of use, I have included a Luggage Makefile with the [script at GitHub][h].

With that script in place, we now need to create our condition in a manifest. Since we are setting a boolean value, we just need to check if the value for `isBoxSyncInstalled` is 0. While we are at it, the app only supports OS X 10.11 and above, so let's use the built-in condition `os_vers_minor` to check for that as well. For ease of use, I suggest using [MunkiAdmin][g] to add the following line to your manifest as the conditional formatting is a bit esoteric and `>` isn't valid in an XML file.

	isBoxSyncInstalled == 0 AND os_vers_minor >= 11

Now we can offer Drive to only the the low-hanging fruit of experienced users and new users who haven't made an investment in Sync. Later perhaps we will look at the challenges of removing Sync once we are ready to go all-in on Drive.


[a]:https://www.box.com
[b]:https://app.box.com/services/box_edit
[c]:https://sites.box.com/sync4/
[d]:https://www.box.com/resources/downloads/drive
[e]:https://osxfuse.github.io/
[f]:https://github.com/munki/munki/wiki/Conditional-Items#admin-provided-conditions
[g]:https://github.com/hjuutilainen/munkiadmin/releases
[h]:https://github.com/tobiasjwm/packaging/tree/master/munki_scripts/Conditions/BoxSyncDetector