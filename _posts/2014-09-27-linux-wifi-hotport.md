---
layout: post
title: linux 下开启wifi热点
category: linux
---
  
Linux 环境下开启wifi热点
====

-  hostap
-  dhcp
-  bash

Hostapd
----

>    hostapd is a user space daemon for access point and authentication servers.

hostapd是AP和认证服务器的守护进程，使用hostapd可以无网卡调整为maste模式，从模拟一个路由的功能，也就是软AP(soft AP)。

简单来讲，hostapd可以通过配置创建一个软AP，下面具体介绍一下配置过程。

## 环境要求

-网卡支持master模式
-linux(debian, ubuntu, archlinx)
-Linux mac80211 驱动

## 安装 HOSTAPD

```sh
$: sudo apt-get install hostapd
```
或者[下载]()安装。

##配置 HOSTAPD

在 * /etc/hostapd/hostapd.conf * 文件内写入如下内容

```sh
#sets the wifi interface to use, is wlan0 in most cases
interface=wlan0
#driver to use, nl80211 works in most cases
driver=nl80211
#sets the ssid of the virtual wifi access point
ssid=ff
#sets the mode of wifi, depends upon the devices you will be using. It can be a,b,g,n. Setting to g ensures backward compatiblity.
hw_mode=g
#sets the channel for your wifi
channel=6
#macaddr_acl sets options for mac address filtering. 0 means “accept unless in deny list”
macaddr_acl=0
#setting ignore_broadcast_ssid to 1 will disable the broadcasting of ssid
ignore_broadcast_ssid=0
#Sets authentication algorithm
#1 - only open system authentication
#2 - both open system authentication and shared key authentication
auth_algs=1
#####Sets WPA and WPA2 authentication#####
#wpa option sets which wpa implementation to use
#1 - wpa only
#2 - wpa2 only
#3 - both
wpa=3
#sets wpa passphrase required by the clients to authenticate themselves on the network
wpa_passphrase=KeePGuessinG
#sets wpa key management
wpa_key_mgmt=WPA-PSK
#sets encryption used by WPA
wpa_pairwise=TKIP
#sets encryption used by WPA2
rsn_pairwise=CCMP
```

执行命令

```sh
$:sudo hostapd /etc/hostapd/hostapd.conf
```

现在hostapd的配置已经完成，手机已经可以搜索到一个名为ff的wifi。但是还连不上这个wifi，因为现在还没有配置dhcp服务器。

##DHCP

现在HOSTAPD已经可以正确运行了，只需要配置好DHCP服务器就可以用移动设备接入wifi上网了。DHCP用来给连如的移动设备分配ip地址，首先安装DHCP服务器。

```sh
$:sudo apt-get install isc-dhcp-server
```

编辑* /etc/dhcp/dhcpd.conf *文件，内容如下：

```sh
ddns-update-style none;
ignore client-updates;
authoritative;
option local-wpad code 252 = text;
 
 subnet
 10.0.0.0 netmask 255.255.255.0 {
 # --- default gateway
 option routers
 10.0.0.1;
 # --- Netmask
 option subnet-mask
 255.255.255.0;
 # --- Broadcast Address
 option broadcast-address
 10.0.0.255;
 # --- Domain name servers, tells the clients which DNS servers to use.
 option domain-name-servers
 10.0.0.1, 8.8.8.8, 8.8.4.4;
 option time-offset
 0;
 range 10.0.0.3 10.0.0.13;
 default-lease-time 1209600;
 max-lease-time 1814400;
 }
```

## BASH脚本

配置一个用来上网的简单bash脚本就可以了。假设命名为wifi.sh，写入如下内容。

```sh
#!/bin/bash
#Initial wifi interface configuration
ifconfig $1 up 10.0.0.1 netmask 255.255.255.0
sleep 2
###########Start DHCP, comment out / add relevant section##########
#Thanks to Panji
#Doesn’t try to run dhcpd when already running
if [ “$(ps -e | grep dhcpd)” == “” ]; then
    dhcpd $1 &
fi
###########
#Enable NAT
iptables --flush
iptables --table nat --flush
iptables --delete-chain
iptables --table nat --delete-chain
iptables --table nat --append POSTROUTING --out-interface $2 -j MASQUERADE
iptables --append FORWARD --in-interface $1 -j ACCEPT

#Thanks to lorenzo
#Uncomment the line below if facing problems while sharing PPPoE, see lorenzo’s comment for more details
#iptables -I FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu

sysctl -w net.ipv4.ip_forward=1
#start hostapd
hostapd /etc/hostapd/hostapd.conf
```

 使用格式为

> *./wifi.sh wifi_card_interface interface_with_internet*

查看自己电脑的无线网卡和有线网卡的名字，比如我的是** wlan0 **, ** eth0 **。

```sh
$:ifconfig -a
```

执行

```sh
chmod a+x wifi.sh
sudo ./wifi.sh wlan0 eth0
```

参考：·[The Linux Way to create Virtual Wifi Access Point](http://nims11.wordpress.com/2012/04/27/hostapd-the-linux-way-to-create-virtual-wifi-access-point/)
