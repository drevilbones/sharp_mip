# Sharp Memory LCD with Adafruit Breakout Kernel Module

This is a linux kernel module for Raspberry Pi to drive Sharp Memory-in-Pixel displays, specifically ones that are equipped with the 
[Adafruit breakout board](https://www.adafruit.com/product/4694), in order to use it as a linux framebuffer console monitor. There are other forks of this driver that have more work put into them, but they seem to be built for other hardware, and I had a hard time getting them to work with a Raspberry Pi Zero W and the aforementioned Adafruit Breakout. Thus, I created this repo. I made one small tweak, and it's working for me, ymmv.

I plan on slowly hacking away at this with the intent of it always being able to run on the hardware specified as a simple linux framebuffer console monitor.

Original README below until I can get this cleaned up more.

⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️⬇️

# Sharp Memory LCD Kernel Driver

**Note**: I did not write this driver. I only modified it to clean up compiler warnings/errors. The original can be found here:
(http://www.librecalc.com/en/wp-content/uploads/sites/4/2014/10/sharp.c)

More information can be found here:
(http://www.librecalc.com/en/downloads/)

This driver is for the LS027B7DH01. It *should* work with other Sharp Mem LCD displays by modifying all 400/240 references with the correct dimensions for your screen.

## Hookup Guide
Connect the following pins:

Display | RasPi
------- | ---------
VIN     | 3.3V      
3V3     | N/C       
GND     | GND       
SCLK    | 11 (SCLK) 
MOSI    | 10 (MOSI) 
CS      | 23        
EXTMD   | 3.3V      
DISP    | 24        
EXTIN   | 25        

## Compile/Install the driver
Verify that you have the linux kernel headers for your platform. For the RasPi these can be obtained by:
```
sudo apt-get install raspberrypi-kernel-headers
```
or more generally:
```
sudo apt-get install linux-headers-$(uname -r)
```

To compile the driver, run:
```
make
```

To install the driver, run:
```
sudo make modules_install
```

If you want the module to load at boot you'll need to add it to the /etc/modules file, like:
```
...
# This file contains...
# at boot time...
sharp
```

## Compile/Install the Device Tree Overlay
The included sharp.dts file is for the Raspberry Pi Zero W. To compile it, run:
```
dtc -@ -I dts -O dtb -o sharp.dtbo sharp.dts
```

To load it at runtime, copy it to /boot/overlays:
```
sudo cp sharp.dtbo /boot/overlays
```

And then add the following line to /boot/config.txt:
```
dtoverlay=sharp
```

## Console on Display
If you want the boot console to show up on the display, you'll need to append `fbcon=map:10` to /boot/cmdline.txt after *rootwait*, like:
```
... rootwait ... fbcon=map:10
```

To make sure the console fits on screen, uncomment the following lines in /boot/config.txt and set the resolution appropriately:
```
framebuffer_width=400
framebuffer_height=240
```
