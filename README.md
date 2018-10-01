## Android 8 for Raspberry pi 3
Download and build Android 8 for Rasberry pi.

<img src="https://cdn.instructables.com/F3K/S4I5/ITR1F5G6/F3KS4I5ITR1F5G6.LARGE.jpg" />


## Requirements
for start , you have to install packages that we need to build and download android source. <br />
for Ubunut 16.04 / 18.04 : 
```
 $ sudo apt-get update 
 $ sudo apt-get install openjdk-8-jdk git gcc-arm-linux-gnueabihf 
```

## Downloading Source code
use these commans for getting android source code : 
```
 $ repo init -u https://github.com/NimaMX/platform_manifest -b Oreo
 $ repo sync
```

## Building Linux Kernel 
for builing android os , first you have to build kernel image and dtbs.
use these commands for builing linux kernel :
* for better performance and quick build , use make with -j [system thread count].
```
 $ cd kernel/rpi
 $ ARCH=arm scripts/kconfig/merge_config.sh arch/arm/configs/bcm2709_defconfig android/configs/android-base.cfg android/configs/android-recommended.cfg 
 $ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- zImage dtbs 
```

## Buldling Android OS
after downloading and building kernel , now your are ready to build android system.img.
for building , use these commands : 
* for better performance and quick build , use make with -j [system thread count].
* According to your computer , builidng android will take some time , so please be patient.
```
 $ source build/envsetup.sh
 $ lunch rpi3-eng
 $ make ramdisk systemimage
```

## Setup SD Card 
after android buliding is finished , now we setup sdcard for raspberry pi.
Partitions of the card should be set-up like following (lable - size - format - flag) :
* partition 1 : boot -  512M - Fat32 - Boot flag enable 
* partition 2 : system - 1000M - Ext4
* partition 3 : cache - 512M - Ext4
* partition 4 : data - 1000M (can be bigger) - Ext4

## Preparing system partition 
for preparing system , we should write system.img to system partion. 
use these commands to do :
````
 $ cd out/target/product/rpi3 
 $ sudo dd if=system.img of=/dev/{system partirion -> link /dev/mmcblk0p2} bs=1M
````

## Preparing boot partition
for preparing boot , you should copy kernel zImage , bootloader , and dtbs.
replace boot with your sdcard boot partition mount point. (like /media/nima/boot)
```
 $ mkdir boot/overlays
 $ cp kernel/rpi/arch/arm/boot/zImage boot:/
 $ cp kernel/rpi/arch/arm/boot/dts/bcm2710-rpi-3-b.dtb boot:/
 $ cp kernel/rpi/arch/arm/boot/dts/overlays/vc4-kms-v3d.dtbo boot/overlays
 $ cp out/target/product/rpi3/ramdisk.img boot:/
```

## Author
Nima Mohammadi 
