## Set up OpenVPN server on Raspberry Pi 
By QuLk @ 2018.7.10

This article is based on Denis Nuțiu’s [“How to run your own OpenVPN server on a Raspberry Pi”](https://medium.freecodecamp.org/running-your-own-openvpn-server-on-a-raspberry-pi-8b78043ccdea)

This article uses RASPBERRY PI 3 MODEL B, OS version: Raspbian GNU/Linux 9 (stretch).

There are five main sections in this article.
1. [Preparing](https://gist.github.com/QuLk/bafc7f29ed246f120cb2a393a8618aca#1-preparing)
2. [Installing OpenVPN](https://gist.github.com/QuLk/bafc7f29ed246f120cb2a393a8618aca#2-installing-openvpn)
3. [Testing the connection under the same WiFi](https://gist.github.com/QuLk/bafc7f29ed246f120cb2a393a8618aca#3-testing-the-connection-in-the-same-wifi-environment)
4. [Port forwarding](https://gist.github.com/QuLk/bafc7f29ed246f120cb2a393a8618aca#4-port-forwarding)
5. [Set up dynamic DNS](https://gist.github.com/QuLk/bafc7f29ed246f120cb2a393a8618aca#5-set-up-dynamic-dns)


### 1. Preparing

#### 1.1 Buy Raspberry Pi
Buy Raspberry Pi 3 Model B from https://www.raspberrypi.org/products/

#### 1.2 Download NOOBS
Download NOOBS to laptop from https://www.raspberrypi.org/downloads/

#### 1.3 install Raspbian on Raspberry Pi
Follow the [software setup guide](https://www.raspberrypi.org/learning/software-guide/) and [NOOBS setup guide](https://www.raspberrypi.org/help/videos/#noobs-setup) video to install Raspbian on Raspberry Pi 3 Model B via SD card.

Or follow the instruction here: [Install Raspbian on a Raspberry Pi 3](https://gist.github.com/QuLk/3175270304abccba624ba52a575cd05c)

#### 1.4 Make sure the Pi connects to your WIFI

#### 1.5 Set up static IP

I’ve configured my Pi so the Pi always uses a static IP address. \
Here I refer the link to set up Raspberry Pi to have a static IP address: \
https://www.raspberrypi.org/learning/networking-lessons/rpi-static-ip-address/

Use ifconfig command or ip command to check the local IP.  \
For example: 
```bash
$ ip -4 addr show | grep global
    inet 192.168.0.20/24 brd 192.168.0.255 scope global wlan0
    inet 10.8.0.1/24 brd 10.8.0.255 scope global tun0
```

192.168.0.20 is the Pi's IP. 

Use "traceroute google.com" to check the route IP. \
For example:
```bash
$ traceroute google.com
traceroute to google.com (172.217.1.46), 30 hops max, 60 byte packets
 1  192.168.0.1 (192.168.0.1)  1.517 ms  4.608 ms  4.773 ms
 2  96.120.42.9 (96.120.42.9)  13.754 ms  19.660 ms  19.415 ms
```
192.168.0.1 is the route's internal IP.

Edit dhcpcd.conf.
```
$ sudo vi /etc/dhcpcd.conf
```

Add below 4 lines to the bottom of dhcpcd.conf:
```
interface wlan0
static ip_address=<Pi's ip>/24
static routers=<route's ip>
static domain_name_servers=<route's ip> 8.8.8.8
```

For example:
```
interface wlan0
static ip_address=192.168.0.20/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1 8.8.8.8
```

### 2. Installing OpenVPN

The installation will take a long time, depending on the key-size you chose. On my Raspberry Pi 3 Model B, it took about 1-2 hours.

Follow the instruction and install OpenVPN from https://github.com/Angristan/OpenVPN-install 


#### 2.1 get the script and make it executable
```bash
$ wget https://raw.githubusercontent.com/Angristan/OpenVPN-install/master/openvpn-install.sh
$ chmod +x openvpn-install.sh
```

#### 2.2 run it
```
$ sudo ./openvpn-install.sh
```
The first time you run it, you need to answer a few questions to setup your VPN server.

If you don’t know the IP address of your server, just put 0.0.0.0 . It can be changed later in the .ovpn file. \
I use 443 for the port and TCP for the protocol. This can be changed later in the .ovpn file as well.

OpenVPN is auto start on system boot by default.

### 3. Testing the connection in the same WiFi environment

After the script has finished, you’ll get an .ovpn file. 

Make a copy and chmod for the .ovpn file generated during installation. The original one is created by root. You can’t modify it as the edit permission for the file is only for root. But on Pi there is no root. So you need to make a copy to edit it.

The .ovpn file is a text file. You make some change on the .ovpn file, such as 
```
proto tcp-client
remote 192.168.0.20 443
```

Here the 192.168.0.20 is a is a private IP address and is the default for my home broadband routers. It will only work in a Private IPv4 address, i.e. internal network. In this way, you can test VPN connection in your WiFi internal network.

Import the .ovpn file in your favourite VPN client. I use OpenVPN Connect on iOS and there are  two apps for Android: OpenVPN Connect, OpenVPN for Android.

If the connection is successful, congratulations, you now have a VPN server! But, you cannot access it from outside… yet.

Note: So far it will only work if you are connected to the same WiFi as the Pi is.

Note: .ovpn file is a text file. You can edit it with text editor. I use Notepad++ to do it. You can modify the third line anytime to make it consistent with your router port and static ip or dynamic DNS.

You make some change on the .ovpn file, such as:
```
proto tcp-client
remote 192.168.0.20 443
...
Subject: CN=<your vpn client>
```


> I don’t use Obfsproxy, because I don’t find any OpenVPN app for Android and iOS has Obfsproxy option. So I skip the Obfsproxy setting section in Denis Nuțiu’s [“How to run your own OpenVPN server on a Raspberry Pi”](https://medium.freecodecamp.org/running-your-own-openvpn-server-on-a-raspberry-pi-8b78043ccdea).
> 
> But if you want set Obfsproxy, you should notice that in the new created file, obfs4proxy.service, it states "/usr/bin/obfs4proxy". But RaspBerry Pi doesn't have obfs4proxy by default. It could be searched and added via start -> preference -> add/remove software.
> 
> Then "Start the Obfuscation proxy" to use it as stated in Denis Nuțiu’s [“How to run your own OpenVPN server on a Raspberry Pi”](https://medium.freecodecamp.org/running-your-own-openvpn-server-on-a-raspberry-pi-8b78043ccdea).


### 4. Port forwarding

I user NetGear router. It supports port forwarding. 

Refer:  \
http://www.noip.com/support/knowledgebase/setting-port-forwarding-netgear-router-genie-firmware/ \
https://kb.netgear.com/24290/How-do-I-add-a-custom-port-forwarding-service-on-my-Nighthawk-router

About how to setup port forwarding on your router, please search your router help to see if it can and how to setup your router for port forwarding.

Below service name 1 is the one I use for OpenVPN.

| Service Name | External Port | External IP Address | Internal Port | Internal IP Address |
|:------------:|:-------------:|:-------------------:|:-------------:|:-------------------:|
|      1       |   TCP: 443    |         Any         |   TCP: 443    |    192.168.0.20     | 



### 5. Set up dynamic DNS

#### 5.1 Check your router Internet IP

To know your router Internet IP, you can go to website: \
https://whatismyipaddress.com/

You will see you IP address in the webpage. 

#### 5.2 Create a free account at www.noip.com 

NetGear router user refer: https://kb.netgear.com/23859/How-to-create-a-No-IP-Dynamic-DNS-Account

**host name**: is the domain name, such as my.noip.com, something like that. \
**IP/Target**: is your router Internet IP, provided by your Internet provider, such as ComCast, AT&T, etc. Use IPv4 to fill the IP/Target. By default noip.com will fill the current your router Internet IP when you create a new account. But you need to update it every 30 days.

#### 5.3 Set up dynamic DNS in your router

NetGear router users please refer: https://kb.netgear.com/23930/How-to-setup-Dynamic-DNS-on-a-NETGEAR-router-using-www-no-ip-com \
Other router users need to search your router help on this topic.

Example for NetGear router users:
 1. Type http://www.routerlogin.net  or http://www.routerlogin.com in your browser.
 1. A login screen displays.
 1. Enter the router user name and password.
 1. The user name is admin. The default password is password. The user name and password are case-sensitive.
 1. The BASIC Home screen displays.
 1. Click the ADVANCED tab.
 1. Select Advanced Setup > Dynamic DNS
 1. Enable the Use a Dynamic DNS Service tick box, and then choose the 'Service Provider' as www.no-ip.com
 1. Enter the Host name, Username and password for No-IP. Please note your No-IP account must already be created and registered.

**Host name** is the one you created at www.noip.com. \
**User name** is your account name at www.noip.com. \
**Password** is your account password at www.noip.com.
  \
  \
  \
You are all set. Enjoy OpenVPN !


