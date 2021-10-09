Preface
-------
Set up and configure linux for the Asus ROG Strix GL703GS. Configuration is applicable to GL703 and GL503 models.

Kernel
------
Ensure the distribution's kernel supports the features you want:

* Fan control (5.7+)
* Fn keys (5.11+)

Kernel 5.7 fixed fan_boost_mode_store causing 100% CPU utilization when changing fan speed[[1]](https://github.com/schaduwen/gl703/blob/master/README.md#references). Kernel 5.11 added support for the keyboard Fn keys[[2]](https://github.com/schaduwen/gl703/blob/master/README.md#references).

Fix Headphone/Microphone Jack
-----------------------------
During kernel 5.7 on Tumbleweed, an update caused no sound from the headphone/microphone jack[[3]](https://github.com/schaduwen/gl703/blob/master/README.md#references). This update introduced, changed the ASUS default headphone model to headset mode with microphone.

To revert this change, change the HD-Audio Codec-Specific model by adding the following to /etc/modprobe.d/alsa-base.conf[[4]](https://github.com/schaduwen/gl703/blob/master/README.md#references):

`options snd_hda_intel index=0 model=headset-mode-no-hp-mic`

If alsa-base.conf does not exist, create it. The device needs to be powered off (do not reboot) for the changes to take effect.

Relevant threads: Manjaro[[5]](https://github.com/schaduwen/gl703/blob/master/README.md#references), Arch[[6]](https://github.com/schaduwen/gl703/blob/master/README.md#references).

Undervolt CPU
-------------
The i7-8750H can be undervolted with:

* undervolt (https://github.com/georgewhewell/undervolt) by georgewhewell

As of kernel 5.9 and newer, undervolting this way will result in a warning. To not get the warning add kernel parameter:

`msr.allow_writes=on`

This is an optional configuration and should only be used if you know what you are doing[[7]](https://github.com/schaduwen/gl703/blob/master/README.md#references).

Keyboard Backlight
------------------
Functionality for the Fn keys need to be manually implemented. This means binding the keyboard shortcuts to the desired keyboard backlight program.

Recommended ROG Aura Core alternatives include:

* rogauracore (https://github.com/wroberts/rogauracore) by wroberts
* OpenRGB (https://github.com/CalcProgrammer1/OpenRGB) by CalcProgrammer1

For example, with rogauracore you would bind the rogauracore brightness commands to XF86XK_KbdBrightnessDown and XF86XK_KbdBrightnessUp.

If not using kernel 5.11 and above, the keyboard will need to be 'wakened' on boot to accept rogauracore commands:

```
/usr/local/bin/rogauracore initialize_keyboard
```

Usage
-----
#### Get CPU fan preset

```
cat /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```

fan_boost_mode can use the following values:

* `0`: Balanced (default)
* `1`: Overboost
* `2`: Silent

#### Get CPU fan speed

```
cat /sys/devices/platform/asus-nb-wmi/hwmon/hwmon*/fan1_input
```

Fan speed is measured in rotations/revolutions per minute (RPM). The silent preset has a hard cap of 2900RPM.

#### Configure CPU fan presets (root required)

```
echo 2 > /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```

#### Undervolt CPU (root required)

Undervolting CPU core and CPU cache by -130mV:

```
/usr/local/bin/undervolt --core -130 --cache -130
```

Temperature limits are also supported. Limit CPU temperature to 80 degrees Celsius:

```
/usr/local/bin/undervolt --temp 80
```

#### Configure keyboard backlight brightness

To use 33% brightness:

```
/usr/local/bin/rogauracore brightness 1
```

With rogauracore brightness can take the following values:

* `0`: 0%
* `1`: 33%
* `2`: 66%
* `3`: 100%

#### Configure keyboard backlight color

```
/usr/local/bin/rogauracore green
```

Custom hex colors, different backlight effects, and four zone coloring are available with rogauracore.

References
----------
[1] Line 1725 of https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/drivers/platform/x86/asus-wmi.c?h=v5.6.13, fan_boost_mode_store returns the wrong value. This causes 100% cpu utilization. To use an earlier kernel, the following patch is required https://lkml.org/lkml/2020/3/4/351.

[2] Line 190 of https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/drivers/hid/hid-ids.h?h=v5.10.16, no support for the N-Key keyboard (0b05:1866).

[3] https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/diff/sound/pci/hda/patch_realtek.c?id=v5.7.9&id2=v5.7.10

[4] https://wiki.archlinux.org/title/Advanced_Linux_Sound_Architecture#Correctly_detect_microphone_plugged_in_a_4-pin_3.5mm_.28TRRS.29_jack

[5] https://forum.manjaro.org/t/sound-from-speakers-no-sound-from-3-5mm-jack-audio/5343/27

[6] https://bbs.archlinux.org/viewtopic.php?id=259320

[7] https://www.phoronix.com/scan.php?page=news_item&px=Linux-Filter-Tightening-MSRs

FAQ
---
#### Why openSUSE?

* Rolling release
* Supports snapshots and rollbacks with snapper and btrfs
* Secure and trusted boot support - able to dual boot windows with openSUSE
* Minimal install, the installer is comprehensive enough to choose specific packages
* All of the above with just the installer - no need to get down and dirty like arch for the same result

The GL703GS is still considered new hardware. A new kernel and cutting edge packages are often required for wine/proton so rolling release distributions is the choice for a better gaming experience.

The btrfs filesystem and snapshot support with snapper complements rolling release systems. Updates breaking the system can be simply rollbacked.

Secure and trusted boot support means users do not need to mess with their bios and allows dual booting of windows if required.

With zypper no-recommends flag, openSUSE Tumbleweed is as lightweight as Arch. It does it all and offers all these features in their comprehensive installer.

#### GL703GS quirks for potential owners?

* CPU can get really hot
* GPU Nvidia proprietary drivers cannot change the maximum operating temperature
* Fans are loud, and will ramp up and down on silent mode
* Brightness of the lights on the back of the screen lid scales with screen brightness and cannot be turned off
* Aluminum material of the laptop lid will scratch and catch dust
* Blue led's of the keyboard backlight is very dim
* The BIOS has limited configuration options
* The advertised 'ADVANCED ANTI-DUST COOLING SYSTEM' is a lie

This is just my opinion of the GL703GS after three years of service, expectations can differ from model to model.

Author's Notes
--------------
Feel free to nag me. I'm more than willing to help and answer questions.

Schaduwen


