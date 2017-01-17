# UbuntuSilentInstallation
Files used to create a silent installation disk

# Caveats
These files are original designed for Ubuntu OEM images and Intel&reg; Joule&trade; boards. To use it on official Ubuntu or other flavors, modifications may be needed.

# Background
This document describes the procedures and settings to create a silent installation disk. The silent installation will perform disk partitioning, file copy, system setup and user setup. The installer will load the pressed file which provides all settings and properties of the system. During the installation, the installer won’t show any interactive dialogs. It will only show the progress of installation.

# Procedures
Hardware and software requirements
* Ubuntu desktop ISO image
* A Ubuntu 16.04+ workstation
* A USB disk (4G min.)

## Create a USB disk from the Ubuntu Desktop image for TuChuck
1. On the Ubuntu workstation, insert a USB disk which is formated to FAT32. The system will automatically mount it at /media/USER/DISKLABEL. USER will be the username on the Ubuntu workstation and DISKLABEL will be the disk name.
2. Install fuseiso.
````
  $ sudo apt install -y fuseiso
````
3. Mount the iso and copy its contents to the USB disk
````
$ mkdir iso
$ fuseiso /path/to/ubuntu.iso iso
$ rsync --delete -avP iso/ /media/USER/DISKLABEL/
$ fusermount -u iso
````
4. Note: the USB disk will only support UEFI boot, but not legacy boot. All contents in the USB disk will be removed after this operation.

## Create the preseed file for silent installation
This preseed file provide the default selections and settings to the installer.
````
$ cp unattended.cfg  /media/USER/DISKLABEL/install/unattended.cfg
````

## Create the manifest-remove file
This file is used to indicate what packages will be removed after installation.
````
$ cp filesystem.manifest-remove /media/USER/DISKLABEL/casper/filesystem.manifest-remove
````

## Change grub.cfg to use the new preseed file
Specify that installer has to load the unattended.cfg.
````
$ chmod u+w /media/USER/DISKLABEL/boot/grub/grub.cfg
$ cp grub.cfg /media/USER/DISKLABEL/boot/grub/grub.cfg
````

## Start installation
1. Unmount and remove the USB disk from the the Ubuntu workstation
2. Connect the USB disk to a TuChuck board
3. The preseed file sets the first eMMC device (/dev/mmcblk0) as the target. Remember to remove the microSD card, or the system will be installed onto the card.
4. Boot from this USB disk
5. The installer will automatically complete all necessary operations
6. The system will reboot several times and then show the desktop environment in the end.
7. The default user is ‘ubuntu’ and the password is ‘ubuntu’.

