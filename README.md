RoboPeak Mini USB Display
====================================

Drivers and Tools for RoboPeak Mini USB Display Project

RoboPeak no longer maintain this project, the only useful links listed below.
[Original Source Project](https://github.com/robopeak/rpusbdisp)
[DFRobot Wiki Page](https://wiki.dfrobot.com/USB_display_module_SKU_DFR0275)
[DFR0275 User Manual PDF](http://www.robopeak.com/data/doc/rpusbdisp/RPUD02-rpusbdisp_usermanual-enUS.1.1.pdf)
[DFR0275 Datasheet PDF](http://www.robopeak.com/data/doc/rpusbdisp/RPUD01-rpusbdisp_datasheet-enUS.1.2.pdf)
[DFR0275 USB Interface Protocol Spec](http://www.robopeak.com/data/doc/rpusbdisp/RPUD03-rpusbdisp_interface_protocol-enUS.1.0.pdf)


How to Build the Linux Kernel Driver
====================================
Here we only provide you the basic building process. Please refer to the related documents for details.


I. Prerequisite
-----------------
Use Debian/Ubuntu/Raspian Linux as example, install build essential packages and the current kernel header:

```shell
$ sudo apt-get update
$ sudo apt-get install git gcc binutils make
$ sudo apt-get install raspberrypi-kernel-headers
```
See also [Raspbian Kernel Headers](https://www.raspberrypi.org/documentation/linux/kernel/headers.md).

II. Native Build - build the kernel driver for the current machine
-----------------------------------------------------------------
Git clone this repo, and
```shell
$ cd drivers/linux-driver
$ make
```
If everything going okay, you should have a built file called rp_usbdisplay.ko.

III. Install Kernel Module
--------------------------
Install the kernel module and load it:
```shell
$ sudo install rp_usbdisplay.ko "/lib/modules/`uname -r`/kernel/rp_usbdisplay.ko"
$ sudo depmod -a
```

Make sure the kernel module is working properly:
```shell
$ sudo modprobe rp_usbdisplay
```
No output means the module is working.

IV. Auto Loading Kernel Module
------------------------------
```shell
$ sudo vi /etc/modules
```
Add `rp_usbdisplay` at the bottom. Save and reboot. 

V. Verify
---------
First we need to find out which framebuffer device the USB display is allocated to.
```shell
cat /proc/fb | grep rpusbdisp-fb
```

This should have some output like:
```
1	rpusbdisp-fb
```
The number 1 means /dev/fb1 device.

Then let's output some random dots to the USB display:
```shell
$ cat /dev/urandom > /dev/fb1
```
You should notice the screen is showing randomized color dots.


X Window
========
In order for X Window to use the USB display, we need to change or create configuration file for X Window.

Use original framebuffer device along with USB display:
```shell
$ sudo cp ./x_conf/10-dualdisp.conf /usr/share/X11/xorg.conf.d/
```

Or use USB display as a single display, be warned the resolution is extremely limited:
```shell
$ sudo cp ./conf/10-singledisp.conf /usr/share/X11/xorg.conf.d/
```

Then restart X server or reboot.

