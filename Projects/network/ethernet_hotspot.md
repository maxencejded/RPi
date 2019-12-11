# Raspberry Pi

* [Prerequisite](#prerequisite)
* [Share Internet from WiFi to Ethernet](#share-internet-from-wifi-to-ethernet)

## Prerequisite

### Hardware
* Raspberry Pi, for this tutorial I'm using a `RPi 4 Model B Rev 1.1`.
* SD Card (at least 8 Gb), for this tutorial I have a 32 Gb SD Card.
* Unix based host machine to setup and connect to the RPi, for this tutorial I'm using a MacBook Pro (macOS 10.15.1 Catalina -- every version should works).

### Sofware
* [Rasbian](https://www.raspberrypi.org/downloads/raspbian/) - for this tutorial I download the `Raspbian Buster Lite`

## Share Internet from WiFi to Ethernet
### Connect to WiFi
Follow the guide in this directory: `connect_wifi.md`.

### Setup Static IP Ethernet
Edit the file `/etc/dhcpcd.conf` by adding at the end of the file
```sh
denyinterfaces eth0
```

Edit the file `/etc/network/interfaces` to set a static IP to `eth0`
```sh
auto lo
iface lo inet loopback

auto wlan0
...

auto eth0
allow-hotplug eth0
iface eth0 inet static
    address 100.0.0.1
    netmask 255.255.255.0
    network 100.0.0.0
    broadcast 100.0.0.255
```

Install `dnsmasq` and edit the file `/etc/dnsmasq.conf`
```console
pi @ raspberrypi:~ $ sudo apt-get install dnsmasq
pi @ raspberrypi:~ $ sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig
pi @ raspberrypi:~ $ sudo vim /etc/dnsmasq.conf
interface=eth0
listen-address=100.0.0.1
dhcp-range=100.0.0.50,100.0.0.100,12h
server=8.8.8.8
bind-interfaces
domain-needed
bogus-priv
```

### Sharing the Internet
Setup IPv4 forwarding
```console
pi @ raspberrypi:~ $ sudo cp /etc/sysctl.conf /etc/sysctl.conf.origin
pi @ raspberrypi:~ $ sudo vim /etc/sysctl.conf
#net.ipv4.ip_forward=1 <- Old
net.ipv4.ip_forward=1
```

Create a new file `/proc/sys/net/ipv4/ip_forward`
```console
pi @ raspberrypi:~ $ sudo sh -c "echo 1 > /proc/sys/net/ipv4/ip_forward"
```

Reset the IP Table by creating a script
```console
pi @ raspberrypi:~ $ sudo vim tablereset.sh
#!/bin/sh
ipt="/sbin/iptables"
[ ! -x "$ipt" ] && { echo "$0: \"${ipt}\" command not found."; exit 1; }
$ipt -P INPUT ACCEPT
$ipt -P FORWARD ACCEPT
$ipt -P OUTPUT ACCEPT
$ipt -F
$ipt -X
$ipt -t nat -F
$ipt -t nat -X
$ipt -t mangle -F
$ipt -t mangle -X
$ipt -t raw -F
$ipt -t raw -X
```

Execute the script and Adding rules
```console
pi @ raspberrypi:~ $ sudo sh tablereset.sh
pi @ raspberrypi:~ $ sudo iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
pi @ raspberrypi:~ $ sudo iptables -A FORWARD -i wlan0 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT
pi @ raspberrypi:~ $ sudo iptables -A FORWARD -i eth0 -o wlan0 -j ACCEPT
```

Check the changes made
```console
pi @ raspberrypi:~ $ sudo iptables -L -n -v
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  wlan0  eth0    0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
    0     0 ACCEPT     all  --  eth0   wlan0   0.0.0.0/0            0.0.0.0/0

Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
```

Save the changes
```console
pi @ raspberrypi:~ $ sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
```

Load the rules on boot
```console
pi @ raspberrypi:~ $ sudo vim /etc/rc.local
iptables-restore < /etc/iptables.ipv4.nat

exit 0
```

Check the route
```console
pi @ raspberrypi:~ $ sudo route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         192.168.2.1     0.0.0.0         UG    304    0        0 wlan0
100.0.0.0       *               255.255.255.0   U     0      0        0 eth0
192.168.2.0     *               255.255.255.0   U     304    0        0 wlan0
```

# Ressources
* [RPi Share WiFi internet Ethernet](https://www.diyhobi.com/share-raspberry-pi-wifi-internet-ethernet/)
* [RPi Wireless-to-Wired Ethernet](https://www.glennklockwood.com/sysadmin-howtos/rpi-wifi-island.html)

# EOF