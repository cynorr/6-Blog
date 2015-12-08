title: Gentoo Develop Tools -- Android SDK
author: Cyno
category: Gentoo & Linux Server
tag: Network
date: 2015-07-02
toc: true

---
This section, we will install an android emulator on Linux. Android platform are based on two components: JDK and SDK. We assumed that JDK has been installed well.

##### 1.Download the `android-sdk-*` frome [Android offical site](http://developer.android.com/sdk/index.html), here we use the `android-sdk_r24.4.1-linux.tgz` version.
##### 2.Unpack it and you will see what likes following:
```bash
user $ tar -zxvf android-sdk_r24.4.1-linux.tgz
user $ cd android-sdk-linux
user $ ls
drwxr-xr-x  2 cyno cyno 4096 Oct 14 01:57 add-ons
drwxr-xr-x  3 cyno cyno 4096 Dec  8 21:20 build-tools
drwxr-xr-x 25 cyno cyno 4096 Dec  8 21:26 docs
drwxr-xr-x  7 cyno cyno 4096 Dec  8 21:32 platforms
drwxr-xr-x  5 cyno cyno 4096 Dec  8 21:19 platform-tools
-rw-r--r--  1 cyno cyno 1158 Oct 14 01:57 SDK Readme.txt
drwxr-xr-x  2 cyno cyno 4096 Dec  8 21:33 temp
drwxr-xr-x 12 cyno cyno 4096 Oct 14 08:02 tools
```
##### 3.Update and Install the APIs that you need.
* 3.1 Launch android
```bash
user $ cd android-sdk-linux/tools/
user $ ./android
```
then you will see the prompt that checks whether the update is available automatically.
* 3.2 Proxy for Region that can't access Google server.
The update and API is on Google's serve, which cannot be accessed if you are in China or some other region. We can use proxy to deal with this problem.
Select menu `Tools`  ->  `Options`, then you will see another `Settings` windows. Configure the proxy like this:
```txt
HTTP Proxy: Server mirrors.neusoft.edu.cn
Proxy Port: 80
```
And active `Force https://... sources to be fetched using http:`
* 3.2.2(Alternative) Change hosts temporarily
Add the following text to `/etc/hosts`, and delete after you update and install all you need.
```
203.208.46.146 dl.google.com
203.208.46.146 dl-ssl.google.com127.0.0.1 servserv.generals.ea.com
74.125.237.1 dl-ssl.google.com
203.208.46.146 dl.google.com
74.125.237.1 dl-ssl.google.com
```

* 4.Install `Platform` and `Image`
Since the update will run automatically, so we just need to focus on installation of `Platform` and `Image`. The rule is simple, you have to install at least 1 platform and each platform should be with at least 1 image. Following is my choice

```bash
-----------------------------------
[*] Android 5.1.1
     [*] ARM [*] System Image
     [*] Intel x86 Atom System Image
-----------------------------------     
[*] Android 4.4.2
     [*] ARM [*] System Image
     [*] Intel x86 Atom System Image
-----------------------------------       
```
* 5.Launch android AVD and enjoy it
```bash
user $ cd android-sdk-linux/tools/
user $ ./android avd
```


===
### PS1.Make sure the KVM work well which in the Linux kernel
**KVM**
```bash
[*] Virtualization--->
    <*>   Kernel-based Virtual Machine (KVM) support
    <*>     KVM for Intel processors support
    < >     KVM for AMD processors support
    <M>   Host kernel accelerator for virtio net
```
**Others**
```bash
General Setup--->
    [*] Control Group support --->
        [*]   Freezer cgroup subsystem
        [*]   Device controller for cgroups
        [*]   Group CPU scheduler
        [*]   Enable perf_event per-cpu per-container group (cgroup) monitoring
        [*]   Block IO controller
        [*]   Resource counters
        [*]     Memory Resource Controller for Control Groups (NEW)
        [*]       Memory Resource Controller Swap Extension (NEW)
        [*]       Memory Resource Controller Kernel Memory accounting (EXPERIMENTAL) (NEW)
-*- Networking support--->
      Networking Options--->
        <M> 802.1d Ethernet Bridging
        <*> Network priority cgroup
        [*] QoS and/or fair queueing--->
            <M>   Control Group Classifier
        [*] Network packet filtering framework (Netfilter)--->
            <M>   Ethernet Bridge tables (ebtables) support--->
                <M>   ebt: mark target support (NEW)
Device Drivers--->
    Character devices--->
        -*- Unix98 PTY support
        [*]   Support multiple instances of devpts
    [*] Network device support--->
        -*-   Network core driver support
        <M>     Universal TUN/TAP device driver support
Kernel Hacking--->
    [*] Debug Filesystem
```
