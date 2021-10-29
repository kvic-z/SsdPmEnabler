**Table of Contents**
   * [About ssdpmEnabler](#about-ssdpmenabler)
   * [Confirmed working MacBook models](#confirmed-working-macbook-models)
   * [Precaution](#precaution)
   * [Prerequisite](#prerequisite)
      * [Disable part of SIP in High Sierra or later](#disable-part-of-sip-in-high-sierra-or-later)
   * [Installation](#installation)
      * [Monterey / Big Sur](#monterey--big-sur)
      * [Catalina, Mojave or High Sierra](#catalina-mojave-or-high-sierra)
      * [Confirm ssdpmEnabler working properly](#confirm-ssdpmenabler-working-properly)
   * [Update](#update)
   * [Un-installation](#un-installation)
   * [If your Mac does not boot...](#if-your-mac-does-not-boot)
   * [If SsdPmEnabler.kext is not loaded anymore](#if-ssdpmenablerkext-is-not-loaded-anymore)
   * [Copyright](#copyright)
   * [License and Disclaimer](#license-and-disclaimer)

# About ssdpmEnabler

ssdpmEnabler is a [macOS kernel extension](https://developer.apple.com/library/archive/documentation/Darwin/Conceptual/KernelProgramming/Extend/Extend.html) or KEXT in short that can enable power saving on SSD/PCIe sockets on specific MacBook Air and Pro models manufactured between 2013 and 2015. 

These models come with user replaceable SSDs. Through a small 3rd-party adapter, modern M.2 NVMe SSDs can directly plug in, replace original Apple SSDs and function as NVMe drives. Replacement details can be found in this [thread](https://forums.macrumors.com/threads/upgrading-2013-2014-macbook-pro-ssd-to-m-2-nvme.2034976/) on MacRumors.


Modern SSDs have advantage on speed and capacity over original Apple SSDs. With ssdpmEnabler, new SSDs on supported MacBook models also enjoy suprisingly good power saving. Specific SSD models could save as good as or in some reported cases even better than original Apple SSDs.

# Confirmed working MacBook models

This section has been expanded and moved to [its own page](https://github.com/kvic-z/SsdPmEnabler/wiki/Confirmed-working-Mac-models).

# Precaution

Enabling power saving through ssdpmEnabler impacts how your SSDs operate. As always dealing with any storage media, back up your data before proceed.

On rare occasions, your specific models of SSDs might not function correctly in low power states. That may possibly cause data corruption in extreme cases.

<span style="color:red">**YOU BE WARNED. THE AUTHOR OF THIS SOFTWARE WON'T TAKE RESPONSIBILITY FOR ANY DATA LOSS. PROCEED AT YOUR OWN RISK.**</span>

# Prerequisite

Apple have announced deprecation of KEXTs in WWDC 2019. For this reason alone, ssdpmEnabler (as a KEXT) will never be cryptographically signed by Apple. With that said many KEXTs written by Apple themselves are still in use and critical to macOS core functionalities. It's safe to assume Apple will take a few more years at minimum to abandon KEXTs completely.

By default unsigned KEXTs are denied from running and this is part of Apple's [System Integrity Protection (SIP)](https://support.apple.com/en-us/HT204899). In order to load unsigned KEXTs just like some of the other 3rd-party KEXTS, part of SIP or SIP in its entirety need to be disabled.

Granularity of control differs with macOS versions, from all or nothing in High Sierra, to only lifting partial restriction in Mojave, Catalina or Big Sur, to the new additional user consent to each unsigned KEXT in Big Sur.

## Disable part of SIP in High Sierra or later

1. Reboot into Recovery Mode. Click [here](https://support.apple.com/en-us/HT201314) for details.
2. In Terminal, type:
````
  csrutil enable --without kext
````

3. Reboot

**N.B.**
* Ignore the warning related to unsupported feature in Step 2.

# Installation

The basic idea is to copy `SsdPmEnabler.kext` into `/Library/Extensions`, update KEXT cache, and load the KEXT. Big Sur requires two additional steps: users explicitly grant permisson to load `SsdPmEnabler.kext` when prompted, and then reboot to take effect.

Latest version of `SsdPmEnabler.kext` can be downloaded from the [Release](https://github.com/kvic-z/SsdPmEnabler/releases) page. The following steps assume users place the downloaded `SsdPmEnabler.kext` inside `~/Downloads.` Adjust the location of your download if necessary.

## Monterey / Big Sur

1. Open Terminal. Type the following command line:
````
 sudo cp -R ~/Downloads/SsdPmEnabler.kext /Library/Extensions
````

2. Once you run the command in Step 1, macOS will pop up a window to alert you about the new KEXT.

Click "Open Security Preferences" on the pop-up. Then click "Allow" to grant `SsdPmEnabler.kext` permission to run.

3. Reboot to take effect

**N.B.**
* There is no EXTRA slash after `SsdPmEnabler.kext` in the command line in Step 1.
* Ignore the invalid signature warning in Step 1
* `SsdPmEnabler.kext` will not be able to run if you do not grant permission in Step 2.
* See this [screenshot](https://raw.githubusercontent.com/kvic-z/SsdPmEnabler/main/New_Security_and%20Privacy_in_BigSur.png) for where to find the "Allow" button in Step 2.

## Catalina, Mojave or High Sierra

1. Open Terminal. Type the following lines one by one:
````
 sudo cp -R ~/Downloads/SsdPmEnabler.kext /Library/Extensions
 sudo kextcache -i /
 sudo kextload /Library/Extensions/SsdPmEnabler.kext
````

2. That's it!

**N.B.**
* There is no EXTRA slash after `SsdPmEnabler.kext` in the first command line in Step 1.
* Ignore the invalid signature warning in Step 1

## Confirm ssdpmEnabler working properly


1. Open Terminal. Type:
````
 log show --style syslog --last boot | grep \(SsdPmEnabler
````

2. If you see the following two lines in the output, all good!
````
kernel[0]: (SsdPmEnabler) Copyright (c) 2020-2021 kvic (https://github.com/kvic-z/SsdPmEnabler)
kernel[0]: (SsdPmEnabler) Enabled PCIe PM on SSD
````

If "Enabled PCIe PM on SSD" is missing, sorry, ssdpmEnabler cannot help your SSDs on power saving.

**N.B.**
* If you see "Couldn't alloc class" warning in the output, it's safe to ignore.

# Update

Hey, good that we have bug fix or enhancement. So in the rare occasions of a new version, use the following steps to update your installed `SsdPmEnabler.kext`.

1. Open Terminal. Type:

**WARNING**. Double check for typo before you press ENTER key

```
cd /Library/Extensions
sudo kextunload SsdPmEnabler.kext
sudo rm -rf SsdPmEnabler.kext
```

2. Proceed to the sub-section for your macOS version under Installation. Redo installation from there.

# Un-installation

Sad to see you leaving. Perhaps next time ssdpmEnabler will work better for you.

1. Open Terminal. Type the following command lines:

**WARNING**. Double check for typo before you press ENTER key
````
 cd /Library/Extensions
 sudo kextunload SsdPmEnabler.kext
 sudo rm -rf SsdPmEnabler.kext
 sudo kextcache -i /
````
2. Reboot into Recovery Mode. Click [here](https://support.apple.com/en-us/HT201314) for details.

3. Open Terminal. Enable SIP in its entirety by typing:
````
 csrutil enable
````

4. Reboot

# If your Mac does not boot...

This should not ever happen. Touch wood. If you run into it right after installing `SsdPmEnabler.kext`. Here are the steps to remove the KEXT.

1. Reboot into Recovery Mode. Click [here](https://support.apple.com/en-us/HT201314) for details.

2. Open Terminal. Type the following three command lines one by one (including double quotes).

Note that for **Big Sur, Mojave and Catalina**, if your system disk is named 'macOS' substitute 'NAME' with 'macOS - Data' or else adjust accordingly.

For **High Sierra**, if your system disk is named 'Macintosh HD' substitute 'NAME' with 'Macintosh HD' or else adjust accordingly.

**WARNING**. Double check for typo before you press ENTER key
````
 cd "/Volumes/NAME/Library/Extensions"
 rm -rf SsdPmEnabler.kext
 touch .
````

3. Reboot

# If SsdPmEnabler.kext is not loaded anymore

Check "System Information.app" > Software > Extensions > SsdPmEnabler.

If the "Loaded" column says "No" then check the details for the attribute: "Signature Validation Error".

If it says "Kext signature validation error code -67050" then this means that SIP was again entirely enabled and as SsdPmEnabler.kext has no signature the loading of the kernel extension is prohibited and hence fails.

## What can lead to SIP being entirely re-enabled?
- macOS upgrade
- Parameter RAM Reset (CMD-ALT-P-R during startup)

## How do I get SsdPmEnabler working again?
Perform the instructions of these sections in the given order: Un-installation, Prerequisite, and Installation.

# Copyright

Copyright (c) 2020-2021. kvic

# License and Disclaimer

SsdPmEnabler.kext is licensed free of charge to individual owners of devices where the software is installed and run for personal, educational and/or commercial purpose.

For 1) bulk deployment in SMEs, enterprises or through technical service centres including but not limited to repair shops; 2) bundling in free or paid software or other commercial products, please contact the author at https://github.com/kvic-z for permission.

SsdPmEnabler.kext is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
