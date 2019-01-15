### Install Raspbian on a Raspberry Pi 3

Updated by QuLk @ Oct 05 2017.  \
Updated by QuLk @ July 14 2018.  \
Updated by QuLk @ Jan 15 2019.  

This instructions is tested on a RASPBERRY PI 3 MODEL B with a **32GB** memory card. For cards over 32GB, please refer the INSTRUCTIONS-README.txt for installation instruction after unzip NOOBS_v2_4_4.zip.

**Notice: to make WIFI work, the information of ‘WIFI Country’ must be filled in.**

#### Steps

1. Download the image to your laptop [latest `Raspbian NOOBS` image](https://www.raspberrypi.org/downloads/noobs/). Earlier versions of Raspbian won't work.

2. Upzip the image and copy to a memory card according to readme file in the image, put the memory card in the RPi and boot it up. 

3. Log in. Default username / password is `pi` / `raspberry`.

4. Run in command window: `sudo raspi-config`, or open the Raspberry Pi Configuration Tool from **start -> Perferneces -> Raspberri Pi Configuration**, and configure the basics:
    - At System tab:
        - Change default user password.
        - Give a hostname.
        - set Resolution for your monitor.
        
    - At Interfaces tab:
        - Enable SSH or whatever you plan to use.
        
    - At Performance tab:
        - Configure gpu memory split under 'Advanced' if needed. It is '64' on  Raspberry Pi 3.
        
    - At Localization tab:
        - Set up Locale: Language, Country. 
        - Set up Timezone.
        - Set up your keyboard layout (It defaults to a British keyboard layout)
        - **Set up WiFi Country. To make WIFI work, the information of ‘WIFI Country’ must be filled in.**
        
5. Save changes and reboot.

6. Set up Wifi (if you are using Wifi). Raspberry Pi 3 has WIFI hardware.
##
You should be all set. Enjoy !
