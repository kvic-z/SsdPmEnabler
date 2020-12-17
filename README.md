**Table of Contents**
   * [About SsdPmEnabler](#about-ssdpmenabler)
   * [Confirmed working MacBook models](#confirmed-working-macbook-models)
   * [Precaution](#precaution)
   * [Pre-requisite](#pre-requisite)
      * [Disable part of SIP in Mojave, Catalina or BigSur](#disable-part-of-sip-in-mojave-catalina-or-bigsur)
      * [Disable SIP in High Sierra](#disable-sip-in-high-sierra)
   * [Installation](#installation)
      * [BigSur](#bigsur)
      * [Catalina, Mojave or High Sierra](#catalina-mojave-or-high-sierra)
      * [Confirm the KEXT loaded and in effect](#confirm-the-kext-loaded-and-in-effect)
   * [Un-installation](#un-installation)
   * [If your Mac does not boot...](#if-your-mac-does-not-boot)
   * [Copyright](#copyright)
   * [License and Disclaimer](#license-and-disclaimer)

# About SsdPmEnabler

SsdPmEnabler is a [macOS kernel extension](https://developer.apple.com/library/archive/documentation/Darwin/Conceptual/KernelProgramming/Extend/Extend.html) or KEXT in short that can enable power saving on SSD/PCIe sockets on specific MacBook Air and Pro models manufactured between 2013 and 2015. 

These models come with user replaceable SSDs. Through a small 3rd-party adapter, modern M.2 NVMe SSDs can directly plug in, replace original Apple SSDs and functiona as NVMe drives. The new SSDs have the advantages on speed and capacity over original Apple SSDs. Lots of good details can be found in this [thread](https://forums.macrumors.com/threads/upgrading-2013-2014-macbook-pro-ssd-to-m-2-nvme.2034976/) on MacRumors.

# Confirmed working MacBook models

`SsdPmEnabler.kext` is verified to function correctly on the following MacBooks and SSDs.
* 2015 13-inch MacBook Pro (MacBookPro12,1)
  * WD SN550 1TB (~40% idle power reduction with `SsdPmEnabler.kext`)
# Precaution

Enabling power saving through `SsdPmEnabler.kext` impacts how your SSDs operate. As always with any storage media, back up your data before proceed.

In rare occasions, your specific model of SSDs might not function correctly in low power states. That may possibly cause data corruption in extreme cases.

<span style="color:red">**YOU BE BEWARNED. THE AUTHOR OF THIS SOFTWARE WON'T TAKE RESPONSIBILITY FOR ANY DATA LOSS. PROCEED AT YOUR OWN RISK.**</span>
# Pre-requisite

Apple have announced deprecation of KEXTs in 2019. For this reason alone, SsdPmEnabler.kext (as a KEXT) will never be cryptographically signed by Apple. With that said many KEXTs written by Apple themselves are still in use and critical to MacOS core functionalities. It's safe to assume it'll take a few more years at minimum before Apple abandon KEXTs completely.

By default unsigned KEXTs are denied from running and this is part of Apple's [System Integrity Protection (SIP)](https://support.apple.com/en-us/HT204899). In order to load unsigned KEXTs just like some of the other 3rd-party KEXTS, part of SIP or whole SIP need to be disabled.

Granularity of control differs with MacOS versions, from all or nothing in High Sierra, to only lifting partial restriction in Mojave, Catalina or BigSur, to the new additional user consent to each unsigned KEXT in BigSur.

## Disable part of SIP in Mojave, Catalina or BigSur

1. Reboot into Recovery Mode. Click [HERE](https://support.apple.com/en-us/HT201314) for details.
2. In Terminal, type:
````
  csrutil enable --without kext
````
3. Reboot

## Disable SIP in High Sierra

1. Reboot into Recovery Mode. Click [HERE](https://support.apple.com/en-us/HT201314) for details.
2. In Terminal, type:
````
  csrutil disable
````
3. Reboot

# Installation

The basic idea is to copy `SsdPmEnabler.kext` into `/Library/Extensions`, update KEXT cache, and load the KEXT. BigSur requires two addtional steps: users explicitly grant permisson to load `SsdPmEnabler.kext` when prompted, and then reboot to take effect.

The following steps assume users place the downloaded `SsdPmEnabler.kext` inside `~/Downloads.` Adjust the location of your download if necessary.

## BigSur

1. Open Terminal. Type the following lines one by one:
````
 sudo cp -R ~/Downloads/SsdPmEnabler.kext /Library/Extensions
 sudo kextcache -i /
 sudo kextload /Library/Extensions/SsdPmEnabler.kext
````

2. Once you run the third line in Step 1, MacOS will ask for your permission. Follow the system's instructions on screen.

3. Reboot

## Catalina, Mojave or High Sierra

1. Open Terminal. Type the following lines one by one:
````
 sudo cp -R ~/Downloads/SsdPmEnabler.kext /Library/Extensions
 sudo kextcache -i /
 sudo kextload /Library/Extensions/SsdPmEnabler.kext
````

2. That's it!

## Confirm the KEXT loaded and in effect


1. Open Terminal. Type the following line:
````
 log show --style syslog --last boot | grep kernel.*SsdPmEnabler
````

2. All good if you see these three lines in the output
````
kernel: (SsdPmEnabler) Copyright (c) 2020-2021 kvic (https://github.com/kvic-z/SsdPmEnabler)
kernel: (SsdPmEnabler) Enabled PCIe PM on SSD
kernel: (SsdPmEnabler) Couldn't alloc class "com-ZLab-SsdPmEnabler"
````

If the second line is missing, power saving fails to enable on the SSD/PCIe socket.

# Un-installation

Sad to see you leaving. Perhaps next time `SsdPmEnabler.kext` will work better for you.

1. Open Terminal. Type the following command lines:

**WARNING**. Double check for typo before you press ENTER key
````
 cd /Library/Extensions
 sudo kextunload SsdPmEnabler.kext
 sudo rm -rf SsdPmEnabler.kext
 sudo kextcache -i /
````
2. Reboot

# If your Mac does not boot...

This should not ever happen. Touch wood if it happens to you right after installinng `SsdPmEnabler.kext`. Here are the steps to remove the KEXT.

1. Reboot into Recovery Mode. Click [HERE](https://support.apple.com/en-us/HT201314) for details.

2. Open Terminal. Type the following three command lines one by one (including double quotes).

Note. For **BigSur, Mojave and Catalina**, if your system disk is named 'macOS' substitute 'NAME' with 'macOS - Data' or else adjust accordingly.

For **High Sierra**, if your system disk is named 'Macintosh HD' substitute 'NAME' with 'Macintosh HD' or else adjust accordingly.

**WARNING**. Double check for typo before you press ENTER key
````
 cd "/Volumes/NAME/Library/Extensions"
 rm -rf SsdPmEnabler.kext
 touch .
````

3. Reboot

# Copyright

Copyright (c) 2020-2021. kvic

# License and Disclaimer

SsdPmEnabler.kext is licensed free of charge to individual owners of devices where the software is installed and run for personal, educational and/or commercial purpose.

For 1) bulk deployment in SMEs, enterprises or through technical service centres including but not limited to repair shops; 2) bundling in free or paid software or other commercial products, please contact the author at https://github.com/kvic-z for permission.

SsdPmEnabler.kext is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
