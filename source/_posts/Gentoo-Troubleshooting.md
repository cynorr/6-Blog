### 1. emerge can't work :Checking whether the C compiler works... no
```bash
root # gcc-config -l
root # gcc-config 1
```
### 2. How to screenshoot on xfce4
```bash
root # emerge xfce4-screenshooter
```
### 3. Dual boot gentoo and windows
```bash
root # emerge os-prober
root # grub2-install /dev/sda
root # grub2-mkconfig -o /boot/grub/grub.cfg
```

### 4. Laptop : Powermanager, Volumn, Brightness
```bash
root # emerge laptop-mode-tools
root # rc-update add laptop_mode default
root # emerge xfce4-power-manager
```
### 5. error: generated/bounds.h when emerging VirtualBox
```bash
root # cd /usr/src/linux
root # make modules_prepare
```
### 6. Necessary applications list
```
euse -E vlc networkmanager alsa
emerge --autounmask-write \
x11-misc/slim xf86-video-intel xf86-video-vesa xorg-drivers xorg-x11 \
greybird xfce4-meta xfce4-notifyd xfce4-panel xfce4-terminal xfce4-screenshooter xfce4-battery-plugin xfce4-power-manager xfce4-mixer xfce4-appfinder xfce4-archive-plugin xfce-extra/tumbler thunar-volman\
thunderbird firefox google-chrome dhcp net-misc/networkmanager nm-applet \
smplayer vlc alsa-utils rhythmbox adobe-flash \
virtualbox virtualbox-guest-additions wine \
wps-office media-fonts/symbola libreoffice gedit fcitx wqy-zenhei sunpinyin \
compiz fusion-icon emerald compiz-plugins-main compiz-plugins-extra \
htop sudo screenfetch pciutils gentoolkit laptop-mode-tools bumblebee 
```
### 7. Chinese fonts
First, configure the /etc/locale.gen
```
en_US ISO-8859-1
en_US.UTF-8 UTF-8
zh_CN GB18030
zh_CN.GBK GBK
zh_CN.GB2312 GB2312
zh_CN.UTF-8 UTF-8
```
Then, install the Chinese fonts and refresh profile
```bash
root # emerge wqy-zenhei #正黑
root # env-update && source /etc/profile
```
### 8. Inatll OS X theme
First download the Gnome Cupertino file, which is available at gnome-look.org. Then unpackage it and copy it to /usr/share/themes/ rather than user's home.
After installation, open apperence and activate the Gnome-Cupertino theme. But after you activate the theme, the window frame remains the same. To fix this we need `dconf` - the Configurator system files tools.
```bash
root # emerge dconf dconf-editor
```
After installation, launch the `dconf-editor`, go to the following address: **org/gnome/desktop/wm/preferences** and in the line theme, change the name of the theme on `Gnome-Cupertino`. 
Now the window frames should vary according to the installed and activated the theme **Gnome Cupertino**. If not, open the apperance and reactivate **Gnome Cupertino**.

### 9.Session and StartUp for **Compiz** can't work
Admittedly, **compiz** should be started after X and xfce4 completely initialization. We can delay the startup of compiz for 1s by using `sleep 1`, otherwise compiz wouldn't work.
Following is the script for compiz startup placed at ~/.start-compiz.sh, which needs to be add to `Session and Startup` list.
```bash
#!/bin/bash
sleep 1
compiz --replace --sm-disable --ignore-desktop-hints ccp &
emerald --replace &
```

### 10. alsa cannot find control device
We need to set codec for alsa
```
Device Drivers  ---> 
  <*> Sound card support  --->
    <*>   Advanced Linux Sound Architecture  --->  
      HD-Audio  --->
        <*> HD Audio PCI                                                             │ │  
  │ │       (64) Pre-allocated buffer size for HD-audio driver                           │ │  
  │ │       [*] Build hwdep interface for HD-audio driver                                │ │  
  │ │       -*- Allow dynamic codec reconfiguration                                      │ │  
  │ │       [*] Support digital beep via input layer                                     │ │  
  │ │       (1)   Digital beep registration mode (0=off, 1=on)                           │ │  
  │ │       [*] Support jack plugging notification via input layer                       │ │  
  │ │       [*] Support initialization patch loading for HD-audio                        │ │  
  │ │       <*> Build Realtek HD-audio codec support                                     │ │  
  │ │       <*> Build Analog Device HD-audio codec support                               │ │  
  │ │       <*> Build IDT/Sigmatel HD-audio codec support                                │ │  
  │ │       <*> Build VIA HD-audio codec support                                         │ │  
  │ │       <*> Build HDMI/DisplayPort HD-audio codec support                            │ │  
  │ │       <*> Build Cirrus Logic codec support                                         │ │  
  │ │       <*> Build Conexant HD-audio codec support                                    │ │  
  │ │       <*> Build Creative CA0110-IBG codec support                                  │ │  
  │ │       <*> Build Creative CA0132 codec support                                      │ │  
  │ │       [*]   Support new DSP code for CA0132 codec                                  │ │  
  │ │       <*> Build C-Media HD-audio codec support                                     │ │  
  │ │       <*> Build Silicon Labs 3054 HD-modem codec support                           │ │  
  │ │       -*- Enable generic HD-audio codec parser                                     │ │  
  │ │       (0) Default time-out for HD-audio power-save mode  
```
