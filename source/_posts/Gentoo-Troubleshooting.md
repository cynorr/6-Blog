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
## 6. Necessary applications list
```
emerge --autounmask-write xorg-drivers xorg-x11 xfce4-meta xfce4-notifyd xfce4-panel xfce4-terminal x11-misc/slim xf86-video-intel xf86-video-vesa firefox libreoffice google-chrome shadowsocks-libev amule net-p2p/mldonkey htop fcitx smplayer vlc gedit alsa-utils rhythmbox adobe-flash vmware-player vmware-tools vmwre-modules xfce4-screenshooter
```
