title: Gentoo I :Overview and Quick Installation
author: Cynorr
category: Gentoo & Linux Server
tag: Gentoo Tutorial
date: 2015-07-01
toc: true
---
## Intruction
**Overview**

Gentoo is a fast, modern mate-distrubution with a clean and flexible design, which is build around free software and doesn't hide from its user what is beneath the hood. Portage, the package maintance system which the Gentoo use, is written in Python, meaning the user can view and modify the source code. Gentoo packaging system uses source code and configuring Gentoo happens with regular text file. In other words, openness everwhere. Welcome to the world of choice and performance.

**Quick Installation**

In the process of installation, we are provided with GCC, bash and some specific tools, which makes fresh man daunted. In order to help fresh man build their confidence, this paper provide a quick installing tutorial, following which we can build a light version of Gentoo in 2 hours.

Good Luck!

## Installation Structrue
**Light Version List**

|**------Configure------**|**------Value------**|
|:-:|:-:|
|Boot Media|Minial installation CD|
|Stage Archives|stage3|
|Boot|grub2|
|Partitions| MBR|
|Init|openRC|
|Desktop|xfce|

**Preparation**

* Bootable CD
* U disk with stage3
* Ethernet Network

**Steps**

* 1.File System: After step 1, we will have base linux environment and chroot 
* 2.Configure the System: After step 2, most necessary configuration files will have been configured
* 3.Portage & Kernel: After step 3, the portage is ready to use and the linux kernel is installed
* 4.Bootloader: After step 4, we can boot the gentoo without the Installation CD
* 5.Desktop: After step 5, we will have graphical environment and xfce desktop environment

---

## Step1: File System
**Preparing the disk**

Firstly, we split the full disk into several partitions using MBR partitioning technology.

`fdisk` is a popular and powerful tool to split the disk into partitions. Fire up `fdisk` aginst the disk (in our example, we use /dev/sda)
```
root # fdisk /dev/sda
```
type `p` to display the disks' current partition configuration
```
Command (m for help): p
Disk /dev/sda: 240 heads, 63 sectors, 2184 cylinders
Units = cylinders of 15120 * 512 bytes
  
   Device Boot    Start       End    Blocks   Id  System
/dev/sda1   *         1        14    105808+  83  Linux
/dev/sda2            15        49    264600   82  Linux swap
/dev/sda3            50        70    158760   83  Linux
/dev/sda4            71      2184  15981840    5  Extended
```
First remove all existing partitions from the disk. Type `d` to delete a partition. For instance, to delete an existing /dev/sda1:
```
Command (m for help): d 
Partition number (1-4): 1
```
First create a very small BIOS boot partition. Type `n` to create a new partition, then `p` to select a primary partition, followed `1` to select the first primary partition. Make sure it starts from 2048 and hit `enter`. When prompted for the last sector , type `+128M` to create a partion 128 Mbyte in size:
```
Command (m for help): n
Command action
  e   extended
  p   primary partition (1-4)
p
Partition number (1-4): 1
First sector (64-10486533532, default 64): 2048
Last sector, +sectors +size{M,K,G} (4096-10486533532, default 10486533532): +128M
```
Type `a` to toggle the bootable flag on a partition and select `1`. After pressing `p`, notice that an **\*** is placed in **Boot** column.

Then, create the root partition. To create root partition, type `n` to create a new partition, then `p` to tell `fdisk` to create a primary partition. Then type `2` to create the second primary partition, **/dev/sda2**. When prompted for the first sector, hit `Enter`. When prompted for the last sector, hit `Enter` to create a partition that takes up the rest of the remaining space on the disk. After completing these steps, type `p` should display a partition table that looks similar to this:
```
Command (m for help): p
Disk /dev/sda: 30.0 GB, 30005821440 bytes
240 heads, 63 sectors/track, 3876 cylinders
Units = cylinders of 15120 * 512 = 7741440 bytes
  
   Device Boot    Start       End    Blocks   Id  System
/dev/sda1   *          1       14    105808+  83  Linux
/dev/sda2            15      3876  28690200   83  Linux
```
To save the partition loyout and exit `fdisk`, type `w`
```
Command (m for help): w
```
Finally, make the boot partition (/dev/sda1) in ext2 and root partition (/dev/sda2) in ext4:
```
root # make.ext2 /dev/sda1
root # make.ext4 /dev/sda2
```
**Installing the Stage3**
First mount the disk and U disk with stage3, type `fdisk -l` to check the U disk partition( our example is /dev/sdb1)
```
mount /dev/sda2 /mnt/gentoo

mkdir /mnt/gentoo/boot
mount /dev/sda1 /mnt/gentoo/boot

mkdir /mnt/gentoo/usb
mount /dev/sdb1 /mnt/gentoo/usb
```
Then copy the stage3 file to root directory and unpack in root directory
```
root # cd /mnt/gentoo/
root # cp usb/stage3-*.tar.bz2 ./
root # tar xvjpf starge3-*.tar.bz2
```
**Chroot**
We need to copy the network configuration file and mount some specfic system partition before chrooting.
```
root # cp -L /etc/resolv.conf /mnt/gentoo/etc/
root # mount -t proc proc /mnt/gentoo/proc
root # mount --rbind /sys /mnt/gentoo/sys
root # mount --rbind /dev /mnt/gentoo/dev
```
Then chroot and reload the system environment variables
```
root # chroot /mnt/gentoo /bin/bash
root # source /etc/profile
```

## Step2 Configuring the System
Set the password
```bash
root # passwd
```
To optimize Gentoo, it is possible to set a couple of variables which impact Portage behavior. All those variables can be set as environment variables. To keep the settings, Portage reads in the **/etc/portage/make.conf** file, a configuration file for Portage.

Fire up an editor (without `vim` here, we use `nano`) to alter the optimization variables.
```
root # nano -w /etc/portage/make.conf
```

**Configuring the compile options**

A first setting `-march=` flag, which specifies the name of the target architecture. A common used value is `native` as that tell the compiler to select the target architecture of current system.
```
CFLAGS="-march=native -O2 -pipe"
CXXFLAGS="${CFLAGS}"
```
The `MAKEOPTS` variable defines how many parallel compilations should occur when installing a package. A good choice is the number threads in the system plus one. For instance, the hyperthread i5-3230M with 4 threads( 2 cores), should set `5` parallel compilations.
```
MAKEOPTS="-j5"
```
**Configuring the USE flag**
`USE` is one of the most powerful variables Gentoo provides to its users. Several programmes can be compiled with or without optional support for certain items. Most of `xfce` desktop users will want to set the following:
```
USE="X dbus jpeg lock session startup-notification thunar udev -gnome -kde -minimal -qt4 
```
**Selecting mirrors**
In order to download source code quickly it is recommended to select a fast mirror. we should surf Gentoo mirror list and search for a mirror that is close to the system's physical location.
First back to boot installing CD environment, which provided a nice tool called `mirrorselect`:
```
root # exit
```
then select the fast mirror by `direction key` and `Enter`.
```
root # mirror -i -o >> /mnt/gentoo/etc/portage/make.conf
root # mirror -i -r -o >> /mnt/gentoo/etc/portage/make.conf
```
Final chroot again
```
root # chroot /mnt/gentoo /bin/bash
root # source /etc/profile
```
**Select Profile & Locale**
We select `default/linux/amd64/13.0/desktop` without `gnome` or `kde`, since we will use `xfce` desktop.
```
root # eselect profile list
root # eselect profile set 3
```
It is now time to set the system-wide locale settings. We Strongly suggest to use at least one UTF-8 locale because some applications may require it. Here, we use `en_US.utf-8`
```
root # eselect locale list
root # eselect locale set 239
```
**Configuring the Network**
In this tutorial, we use `dhcp`. We specfic the dhcp settings right now and install `dhcp` after syncing the portage tree.
First check the net port by typing `ifconfig`:
```
root # ifconfig

enp2s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.101  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::fabc:12ff:fe80:ada3  prefixlen 64  scopeid 0x20<link>
        ether f8:bc:12:80:ad:a3  txqueuelen 1000  (Ethernet)
        RX packets 81407  bytes 116649996 (111.2 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 46215  bytes 3370975 (3.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 0  (Local Loopback)
        RX packets 3144  bytes 1874421 (1.7 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 3144  bytes 1874421 (1.7 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
The `enp2s0` is the Ethernet port flag, set the connecting method as `dhcp`
```
root # echo "config_enp2s0=`dhcp`" > /etc/conf.d/net
```
To have the network interfaces activated at boot, they need to be added to the default runlevel.
```
root # cd /etc/init.d/
root # ln -s net.lo net.enp2s0
root # rc-update add net.enp2s0 default
```
**Configuring the fstab**
Under Linux, all partitions used by the system must be listed in **/etc/fstab**, the **/boot** and **/root** partitions must be mounted automatically.
```
nano -w /etc/fstab
```
Below is a example of an **/etc/fstab** file:
```
/dev/sda1   /boot        ext2    defaults,noatime     0 2
/dev/sda2   /            ext4    noatime              0 1
  
/dev/cdrom  /mnt/cdrom   auto    noauto,user          0 0
```

## Step3: Portage & Kernel
**Installing a portage snapshot**
A Portage snapshot is a collection file that inform portage what software title are availabe to use, which profiles the adminintractor can select, etc.
```
root # emerge-webrsync
root # emerge --sync
```
**Configuring the Kernel**
First installing the source:
```
root # emerge gentoo-sources
```
We use `genkernel` to configure the kernel to automatically build the kernel right here, and more information and knowledge about kernel will be following chapter.
```
root # emerge genkernel
root # genkernel all
```

## Step4: Bootloader
The bootloader is responsible for firing up the Linux Kernel upon boot-without it, the system would not know how to proceed when the power button has been pressed.

GRUB2 is provided through the `sys-boot/grub` package:
```
root # emerge grub
```
Next, install the necessary GRUB2 files in **/boot/grub**. Assuming the first disk is **/dev/sda**, the following command will do this :
```
root # grub2-install /dev/sda
```
(Optional) To generate the final GRUB2 configuration, run the `grub2-mkconfig` command:
```
root # grub2-mkconfig -o /boot/grub/grub.cfg
```
Reboot directly, and the installing CD will umount all mounted partitions automatically.
```
root # reboot
```

## Step5: Desktop
At first, we should install X-server. Xorg is the X Window server which allows users to have a graphical environment.
```
root # emerge xorg-drivers
root # emerge xorg-x11
```
`Xfce` is a fast lightweight but full-featured desktop environment.
**xfce**
```
root # emerge xfce4-meta xfce4-notifyd xfce4-panel xfce4-terminal
```
**slim loginer**
`SLiM` is one of the login manager, Simple Login Managert.
```
root # emerge x11-misc/slim
```
**Making slim / xfce autoboot**
```
root # vim /etc/conf.d/xdm
```
Set `DISPLAYMANAGER` as `slim`, and add xdm to autoboot list:
```
root # rc-update add xdm default
```
Install `dbus` and set it autoboot, otherwise slim can't wake xfce up.
```
root # emerge dbus
root # rc-update add dbus default
root # /etc/init.d/dbus start
```
Final make slim launch xfce
```
root # echo XSESSION=\"Xfce4\" > /etc/env.d/90xsession
root # env-update && source /etc/profile
root # reboot
```

## Add non-root user
```
root # useradd -d /home/cyno -m cyno
root # passwd cyno
root # usermod -G wheel,audio cyno
root # gpasswd -a cyno wheel
root # chmod +s /bin/su
```
