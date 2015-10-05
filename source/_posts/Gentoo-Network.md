title: Gentoo II NetWork: Ethernet, Wireless, Shadowsocks
author: Cyno
category: Gentoo & Linux server
tag: Network
date: 2015-07-02
toc: true

---
## Introduction
It is easy to config the Network for most desktop with just single Ethernet interface, but not easy for laptop with both Ethernet and Wireless interface. Because Wifi supplicant has some conflict with dhcpcd, we should pay more attention to them.

## Ethernet
If no requirement for static IP address, we prefer DHCP that can obtain it automatically. `dhcpcd` is a popular client that is capable of handing both IPv4 and IPv6 configurations. 
Firstly install the `dhcpcd`
```bash
root # emerge dhcpcd
```
Then configure the net profile, we can check name of Ethernet interface by typing `ifconfig` (in this case, the interface name is enp2s0).
Following is `/etc/conf.d/net`
```
# Ehternet 
config_enp2s0='dhcp'
```
**Important**: Do not add dhcpcd to any runlevel,since it can't work well with apa_supplicant for wireless card. And don't worry that Ethernet interface can't get IP address, it will invoke the specific subservice of dhcpcd.

Finally, add the ethernet to the runlevel
```
root # cd /etc/ini.t
root # ln -s net.lo net.enp2s0
root # rc-update add net.enp2s0 default
```

## Wireless
The WPA supplicant project provides a package that allows user to connet to WPA enabled access points.
```bash
root # emerge wpa_supplicant
```
Force the use of wpa_supplicant for wireless. As Ethernet, we use 'ifconfig' commond to check wireless interface name (in this case, my wireless name is wlp3s0).
Following is `/etc/conf.d/net`
```bash
# Ehternet 
config_enp2s0='dhcp'

# Wireless
modules_wlp3s0='wpa_supplicant'
# The driver of wireless card, -Dnl80211 or -Dwext, my driver is -Dnl80211
wpa_supplicant_wlp3s0='-Dnl80211' 
```
**Important**:Do not add wpa_supplicant to any runlevel, otherwise it can't obtain IP address. It will call the subservice of both wpa_supplicant and dhcpcd for authentication and IP address.

Final add the wireless service to runlevel
```bash
root # cd /etc/init.d/
root # ln -s net.lo net.wlp3s0
root # rc-update add net.wlp3s0 default
```
## GUI
Both Wicd and NetworkManager are greatful fraphical client on desktop environment, and we prefer Networkmanager. Though wicd is powerful, it has not plugin for panel. However, Networkmanager has a panel client `nm-applet` as a button in panel, which looks concise.
```bash
root # emerge networkmanager
root # emerge nm-applet
root # rc-update add NetworkManager default
```

## Shadowsocks
Don't use the shadowsocks-libev in default gentoo portage, which need specify either Ethernet or Wireless manually. The shadowsocks in `pip` is prefered one.
```bash
root # emerge dev-python/pip
root # pip install shadowsocks
```
Then set the shadowsocks configuration `/etc/shadowsocks.json` like this:
```
{
        "server":"128.199.226.255",
        "server_port":8388,
        "local_port":1080,
        "password":"262524",
        "timeout":600,
        "method":"aes-256-cfb"
}
```
Launch shadowsocks 
```bash
root # ssclient -c /etc/shadowsocks.json
```
