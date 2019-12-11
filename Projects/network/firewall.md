# Raspberry Pi

* [Prerequisite](#prerequisite)
* [Setup Firewall](#setup-raspbian)
* [Protect for DDOS](#protect-for-ddos)

## Prerequisite

### Hardware
* Raspberry Pi, for this tutorial I'm using a `RPi 4 Model B Rev 1.1`.
* SD Card (at least 8 Gb), for this tutorial I have a 32 Gb SD Card.
* Unix based host machine to setup and connect to the RPi, for this tutorial I'm using a MacBook Pro (macOS 10.15 Catalina -- every version should works).

### Sofware
* [Rasbian](https://www.raspberrypi.org/downloads/raspbian/) - for this tutorial I download the `Raspbian Buster Lite`

## Setup Firewall
The default policy in a default installation is to ACCEPT all traffic. So we need to DROP all the all traffic except the one we are really using.

Copy the content of `Ressources/firewall.service` in the RPi
```console
pi@raspberrypi $ sudo vim firewall.service
```

Copy the file `Ressources/scripts/firewall.sh` in the RPi
```console
pi@raspberrypi $ mkdir -p /etc/script
pi@raspberrypi $ sudo vim /etc/script/firewall.sh
pi@raspberrypi $ sudo chomd 0700 /etc/script/firewall.sh
```

Enable and Start the service *firewall.service*
```console
pi@raspberrypi $ sudo chmod 0600 firewall.service
pi@raspberrypi $ sudo cp firewall.service /etc/systemd/system/firewall.service
pi@raspberrypi $ sudo systemctl enable firewall.service
pi@raspberrypi $ sudo systemctl start firewall.service
```

Verify that the rules are working
```console
pi@raspberrypi $ sudo iptables -S
-P INPUT DROP
-P FORWARD DROP
-P OUTPUT ACCEPT
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -s 127.0.0.0/8 -d 127.0.0.0/8 -i lo -j ACCEPT
-A INPUT -p tcp -m tcp --dport 22 -m state --state NEW -j ACCEPT
```

## Protect for DDOS
Install the following package
```console
pi@raspberrypi $ sudo apt-get install -y fail2ban
```

# Ressources
[fail2ban](https://askubuntu.com/questions/745051/iptables-and-hacker-prevention)

### EOF