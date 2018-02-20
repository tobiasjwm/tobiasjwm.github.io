---
layout: post
title: Installing ScanSnap Manager with Munki
excerpt_separator: <!--more-->
---

ScanSnap scanners are extremely popular among my clients. They are fast, efficient, and output a high-quality PDF. The ScanSnap Manager application makes it simple for average users to very simply create powerful workflows that include running OCR (Optical Character Recognition), rule-based filing and other type and quality modifications.

## The Problem

With its software distribution strategy, Fujitsu has thrown out the [tenants of good packaging][ten]. If you go to the [installer download page][a] you get a package that assumes that all installations are completed by the user and worse, includes a slew of additional components for their Automatic Online Update (AOU) software which is *only* Automatic in that it prompts the user to go through a painful and confusing process of installing multiple packages with multiple password prompts and DMGs and packages mounting and unmounting in userland. Did I mention that the Update process itself takes forever*?

Fujitsu does [offer an installer][b] that does not install the AOU components, but like its big sister, it also assumes you are running the installer manually as a logged in user and breaks as many of the rules of good packaging as possible including running unnecessary commands in *both* preinstall and postinstall scripts, triggering Wizards and popping up show-stopper user prompts using AppleScript (!). Both  installers are just *bad*.

## The Solution

Repackaging. *sigh*
<!--more-->
We are going to have to rip this baby apart, modify both the preinstall and postinstall scripts, and then put it all back together and test the hell out of it to see if our assumptions are correct and the installer does not stall or fail to create a usable application.

## The Process

To create a package that we can run with Munki, we will be extracting the package from the DMG, unpacking the installer with `pkgutil`, commenting out any commands running in userland then re-packaging so we can import it into Munki.

1. **Get the latest version.** As described above, we want to get the ScanSnap Manager installer *without* the AOU package and other dead weight. As of winter 2018, you can find this [installer here][b], which is not where it was last time I found it, so your milage may vary. Scroll to the table at the bottom of the page and click **Download** next to "ScanSnap Manager for Mac  V6.3". The link extracted from the page is not a direct download link. Sorry. :(
2. **Mount the DMG and extract the package.** Here we will use `pkgutil` to extract the package components so that we can mangle the install scripts. For more on this process, see Armin Briegel's excellent [Packaging for Apple Administrators][c].

	```
	$ hdiutil mount ~/Downloads/MaciX500ManagerV63L50WW1.dmg
	expected   CRC32 $CE13EEFB
	/dev/disk2          	Apple_partition_scheme
	/dev/disk2s1        	Apple_partition_map
	/dev/disk2s2        	Apple_HFS                      	/Volumes/ScanSnap
	$ pkgutil --expand /Volumes/ScanSnap/ScanSnap\ Manager.pkg scansnap_edit
	```

3. **Edit the preinstall script.** Now that our package has been freed, we can open the scripts and make our changes.

	```
	$ bbedit ~/scansnap_edit/scansnapManager.pkg/Scripts/preinstall
	```

4. **Comment out everything targeting userland and a logged in user.** Here you are on your own as I cannot be held responsible for your modifications. I suggest you find any references to "$HOME", "$USER" and "osascript". Since our goal is to make this happen at the login window with no logged in user, none of this will work anyway and will simply stall the installation.
5. **Rinse and repeat.** Save the results of your prolific mangling an do the same for the postinstall script.
6. **Repackage.** Now that we have butchered the scripts, let's put this back together so that we have a usable installer package. *Note that since we have modified the package, the signing is no longer valid. If we try to run this manually Gatekeeper will be very upset with us. Because we are going to install this with Munki (or your RMM of choice) we don't have to be concerned with this.*

	```
	$ pkgutil --flatten scansnap_edit/ ScanSnap_Manager-6.3.50.pkg
	```

7. **Test, test, test.** Your co-workers are not your lab rats. Test your install scenarios to assure it works as expected in all your install scenarios. Again, I recommend [Armin's book][c] for tips on testing installers.

### Parting thoughts

In considering this strategy, my initial concerns were around login window installs. At the time I developed this strategy in 2015 we were micro-managing our client devices. Install requests would come in and we would add the software to machine manifests. In its vanilla form, the ScanSnap Manager installer would _eventually_ finish once the scripts waiting for user input would eventually time out but this had two problems; sometimes the scripts _wouldn’t_ time out before Munki, resulting in a failed install and in any case, waiting for scripts to time out would cause the process to take in excess of 45 minutes. it only took one unhappy employee report of being unable to use their computer in the morning for me to realize this was an untenable solution.

Now that we are encouraging self-service with Managed Software Center, there is the further consideration of wrapping the user-land commands in a test for a logged in user so that we can allow the wireless setup wizard to run for example. But that seems like a lot of work and I am lazy. Plus, we still need to build the Delta updated so that this will actually run properly on 10.12 and above.


[ten]:https://www.afp548.com/2010/06/03/the-commandments-of-packaging-in-os-x/
[a]:http://www.fujitsu.com/global/support/products/computing/peripheral/scanners/scansnap/software/ix500m-setup.html
[b]:http://scansnap.fujitsu.com/global/dl/setup/m-ix500-inst.html?MODEL=5018
[c]:https://itunes.apple.com/us/book/packaging-for-apple-administrators/id1173928620?mt=11
