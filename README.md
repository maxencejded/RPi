# Raspberry Pi

* [Prerequisite](#prerequisite)
* [Setup the SD Card](#setup-the-sd-card)
* [Setup Raspbian](#setup-raspbian)
* [Go Further](#go-further)

## Prerequisite

### Hardware
* Raspberry Pi, for this tutorial I'm using a `RPi 4 Model B Rev 1.1`.
* SD Card (at least 8 Gb), for this tutorial I have a 32 Gb SD Card.
* Unix based host machine to setup and connect to the RPi, for this tutorial I'm using a MacBook Pro (macOS 10.15 Catalina -- every version should works).

### Sofware
* [Rasbian](https://www.raspberrypi.org/downloads/raspbian/) - for this tutorial I download the `Raspbian Buster Lite`
* [Etcher](https://www.balena.io/etcher/) - If you don't want to download the program, you can do it using command line

## Setup the SD Card

### Etcher
Launch the programme.
Select the image (rasbian image freshly downloaded `2019-07-10-raspbian-buster-lite.img`. If it's a `.zip`, unzip it).
Select the target (Plug the SD Card)
Click `Flash!`, you will be asked to put your password

### Command Line
Open Terminal on macOS (`/Application/Utilities/Terminal`)

Type the following command and find your SD Card.
```console
$ diskutil list
/dev/disk0 (internal, physical):
	...
/dev/disk1 (synthesized):
	...
/dev/disk2 (external, physical):	# <- This one for me 
	...
```

Unmount your SD Card using his identifier
```console
$ diskutil unmountDisk /dev/disk2
```

Copy the operating system images on the SD Card
```console
$ sudo dd bs=1m if="~/Downloads/2019-07-10-raspbian-buster-lite.img" of=/dev/rdisk2
```

When the operation is done, the prompt come back.

Now you can eject the SD Card
```console
sudo diskutil eject /dev/rdisk2
```

### Active SSH on the RPi
Before ejecting the SD Card and Start using the RPi, I recommand to add the possibilities to connect to SSH, you just need to create a file called ssh on the `boot` volume
```console
$ touch /Volumes/boot/ssh
```

## Setup Raspbian

Connect to the RPi by SSH
```console
pi@raspberrypi $ ssh pi@raspberrypi.local
...
Password: <default password: raspberry>
```

Setup using the RPi using the internal script
```console
pi@raspberrypi $ sudo raspi-config
```

Advice:
* Change your password !!!
* Setup Localisation Options
* Expand Filesystem (in Advanced Options)

Reboot your Pi

Connect again and update the System
```console
pi@raspberrypi $ sudo apt-get update
pi@raspberrypi $ sudo apt-get upgrade -y
pi@raspberrypi $ sudo apt-get autoremove -y
# Personnal setup
pi@raspberrypi $ sudo apt-get install -y zsh git vim
```

## Go Further
List of common ports
```console
pi@raspberrypi $ less /etc/services
```

Scan ports open on the RPi
```console
pi@raspberrypi $ sudo nmap -v localhost
```

# Ressources
[Installing operating system](https://www.raspberrypi.org/documentation/installation/installing-images/mac.md)

### EOF