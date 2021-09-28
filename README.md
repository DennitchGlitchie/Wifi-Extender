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

Andrew has offered the following alternative: (I have gotten this to work successfully)
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
Setting the date sudo date -s '2021-01-04 13:04:00' (have to do this every time? )
xbps-install -S vim
Setting up vimrc at vim ~/.vimrc

(8) Package Manager Nix
xbps-install -Sy nix
https://voidlinux.org/news/2014/01/Using-the-Nix-package-manager.html

(8) Following the Tutorial
https://willhaley.com/blog/raspberry-pi-wifi-ethernet-bridge/

(9) using xbps-install instead of apt-get



xbps-query -R -s parprouted  #search for the package (tells us that it was installed)
xbps-query -f parprouted #what is installed specifically (binary and the manual file)
mkdir /etc/sv/parprouted #"parprouted" is up to us to name
We are utilizeing runit instead of systemd 

Setting the date time 
ln -s /etc/sv/ntpd/ /var/service

Diagnosing "dhcp-helper: cannot bind DHCP server socket: Address in use"
https://wiki.voidlinux.org/Frequently_Asked_Questions#Which_package_contains_XYZ.3F
xbps-install net-tools
netstat -nlp (this is di

hmm, I've never heard of iwgetid
I just use "iw dev wlan0 link"

Checking what wifi network I'm on: 
ok I tried xbps-query -Ro and it didn't work for me either
I actually use xtools so I didn't know how discouraged xbps-query -Ro is
it turns out it's a really shitty feature that re-downloads the first chunk of every single package from the mirror to search it
there's an add-on utility to xbps, called xtools
that provides some nice additional features, including a tool caled xlocate that builds a searchable index of all files in all packages
so you can "xbps-install xtools",
then try "xlocate netstat" and it'll say you need to run "xlocate -S" to generate the index
once you generate the index, "xlocate netstat" will find a bunch of unrelated hits, files like gnome-netstatus-txrx.svg
so then you can search more specifically: xlocate bin/netstat
which would find /usr/bin/netstat, /sbin/netstat, etc
and that search would find the single package that contains /usr/bin/netstat

On Raspberry Pi:
ip link set up dev eth0
ip a add 10.99.0.1/24 dev eth0
ping 10.99.0.2
iptables-save
iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
iptables -A FORWARD -i wlan0 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i eth0 -o wlan0 -j ACCEPT
echo 1 > /proc/sys/net/ipv4/ip_forward

On Macbook: 
Going to network preferences 
Configure IPv4: Manually
IP Address: 10.99.0.2
Subnet Maks: 255.255.255.0
Router 10.99.0.1

Need to add DNS servers too 
https://www.cnet.com/tech/computing/how-to-change-dns-windows-mac-osx/


