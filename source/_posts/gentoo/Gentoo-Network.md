title: Gentoo II NetWork -- Ethernet, Wireless, Shadowsocks
author: Cyno
category: Gentoo & Linux Server
tag: Network
date: 2015-07-02
toc: true

---
## Introduction
It is easy to config the Network for most desktop with just single Ethernet interface, but not easy for laptop with both Ethernet and Wireless interface. Because Wifi supplicant has some conflict with dhcpcd, we should pay more attention to them.

## Ethernet && Wireless
If no requirement for static IP address, we prefer DHCP that can obtain it automatically. `dhcp` is a popular client that is capable of handing both IPv4 and IPv6 configurations.
Firstly install the `dhcp`, rather than 'dhcpcd', which has conflits with `NetWorkManager`.
```bash
root # emerge dhcp
```
**Important**: Don't add anything to `/etc/conf.d/net`, and 'NetworkManager` will set `dhcp` default;

Then, set the symbol of ethernet and wireless.
**This is the necessary step.** Without symbol, the `NetworkManager` would fail to start.
```
root # cd /etc/ini.t
root # ln -s net.lo net.enp2s0
root # ln -s net.lo net.wlp3s0
```
**Important**: Don't add ethernet and wireless to any runlevel, `NetworkManager` will call them automatically;

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
