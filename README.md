# Wifi-Extender
Creating a Wifi Bridge with Raspberry Pi 4

(1) Purchasing Raspberry Pi 4
https://www.adafruit.com/product/4292?gclid=Cj0KCQjwm9yJBhDTARIsABKIcGaMCCXctRT9XmAgbyTRG1ColiUDm71fjImdOMnD-qp_AgXYHO1TshgaAmrSEALw_wcB

(2) Formatting SD Card
diskutil list to identify where the SD card is (unmount if its busy)
Use the following file from Andrew https://blog.qartis.com/void-rpi4-musl-20210828.img.xz
sudo dd if=~/Downloads/void-rpi4-musl-20210828.img of=/dev/rdisk2 bs=4m 

(3) Logging in
user: root 
password: void-linux

(4) Setting up dhcpcd to run automatically
sudo ln -s /etc/sv/dhcpcd /var/service

(5) Getting Connected to the Wifi:
wpa_passphrase SSID PASSWORD >> /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
wpa_supplicant -B -iwlan0 -c/etc/wpa_supplicant/wpa_supplicant-wlan0.conf (this needs to be ran on every resart)
https://unix.stackexchange.com/questions/531071/how-to-connect-with-wifi-during-void-linux-installation

Andrew has offered the following alternative:
let's try to simplify that
here's my /etc/wpa_supplicant/wpa_supplicant.conf:
ctrl_interface=/run/wpa_supplicant
ctrl_interface_group=wheel
update_config=1

network={
	ssid="NETGEAR-2"
	psk="xxx"
}
and then I just ln -s /etc/sv/wpa_supplicant /var/service


(6) SHHing into the PI:
sudo ln -s /etc/sv/sshd /var/service/
Note: I encountered a problem that made me want to reset the SD card on the pi. This generated a new SSH authentication/key. This had to be added to the /.shh/known_hosts file

(7) Getting Bash and Vim going:
Typing in "Bash" to switch to bash
Setting the date sudo date -s '2021-01-04 13:04:00'
xbps-install -S vim
Setting up vimrc at vim ~/.vimrc

(8) Following the Tutorial
https://willhaley.com/blog/raspberry-pi-wifi-ethernet-bridge/
