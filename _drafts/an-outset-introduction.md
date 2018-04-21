---
layout: default
title: An Introduction to Outset
tags: outset packaging
---

_Buckle in kids, this is a long one. This is a foundation for the next few posts, as they require the use of Outset to do their magic._

[Outset][a] is a Python tool by the inimitable [Joe Chilcote][b] that installs packages, profiles and runs scripts at boot or at login. It also includes a couple of special features to catch the most common strategies including running once or every time and running scripts as a privileged user (root) at login, (which normally runs as user).<!--more--> 

## TL;DR

Outset is a solid tool with a strong community.
Running scripts as a logged in user is useful for first-time account set ups and configuring apps in user land.

### How it Works

Outset leverages the power of `launchd` by installing several LaunchDaemons and LaunchAgents that trigger the Outset application at boot or login. the process is fairly straight-forward; Outset has a series of directories at `/usr/local/outset/`, one for each run context. When Outset is invoked, it processes everything in these directories by context, writes the completion of any "once" items to a plist then cleans up.

This is, of course, an over-simplification of the process, but the details are only important in complex workflows. When things get complicated, you can dig into the more complex moving parts which allow you to create a specific run order for items in a particluar folder, change the context for running login scripts from 'as user' to 'as root' and even exclude user accounts from Outset runs. You can get all the details at the [Outset wiki][osw].

### Deploying Outset

Outset deployment should be accomplished using a modular approach; deploy the app then deploy your custom items, preferably individually so that you have more options when making workflow changes.

To deploy the app, simply grab the latest from the [GitHub releases page][grp] and add it to your management tool or, better yet, add the [AutoPkg recipe][apr] to assure you never miss an update. You are running [AutoPkg][ap], right?

To deploy your items, you can [git clone the Outset GitHub repo][ghc] to get the "custom-outset" directory. Here you will find the six outset directories representing the context in which you want your items to run in. This directory includes a makefile that will build a package containing any items you add to the directories in `pkgroot/usr/local/outset`. Note that you will want to remove the example scripts from each directory. These are required for making the example available as Github ignores empty directories.

Say you want to include a [dockutil script][dus] to customize a user Dock. Write your script and make it executable (`chmod a+x /path/to/script`) then drop it in the `login-once` folder,  then `cd` to your custom-outset folder and customize the PKGTITLE, PKGVERSION, PKGID, PROJECT variables to your unique values then run `make pkg`. If you are a fan of [The Luggage][tl]  like me, I suggest creating a (or modifying your existing) luggage.local file with the values in Eric Gomez's [Outset Luggage Companion][olc] to make creating outset item installs simple, painless and auditable.

### Special Features

- Disabling loginwindow process to wait for a network connection
- Running login scripts as root
- Allows user accounts to be excluded from login runs
- Executing items in specific order
- Executing items "on demand"




[a]:https://github.com/chilcote/outset
[b]:any about page for Joe. Perhaps a conference page. 
[osw]:https://github.com/chilcote/outset/wiki/FAQ
[grp]:https://github.com/chilcote/outset/releases
[apr]:NEED LINK TO OUTSET RECIPE
[ap]:https://github.com/autopkg/autopkg
[ghc]:https://github.com/chilcote/outset.git
[dus]:https://github.com/chilcote/outset/wiki/Dockutil
[tl]:THE LUGGAGE LINK
[olc]:https://blog.eriknicolasgomez.com/2015/03/25/outset-luggage-companion/