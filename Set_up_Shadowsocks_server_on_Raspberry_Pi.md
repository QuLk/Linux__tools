## Set up Shadowsocks server on Raspberry Pi 
By QuLk @ 2018.7.12

Refer:   
https://medium.freecodecamp.org/running-your-own-openvpn-server-on-a-raspberry-pi-8b78043ccdea  \
https://www.reddit.com/r/China/comments/8hp0kr/shadowsocks_server_on_raspberry_pi/  \
https://www.linuxbabe.com/linux-server/setup-your-own-shadowsocks-server-on-debian-ubuntu-centos

This article uses RASPBERRY PI 3 MODEL B, OS version: Raspbian GNU/Linux 9 (stretch).

There are four main sections in this article:
1. [Preparing](https://github.com/QuLk/Linux__tools/blob/master/Set_up_Shadowsocks_server_on_Raspberry_Pi.md#1-preparing)
1. [Installing Shadowsocks](https://github.com/QuLk/Linux__tools/blob/master/Set_up_Shadowsocks_server_on_Raspberry_Pi.md#2-installing-shadowsocks)
1. [Port forwarding](https://github.com/QuLk/Linux__tools/blob/master/Set_up_Shadowsocks_server_on_Raspberry_Pi.md#3-port-forwarding)
1. [Set up dynamic DNS](https://github.com/QuLk/Linux__tools/blob/master/Set_up_Shadowsocks_server_on_Raspberry_Pi.md#4-set-up-dynamic-dns)


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

I’ve configured my Pi so the Pi always uses a static IP address.  \
Here I refer the link to set up Raspberry Pi to have a static IP address:  \
https://www.raspberrypi.org/learning/networking-lessons/rpi-static-ip-address/ 

Use ifconfig command or ip command to check the local IP.  \
For example:  
```bash
$ ip -4 addr show | grep global
    inet 192.168.0.20/24 brd 192.168.0.255 scope global wlan0
    inet 10.8.0.1/24 brd 10.8.0.255 scope global tun0
```
192.168.0.20 is the Pi's IP.   

Use "traceroute google.com" to check the route IP.  \
For example:  
```bash
$ traceroute google.com
traceroute to google.com (172.217.1.46), 30 hops max, 60 byte packets
 1  192.168.0.1 (192.168.0.1)  1.517 ms  4.608 ms  4.773 ms
 2  96.120.42.9 (96.120.42.9)  13.754 ms  19.660 ms  19.415 ms
```
192.168.0.1 is the route's internal IP.

Edit dhcpcd.conf:
```bash
$ sudo vi /etc/dhcpcd.conf
```

Add below 4 lines to the bottom of dhcpcd.conf:
```
interface wlan0
static ip_address=<Pi's ip>/24
static routers=<route's ip>
static domain_name_servers=<route's ip> 8.8.8.8
```
  \
For example:
```
interface wlan0
static ip_address=192.168.0.20/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1 8.8.8.8
```

### 2. Installing Shadowsocks

#### 2.1 Update local package index
```bash
$ sudo apt-get update
```
#### 2.2 Install shadowsocks
Search shadowsocks via _**start -> preference -> add/remove software**_ and install it. \
I select shadowsocks-2.9.0-2 to install.

#### 2.3 Edit config.json

There is a template in the file config.json. You need to modify it according to your server IP, server port, password for Shadowsocks.

Example:
```bash
$ sudo vi /etc/shadowsocks/config.json
```

Modify the template as below lines:
```
{
    "server":"192.168.0.20",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"abcdefg",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false,
    "workers": 1,
    "prefer_ipv6": false
}
```

#### 2.4 Start the Shadowsocks server

To start it:
```bash
$ sudo ssserver -c /etc/shadowsocks/config.json -d start
```

To stop it:
```bash
$ sudo ssserver -d stop
```

To restart it:
```bash
$ sudo ssserver -c /etc/shadowsocks/config.json -d restart
```

To see the activity of the Shadowsocks server:
```bash
$ tail /var/log/shadowsocks.log
```

#### 2.5 Auto start on system boot

Edit .profile:
```bash
$vi ~/.profile
```

Add the start server command to the bottom of .profile:
```
sudo ssserver -c /etc/shadowsocks/config.json -d start
```

### 3. Port forwarding

I use NetGear router. It supports port forwarding. 

Refer:   \
http://www.noip.com/support/knowledgebase/setting-port-forwarding-netgear-router-genie-firmware/  \
https://kb.netgear.com/24290/How-do-I-add-a-custom-port-forwarding-service-on-my-Nighthawk-router 

About how to setup port forwarding on your router, please search your router help to see if it can and how to setup your router for port forwarding. 

Below service name 2 is the one I use for Shadowsocks on my NetGear router.

| Service Name | External Port | External IP Address | Internal Port | Internal IP Address |
|:------------:|:-------------:|:-------------------:|:-------------:|:-------------------:|
|      2       |   TCP: 8388   |        Any          |   TCP: 8388   |    192.168.0.20     | 


### 4. Set up dynamic DNS

#### 4.1 Check your router Internet IP

To know your router Internet IP, you can go to website:  \
https://whatismyipaddress.com/

You will see you IP address in the webpage. 

#### 4.2 Create a free account at www.noip.com 

NetGear router users refer: https://kb.netgear.com/23859/How-to-create-a-No-IP-Dynamic-DNS-Account  

**host name**: is the domain name, such as my.noip.com, something like that.  \
**IP/Target**: is your router Internet IP, provided by your Internet provider, such as ComCast, AT&T, etc. Use IPv4 to fill the IP/Target. By default noip.com will fill the current your router Internet IP when you create a new account. But you need to update it every 30 days.

#### 4.3 Set up dynamic DNS in your router

NetGear router users refer: https://kb.netgear.com/23930/How-to-setup-Dynamic-DNS-on-a-NETGEAR-router-using-www-no-ip-com  \
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

**Host name** is the one you created at www.noip.com.  \
**User name** is your account name at www.noip.com.  \
**Password** is your account password at www.noip.com. 
  \
  \
  \
You are all set. Enjoy Shadowsocks !

**Note:**  \
Somehow after restart Raspberry Pi, the port 8388 is not released based on tail /var/log/shadowsocks.log. It says:
```
socket.error: [errno 98] Address already in use.
```
And Shadowsocks seems not started (not sure. Someone says cannot connect to shadowsocks server in China, but the other can in USA). In this case, need to release the port and start Shadowsocks server manually:
```
sudo fuser 8388/tcp
sudo netstat -anp | grep 8388
sudo fuser -k 8388/tcp
sudo ssserver -c /etc/shadowsocks/config.json -d start
```

Using "sudo fuser 8388/tcp", to see if 8388 is occupied by any PID. Or, use "sudo netstat -anp | grep 8388" to see if 8388 is occupied by any PID and which program occupies it.  \
Using "sudo fuser -k 8388/tcp", to kill any PID which occupies the port 8388.
