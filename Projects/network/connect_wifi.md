# Raspberry Pi

* [Prerequisite](#prerequisite)
* [Connect to WiFi](#connect-to-wifi)

## Prerequisite

### Hardware
* Raspberry Pi, for this tutorial I'm using a `RPi 4 Model B Rev 1.1`.
* SD Card (at least 8 Gb), for this tutorial I have a 32 Gb SD Card.
* Unix based host machine to setup and connect to the RPi, for this tutorial I'm using a MacBook Pro (macOS 10.15.1 Catalina -- every version should works).

### Sofware
* [Rasbian](https://www.raspberrypi.org/downloads/raspbian/) - for this tutorial I download the `Raspbian Buster Lite`

## Scan network
Run the command:
```sh
sudo iwlist wlan0 scan
```
where `wlan0` is the interface for the WiFi

## Connect to WiFi
Edit the file `/etc/wpa_supplicant/wpa_supplicant.conf`
```sh
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US

network={
	ssid="42US Student"
	key_mgmt=WPA-EAP
	eap=PEAP
	identity="XXX"
	password="XXX"
	phase2="MSCHAPV2"
	priority=1
}
```

Add the following line in the file `/etc/network/interfaces`
```sh
auto lo
iface lo inet loopback

...

allow-hotplug wlan0
iface wlan0 inet dhcp
        pre-up wpa_supplicant -B -Dwext -i wlan0 -c/etc/wpa_supplicant/wpa_supplicant.conf
        post-down killall -q wpa_supplicant
```

# Ressources
* [RPi WPA Entreprise](https://www.raspberrypi.org/forums/viewtopic.php?p=353961#p353961)

# EOF
