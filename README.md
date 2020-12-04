# ArmourDeploy

ArmourDeploy is a swift app developed by ArmourPeak. Invoked from the command-line, it lets Mac admins easily deploy a macOS installer with a single command. If you're a Mac Admin, then you're most likely familiar with Apple's 'startosinstall' tool located inside the macOS Install.app. ArmourDeploy is part wrapper for 'startosinstall', part downloader for "Install macOS.app", and all awesome.

ArmourDeploy provides 'Erase All Contents and Settings' functionality for macOS, as well as saving time by maintaining a cached copy of the latest macOS installer on each Mac in the fleet. There is no need for an admin to package and host a macOS installer. The latest build of macOS, compatible with the current running hardware, is downloaded from Apple's servers whenever it is needed. Use your RMM or create a LaunchDaemon to run ArmourDeploy on a regular schedule to ensure your managed Macs have the most up-to-date build of macOS cached on every system. Specify which OS will be cached, including multiple OS versions. Keep a copy of Mojave cached for maintaining Mojave, while also caching the latest Catalina release for providing OS upgrades to Apple's newest Operating System.

What if we choose not to cache a copy of the macOS installer on every Mac? No problem! ArmourDeploy will combine all its features into one continuous workflow, allowing for "on-demand" macOS Installer download and execution of 'startosinstall' as a a single-click operation. ArmourDeploy will display a window for providing progress.

![](https://static.wixstatic.com/media/9a545e_3c7396e2782945d180d3b9427dce47c5~mv2.png/v1/fill/w_1824,h_1520,al_c/Screen%20Shot%202020-02-14%20at%2010_21_59%20AM_pn.png)
>User interface for Apple's 'startosinstall' tool, displaying progress that the system is preparing. This is a great option when executed from a "Self Service" style tool. Until now, this has been a pain point for admins, since there was no feedback provided to the user that the system is preparing to restart and install (and potentially erase) macOS.

![enter image description here](https://static.wixstatic.com/media/9a545e_5f874e56e80e4e47be10b1ec0b6e8eaf~mv2.png/v1/fill/w_1824,h_1520,al_c/Screen%20Shot%202020-02-14%20at%2010_23_08%20AM_pn.png)
>When performing an "erase" operation, an optional captcha can protect the system from accidental erasure. This option can be disabled for fully automated, unattended erase and install workflows.

![enter image description here](https://static.wixstatic.com/media/9a545e_22409201b5f14b1ab9e300383778cd4c~mv2.png/v1/fill/w_1824,h_1520,al_c/Screen%20Shot%202020-02-14%20at%2010_35_02%20AM_pn.png)
>For Macs that do not already have a macOS installer cached on the system, ArmourDeploy will provide a user interface displaying progress while downloading the OS from Apple, assembling the installer app, and executing 'startosinstall', all with a single click.

![enter image description here](https://static.wixstatic.com/media/9a545e_c16510e87b9d4a73a2839e8efd8e2514~mv2.png/v1/fill/w_1136,h_827,al_c/SS_shot.png)
>Example policies in Jamf Pro Self Service. User-initiated, single-click policies will launch ArmourDeploy and perform OS installations and upgrades, even if the user is not an administrator on the Mac. Scope the "Erase All Content & Settings" policies to Help Desk users, who can login to Self Service and run these policies.

ArmourDeploy floats above all other windows, constantly reminding the user that macOS is about to be installed. There is no menu bar or dock item, which keeps the user from quitting the app. For system erase operations, ArmouDeploy always displays a 'cancel' button. Otherwise, the 'cancel' button is not shown unless the --showcancel flag is provided. If no user is logged into the Mac, the GUI will be suppressed, but all operations will continue in the background. Have a look at the various options listed below for running ArmourDeploy.

## RELEASE
https://github.com/armourpeak/ArmourDeploy-Release/releases

## USAGE

    ArmourDeploy --action install | ArmourDeploy --action cache | ArmourDeploy --action status

## REQUIREMENTS
Elevated privileges (sudo). The --erase option requires ArmourDeploy to be run from an APFS formatted volume

## COMPATIBILTIY
macOS High Sierra, macOS Mojave, macOS Catalina

USAGE
-------

	--action, -a (Required)

		-install - Runs startosinstall and presents a user interface displaying progress for the 'Preparing' phase of the installer prior to the reboot
		-cache - Caches the latest available build of macOS (specified by --osname) from Apple that is compatible with this Mac. Schedule runs to ensure the latest available build from Apple is being cached. Returns cached build name if installer is currently cached
		-status - returns true or false if an OS specified by --osname is currently cached. Good for checking status from RMM

		When --action cache install are used together, a user interface displays overall progress. If macOS is already cached, the cached installer is used if it is the most current, and the behavior is the same as using --action install. If macOS is not already cached, the UI will display progress for downloading macOS components, assembling the components into the .app, and the 'Preparing' phase for startosintall. This option is intended for systems that should run startosinstall, but don't have the convenience of a cached installer present on the system.

	--appfolder - Leave the macOS installer in /Applications. By default the installer will be moved to our Application Support folder. If left in /Applications, macOS may remove it after use.

	--erase - Erase All Content And Settings, install a new copy of macOS. Requires --action install --osname name

	--noforcequit - By default, when using the --erase option, ArmourDeploy will force quit any apps after the 'Preparing' phase, regardless of any unsaved documents, or apps that may prevent logout etc. Use this option to override and prompt to close things safely.

	--noverify - Supresses the captcha presented when using the --erase option only. Use this for unattended erase workflows. Implied if no user is logged in to perform the challenge.

	--osname, -n - (Required) - Name of the macOS Installer to use (ie. Catalina, Mojave, High Sierra). Required for --action install, --action cache, and --action status actions.

	--showcancel - Show the cancel button in the UI. The cancel button will always be shown during an --erase operation, but is optional for an install/upgrade.

	--help, h - Display available options
    



## EXAMPLES
Cache Install macOS Catalina.app - If the latest is already cached, returns cached build number. Replaces a build if not the latest (CLI):

    /Library/Application\ Support/com.armourpeak.armourdeploy/ArmourDeploy.app/Contents/MacOS/ArmourDeploy --action cache --osname catalina

Install/Upgrade macOS Catalina.app (GUI):

    /Library/Application\ Support/com.armourpeak.armourdeploy/ArmourDeploy.app/Contents/MacOS/ArmourDeploy --action install --osname catalina

Erase all content and settings and install macOS Catalina, suppress the captcha (GUI):

    /Library/Application\ Support/com.armourpeak.armourdeploy/ArmourDeploy.app/Contents/MacOS/ArmourDeploy --action install --osname catalina --erase --noverify

Cache and download macOS Catalina if it doesn't exist, erase all content and settings and suppress the captcha (GUI):

    /Library/Application\ Support/com.armourpeak.armourdeploy/ArmourDeploy.app/Contents/MacOS/ArmourDeploy --action cache install --osname catalina --erase --noverify

## DEBUG

From the local mac, or ssh into the mac running ArmourDeploy:

    log stream --predicate 'subsystem == "com.armourpeak.armourdeploy"' --debug --info

## FAQ
Why no support for "Big Sur"? It's coming. I'm a busy guy and not a software developer. This tool is used for our Managed Services clients, and we're not rolling out Big Sur yet. 

Does ArmourDeploy use Apple’s  softwareupdate —fetch-full-installer  to download macOS Catalina?

No. Our alpha versions of ArmourDeploy used this feature, and the code base still exists for possible future use, but there were a few reasons we shelved this.

- softwareupdate command launches the “Install macOS Catalina.app” when it finishes downloading. For keeping a cached version “hidden away”, we did not want the app to launch. We built a ‘watcher’ that kills the installer app when it launches, but the app can still become visible for a moment before the ‘watcher’ would kill it. The installer will also be inserted in the “Recent Items” of a user’s Dock


- We wanted to support Mojave and High Sierra for our clients

Can I remove or replace the ArmourPeak logo?

If you would like a “white label” copy of ArmourDeploy for use in your organization or MSP, please reach out to software@armourpeak.com.
We'll be adding support for branding in a release shortly around when we release support for Big Sur.

Where's the code? Sorry, I'm not a developer, and my swift skills are still pretty caveman. Maybe I'll open the code up at some point if there's any interest.


