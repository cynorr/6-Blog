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
