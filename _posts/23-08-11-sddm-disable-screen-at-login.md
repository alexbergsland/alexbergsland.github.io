--
title: Disable monitor at login (SDDM)
categories: [linux,sddm]
tags: [sddm,display_manager,login,monitor]
---

# Disable monitor at login when using SDDM
I have dual monitors but SDDM shows the cursor on the right monitor but the left one i the main monitor.<br>
These changes will disable the right monitor at login and then enable it when the desktop environments starts.<br><br>

##

Check `/etc/sddm.conf` for
```bash
[X11]
DisplayCommand=/etc/sddm/Xsetup
```

If that file does not excist make one with
```bash
sddm --example-config > ./sddm.conf
```
Copy the content to `/etc/sddm.conf`<br>
In theory you should be able to
```bash
sddm --example-config > /etc/sddm.conf
```
But I had permission issues with that.

Check which monitors are connected
```bash
xrandr | grep " connected "
DisplayPort-0 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 531mm x 299mm
HDMI-A-0 connected 1920x1080+1920+0 (normal left inverted right x axis y axis) 510mm x 287mm
```
My main monitor is `DisplayPort-0` and the secondary one is `HDMI-A-0`

##

```bash
sudo nano /etc/sddm/Xsetup
```

```shell
#!/usr/bin/sh
# Xsetup - run as root before the login dialog appears
xrandr --output HDMI-A-0 --off
xrandr --output DisplayPort-0 --mode 1920x1080 --pos 0x0 --rotate normal --rate 144

```

Also make a script in you home directory (or where ever)
```bash
#!/usr/bin/sh
xrandr --output DisplayPort-0 --mode 1920x1080 --pos 0x0 --rotate normal --rate 144
xrandr --output HDMI-A-0 --mode 1920x1080 --pos 1920x0 --rotate normal
```
Make it executable and add it as a startup program in your desktop environment.