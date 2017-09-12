# Wireless-to-WLAN-bridge-with-Raspberry
Create a sistem that connect to a wifi and expose that to a lan port.

# How to

## Prepare raspberry
### Material
* raspberry
* raspberry alimentation cable
* usb mouse
* usb keyboard
* ethernet cable
* etheret Hub
* HDMI cable
* display that support HDMI input
* [micro sd](https://www.sandisk.it/content/dam/sandisk-main/en_us/portal-assets/product-images/retail-products/microSD_SDHC_Class4_8GB.png) with 8GB or more
* PC
* something to connect micro sd to your PC
### Create operative sistem micro sd
First of obtain Raspbian.
* [download](https://www.raspberrypi.org/downloads/raspbian/) Raspbian Stretch with desktop
* use a micro sd with 8GB or more
* download and install [etcher](https://etcher.io/)
* use etcher to put rapbian on your micro sd
### Prepare for start
* connect to your raspberry:
    * usb mouse
    * usb keyboard
    * etheret cable
    * HDMI cable
* connect the other extremity of ethernet cable to ethernet Hub
* connect the other extremity of HDMI cable to display
* alimentate rasberry

## Create the bridge
* open a terminal (ctrl + alt + t)
* modify the /etc/wpa_supplicant/wpa_supplicant.conf \
`sudo nano /etc/wpa_supplicant/wpa_supplicant.conf` \
 as in the [wpa_supplicant.conf file](https://github.com/nicolalandro/Wireless-to-WLAN-bridge-with-Raspberry/blob/master/wpa_supplicant.conf), remember to modify it with your wow fi credential
* remove the old wlan0 file \
`sudo rm /var/run/wpa_supplicant/wlan0`
* Try this command \
`sudo wpa_supplicant -Dwext -iwlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf` \
if it fail do `sudo reboot` after you shold be connected to wow fi
* `sudo apt-get install dnsmasq`
* `sudo reboot`
* block connection \
`sudo ifdown -a`
* modify the file /etc/network/interfaces \
`sudo nano /etc/network/interfaces` \
as in the [interfaces file](https://github.com/nicolalandro/Wireless-to-WLAN-bridge-with-Raspberry/blob/master/interfaces)
* restart connection \
`sudo ifup -a`
* modify the file /etc/dnsmasq.conf \
`sudo nano /etc/dnsmasq.conf`
as in the [dansmasq.conf file](https://github.com/nicolalandro/Wireless-to-WLAN-bridge-with-Raspberry/blob/master/dnsmasq.conf) \
pay attention to not insert in the range the ip ethernet of Raspberry
* modify /etc/sysctl.conf \
`sudo nano /etc/sysctl.conf`\
uncomment the line `net.ipv4.ip_forward=1`
* `sudo iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE`
* `sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"`
* create the file /lib/dhcpcd/dhcpcd-hooks/70-ipv4-nat \
`sudo nano /lib/dhcpcd/dhcpcd-hooks/70-ipv4-nat` \
and insert `iptables-restore < /etc/iptables.ipv4.nat`
* `sudo service dnsmasq start`
* `sudo reboot`

## Before connect your pc to ethernet Hub
set IPv4 in the pc that will use it
* IP: a casual in the selected range es: 169.254.216.51
* mask: 255.255.0.0
* gateway: 169.254.216.3    `IP ethernet Raspberry`
* DNS 1: 8.8.8.8 `google DNS`
* DNS 2: 8.8.4.4

## Connect your pc
now you can connect your pc to the ethernet Hub
    
# Bibliography
* [Raspberry forum: Pi as wireless bridge?](file:///home/mint/Scrivania/Mega/RaspberryPi/BridgeWork/Pi%20as%20wireless%20bridge_%20-%20Raspberry%20Pi%20Forums.html)
* [Raspberry forum: How To: Wifi to Ethernet Bridge(Updated for RPi 3)](https://www.raspberrypi.org/forums/viewtopic.php?t=132674)
* [Chaucery Blog: Raspberry Pi wireless bridge](http://blog.chaucery.com/2013/02/raspberry-pi-wireless-bridge.html)
* [Using your new Raspberry Pi 3 as a WiFi access point with hostapd](https://frillip.com/using-your-raspberry-pi-3-as-a-wifi-access-point-with-hostapd/)
