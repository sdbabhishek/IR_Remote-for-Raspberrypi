Notes to make IR shield (made by LinkSprite) work in Raspberry Pi 3 (bought from Amazon [1]). 
The vendor has some documentation [2] but that is not complete and sufficient for Raspbian Stretch. 
Following are the changes that I made to make it work.

$ sudo apt-get update
$ sudo apt-get install lirc

# Add the following lines to /etc/modules file
lirc_dev
lirc_rpi gpio_in_pin=18 gpio_out_pin=17

# Add the following lines to /etc/lirc/hardware.conf file
LIRCD_ARGS="--uinput --listen"
LOAD_MODULES=true
DRIVER="default"
DEVICE="/dev/lirc0"
MODULES="lirc_rpi"

# Update the following line in /boot/config.txt
dtoverlay=lirc-rpi,gpio_in_pin=18,gpio_out_pin=17

# Update the following lines in /etc/lirc/lirc_options.conf
driver    = default
device    = /dev/lirc0

$ sudo /etc/init.d/lircd stop
$ sudo /etc/init.d/lircd start

# Check status to make lirc is running
$ sudo /etc/init.d/lircd status

# Reboot before testing
$ reboot

# To test if lirc driver is working
$ sudo /etc/init.d/lircd stop
$ mode2 -d /dev/lirc0
<press a key in remote and you should see multple lines like below>
pulse 560
space 1706
pulse 535

# to record a custom remote/register a remote device
$ sudo /etc/init.d/lircd stop
$ sudo irrecord -d /dev/lirc0 ~/lircd.conf
# follow the instruction prompted by the above command carefully
# at the end ~/lircd.conf file will be generated

# backup the original lircd.conf
$ sudo mv /etc/lirc/lircd.conf /etc/lirc/lircd_original.conf
$ sudo cp ~/lircd.conf /etc/lirc/lircd.conf
$ sudo /etc/init.d/lircd start

# you can test if the recorded remote works by
$ irsend SEND_ONCE <device-name> KEY_POWER
$ irsend SEND_ONCE <device-name> KEY_VOLUMEUP

[1] https://www.amazon.com/Infrared-Shield-for-Raspberry-Pi/dp/B00K2IICKK/ref=pd_sbs_328_1?_encoding=UTF8&psc=1&refRID=1QPY33VFCGETBJ17K8QE
[2] http://learn.linksprite.com/raspberry-pi/shield/infrared-transceiver-on-raspberry-pi-lirc-software-installation-and-configuration/
