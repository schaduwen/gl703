# GL703
User guide to the Asus ROG Strix GL703GS. Linux-centric (openSUSE).

## Fan control

#### To configure the CPU fan speed presets (requires root access):

```
echo 2 | sudo tee /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```

/sys/devices/platform/asus-nb-wmi/fan_boost_mode can take the following values:
- `0`: Balanced (default).
- `1`: Overboost.
- `2`: Silent. Fan speed never exceeds 2900RPM.

Prior to kernel 5.7, configuring the fan speed presets would cause 100% CPU utilization. It is recommended to use 5.7 and later on all asus laptops that use the asus-nb-wmi module.

#### To get the CPU fan speed:

```
cat /sys/devices/platform/asus-nb-wmi/hwmon/hwmon*/fan1_input
```

## Keyboard backlight
