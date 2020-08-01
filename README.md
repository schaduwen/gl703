# GL703
User guide to the Asus ROG Strix GL703GS. Linux-centric (openSUSE).

## Fan control
To change fan speed presets (requires root access), configure them through sysfs. For example:

```
echo 2 | sudo tee /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```

The fan_boost_mode can take the following values:
- `--0`: Balanced (default).
- `--1`: Overboost.
- `--2`: Silent. Fan speed never exceeds 2900RPM.
