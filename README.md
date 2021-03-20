ASUS ROG STRIX GL703
====================
Setting up and configuring linux on the Asus ROG Strix GL703. The machine used in this guide is the GL703GS, installed with openSUSE Tumbleweed. Configuration is similar in all GL703 and GL503 models.

Kernel
------
Ensure whatever distribution used has a kernel recent enough to support the features you need:
* Fan control (5.7+)
* Fn keys (5.11+)

Prior to kernel 5.7, configuring fan_boost_mode caused 100% CPU utilization [1].
With kernel 5.11, support for Asus N-Key keyboard is added. Function keys now work [2].

Headphone/mic Jack
------------------
Sometime during kernel 5.7, an update caused the headphone/microphone jack to not work [3].
Add `options snd-hda-intel model=dell-headset-multi` to /etc/modprobe.d/alsa.conf.
If the alsa.conf does not exist, create it.
The device needs to be powered off (not rebooted) for the changes to take effect.

Undervolt
---------
The i7-8750H can be undervolted with the program undervolt by georgewhewell (https://github.com/georgewhewell/undervolt).
As of kernel 5.9+, the kernel parameter `msr.allow_writes=on` needs to be included [4].

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

Without kernel 5.11+, N-Key keyboards needs to be waken on boot to accept rogauracore commands:
```
/usr/local/bin/rogauracore initialize_keyboard
```

Usage
-----
#### Get CPU fan preset:
```
cat /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```
fan_boost_mode can take the following values:
* `0`: Balanced (default).
* `1`: Overboost.
* `2`: Silent.

#### Get CPU fan speed:
```
cat /sys/devices/platform/asus-nb-wmi/hwmon/hwmon*/fan1_input
```
Fan speed is measured in rotations/revolutions per minute (RPM). Silent preset CPU fan speed never exceeds 2900RPM.

#### Configure CPU fan presets (root required):
```
echo 2 > /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```
Using sudo and tee:
```
echo 2 | sudo tee /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```

#### Undervolt CPU (root required):
Undervolting CPU core and CPU cache by -130mV:
```
/usr/local/bin/undervolt --core -130 --cache -130
```
Temperature limits are also supported. Limit CPU temp to 80 degree celsius:
```
/usr/local/bin/undervolt --temp 80
```

#### Configure keyboard backlight brightness:
```
/usr/local/bin/rogauracore brightness 0
```
rogauracore brightness can take the following values:
* `0`: 0%
* `1`: 33%
* `2`: 66%
* `3`: 100%

#### Configure keyboard backlight color:
```
/usr/local/bin/rogauracore green
```
rogauracore accepts custom hex colors, backlight effects, and four zone coloring.

References
----------
[1] In line 1725 of https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/drivers/platform/x86/asus-wmi.c?h=v5.6.13, the fan_boost_mode_store function returns the wrong value. This will cause 100% cpu utilization. This is fixed in kernel 5.7. To use an earlier kernel the following patch is required https://lkml.org/lkml/2020/3/4/351.

[2] Kernel hasn't included support for the N-Key keyboard (0b05:1866, line 190 of https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/drivers/hid/hid-ids.h?h=v5.10.16).

[3] https://forum.manjaro.org/t/sound-from-speakers-no-sound-from-3-5mm-jack-audio/5343/27

[4] https://www.phoronix.com/scan.php?page=news_item&px=Linux-Filter-Tightening-MSRs
