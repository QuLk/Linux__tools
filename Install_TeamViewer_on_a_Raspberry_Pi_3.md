### Install TeamViewer on a Raspberry Pi 3

By QuLk @ 2017.11.11 \
updated @ 2018.7.14
updated @ 2019.1.15

This article uses RASPBERRY PI 3 MODEL B+, OS version: Raspbian GNU/Linux 9 (stretch).

It needs 2 steps:

1. Downlaod the [TeamViewer Host for Raspberry Pi](https://www.teamviewer.com/en/download/linux/). \
Direct link: https://download.teamviewer.com/download/linux/teamviewer-host_armhf.deb

1. Update packages to latest version.
```bash
sudo apt-get update&& sudo apt-get -y dist-upgrade&&sudo apt-get update
```
If you are in China, please refer: https://blog.csdn.net/liudijiang/article/details/80699975

When install xrdp, it will say "connect to mirrors.opencas.cn fail". You need to replace the original package source.

1) Modify /etc/apt/sources.list
```bash
sudo vi /etc/apt/sources.list
```
  1#deb http://raspbian.raspberrypi.org/raspbian/ stretch main contrib non-free rpi    <<---comment out this line   \
  2 # Uncomment line below then 'apt-get update' to enable 'apt-get source'   \
  3 #deb-src http://raspbian.raspberrypi.org/raspbian/ stretch main contrib non-free rpi   \
  4 #replace as http://mirrors.shu.edu.cn/raspbian/raspbian/    \
  5 deb http://mirrors.shu.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi    #<---add    \

2） Update packages 
```bash
sudo apt-get update&& sudo apt-get -y dist-upgrade&&sudo apt-get update
```

Then install xrdp when installing TeamViewer, the download source will be updated to http://mirrors.shu.edu.cn/raspbian/raspbian/


1. Installing TeamViewer Host:
```bash
sudo apt install ./teamviewer-host_armhf.deb
```

All set. TeamViewer will start with the system. You can log in with your account to remotely control it.
