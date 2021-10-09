Preface
-------
Set up and configure linux for the Asus ROG Strix GL703GS. Configuration is applicable to GL703 and GL503 models.

Kernel
------
Ensure the distribution's kernel supports the features you want:
* Fan control (5.7+)
* Fn keys (5.11+)

Kernel 5.7 fixed fan_boost_mode causing 100% CPU utilization when changing fan speed [[1]](https://github.com/schaduwen/gl703/blob/master/README.md#references).
Kernel 5.11 added support for the keyboard Fn keys [[2]](https://github.com/schaduwen/gl703/blob/master/README.md#references).

Headphone/mic Jack
------------------
Currently broken on kernel 5.13.12. Investigation pending.

Sometime during kernel 5.7, an update caused the headphone/microphone jack to not work [[3]](https://github.com/schaduwen/gl703/blob/master/README.md#references).
Add `options snd-hda-intel model=dell-headset-multi` to /etc/modprobe.d/alsa.conf.
If the alsa.conf does not exist, create it.
The device needs to be powered off (not rebooted) for the changes to take effect.

Undervolt
---------
The i7-8750H can be undervolted with the program undervolt by georgewhewell (https://github.com/georgewhewell/undervolt).
As of kernel 5.9+, the kernel parameter `msr.allow_writes=on` needs to be included [[4]](https://github.com/schaduwen/gl703/blob/master/README.md#references).

Keyboard Backlight
------------------
Functionality for the Fn keys need to be manually implemented.
Recommended ROG Aura Core alternatives:
* rogauracore by wroberts (https://github.com/wroberts/rogauracore)
* OpenRGB by CalcProgrammer1 (https://github.com/CalcProgrammer1/OpenRGB)

For example, with rogauracore:
1. Install dependencies (libusb-1_0-devel on openSUSE).
2. Configure and make install program.
3. Bind rogauracore brightness commands to XF86XK_KbdBrightnessDown and XF86XK_KbdBrightnessUp using your preferred method.

Without kernel 5.11+, N-Key keyboards need to be wakened on boot to accept rogauracore commands:
```
/usr/local/bin/rogauracore initialize_keyboard
```

Usage
-----
#### Get CPU fan preset
```
cat /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```
fan_boost_mode can take the following values:
* `0`: Balanced (default).
* `1`: Overboost.
* `2`: Silent.

#### Get CPU fan speed
```
cat /sys/devices/platform/asus-nb-wmi/hwmon/hwmon*/fan1_input
```
Fan speed is measured in rotations/revolutions per minute (RPM). Silent preset CPU fan speed never exceeds 2900RPM.

#### Configure CPU fan presets (root required)
```
echo 2 > /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```
Using sudo and tee:
```
echo 2 | sudo tee /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```

#### Undervolt CPU (root required)
Undervolting CPU core and CPU cache by -130mV:
```
/usr/local/bin/undervolt --core -130 --cache -130
```
Temperature limits are also supported. Limit CPU temp to 80 degree Celsius:
```
/usr/local/bin/undervolt --temp 80
```

#### Configure keyboard backlight brightness
```
/usr/local/bin/rogauracore brightness 0
```
rogauracore brightness can take the following values:
* `0`: 0%
* `1`: 33%
* `2`: 66%
* `3`: 100%

#### Configure keyboard backlight color
```
/usr/local/bin/rogauracore green
```
rogauracore accepts custom hex colors, backlight effects, and four zone coloring.

References
----------
[1] Line 1725 of https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/drivers/platform/x86/asus-wmi.c?h=v5.6.13, fan_boost_mode_store returns the wrong value. This causes 100% cpu utilization. To use an earlier kernel, the following patch is required https://lkml.org/lkml/2020/3/4/351.

[2] Line 190 of https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/drivers/hid/hid-ids.h?h=v5.10.16, no support for the N-Key keyboard (0b05:1866).

[3] https://forum.manjaro.org/t/sound-from-speakers-no-sound-from-3-5mm-jack-audio/5343/27

[4] https://www.phoronix.com/scan.php?page=news_item&px=Linux-Filter-Tightening-MSRs

FAQ
---
#### Why openSUSE?
* Rolling release.
* Supports snapshots and rollbacks.
* Secure and trusted boot support.
* Minimal install (<300 packages with text mode install).
* All of the above out of the box without messing with the system.

The GL703GS is a gaming machine. Cutting edge packages are often required for the best performance in wine/proton (eg. glibc) and so rolling distributions often provide better gaming experiences. The btrfs filesystem and snapshot support complements rolling release systems. Simply rollback if anything breaks. Secure and trusted boot support means users do not need to fiddle with their bios settings and allows their computer to dual boot windows. With the no-recommends flag, openSUSE Tumbleweed is as light as Arch. openSUSE caters to the majority in that it offers all these features through their comprehensive installer. It is very hard to mess up the installation process.

#### Is the GL703GS a good laptop?
Good:
* screen (fast, non-existent backlight bleed)
* gpu (crushes games at 1080p)

Meh:
* cpu (hot)
* gpu (proprietary drivers - cannot configure)
* fan (loud, constantly spins up and down on silent mode)
* lid (cannot disable outer lid led - scales with screen brightness)
* material (opening it up will wear down the plastic, aluminum lid catch scratches)

Trash:
* keyboard backlight led (blue is very dim)
* BIOS (limited configuration options)
* dust (advertised 'ADVANCED ANTI-DUST COOLING SYSTEM' is a lie)

This is just an opinion of the GL703GS after almost three years of service, and can differ from model to model.

Author's Notes
--------------
Feel free to nag me. I'm more than willing to help and answer questions.

Schaduwen -22 March 2021


