# Raspberry Pi

* [Prerequisite](#prerequisite)
* [Setup the SD Card](#setup-the-sd-card)
* [Setup Raspbian](#setup-raspbian)
* [Install Rclone](#install-rclone)
* [Install Plex Media Server](#install-plex-media-server)
* [Go Further](#go-further)

## Prerequisite

### Hardware
> Raspberry Pi, for this tutorial I'm using a `RPi 4 Model B Rev 1.1`.
> SD Card (at least 8 Gb), for this tutorial I have a 32 Gb SD Card.
> Unix based host machine to setup and connect to the RPi, for this tutorial I'm using a MacBook Pro (macOS 10.15 Catalina -- every version should works).

### Sofware
> [Rasbian](https://www.raspberrypi.org/downloads/raspbian/) - for this tutorial I download the `Raspbian Buster Lite`
> [Etcher](https://www.balena.io/etcher/) - If you don't want to download the program, you can do it using command line

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
> Change your password !!!
> Setup Localisation Options
> Expand Filesystem (in Advanced Options)

Reboot your Pi

Connect again and update the System
```console
pi@raspberrypi $ sudo apt-get update
pi@raspberrypi $ sudo apt-get upgrade -y
pi@raspberrypi $ sudo apt-get autoremove -y
# Personnal setup
pi@raspberrypi $ sudo apt-get install -y zsh git vim
```

### Setup Firewall
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

### Protect for DOS and Port Scans
Install the following package
```console
pi@raspberrypi $ sudo apt-get install -y fail2ban
```

## Install Rclone

Whit Rclone you can mount a Cloud Drive (Optionnal).
### Requirement
> curl (`sudo apt-get install -y curl`)
> fusermount (`sudo apt-get install -y sshfs`)

### Installation
Download rclone on the RPi
```console
pi@raspberrypi $ curl https://rclone.org/install.sh | sudo bash
```

You will need to provide a `client_id` and `client_secret`, you need to create thoses keys by following this tutorial [Google Drive API](https://github.com/Cloudbox/Cloudbox/wiki/Google-Drive-API-Client-ID-and-Client-Secret)

Install and setup rclone (follow the instructions)
```console
pi@raspberrypi $ rclone config
```

Copy the configuration file
```console
pi@raspberrypi $ sudo mkdir /opt/rclone
pi@raspberrypi $ sudo cp ~/.config/rclone/rclone.conf /opt/rclone
```

Copy the content of `Ressources/gdrive.service` in the VM
```console
pi@raspberrypi $ sudo gdrive.service
```

Enable and Start the service *gdrive.service*
```console
pi@raspberrypi $ sudo chmod 0600 gdrive.service
pi@raspberrypi $ sudo cp gdrive.service /etc/systemd/system/gdrive.service
pi@raspberrypi $ sudo systemctl enable gdrive.service
pi@raspberrypi $ sudo systemctl start gdrive.service
```

See [Command Systemctl](##annexes#commands-services) for more informations to handle services.

## Install Plex Media Server

### Requirement
> curl (`sudo apt-get install -y curl`)
> apt-transport-https (`sudo apt-get install -y apt-transport-https`)

### Installation
Add the official plexmediaserver to the repository
```console
pi@raspberrypi $ curl https://downloads.plex.tv/plex-keys/PlexSign.key | sudo apt-key add -
pi@raspberrypi $ echo deb https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
```

Install plexmediaserver
```console
pi@raspberrypi $ sudo apt-get update
pi@raspberrypi $ sudo apt-get install plexmediaserer
```

Enable and Start plexmediaserver.service
```console
pi@raspberrypi $ sudo systemctl enable plexmediaserver.service
pi@raspberrypi $ sudo systemctl start plexmediaserver.service
```

### Configuration
Go tho this address [Plex](raspberrypi.local:32400/manage)

## Go Further
List of common ports
```console
pi@raspberrypi $ less /etc/services
```

Scan ports open on the RPi
```console
pi@raspberrypi $ sudo nmap -v localhost
```

## Annexes

### Services commands
```
# Status service
sudo systemctl status $SERVICE
# Start service
sudo systemctl start $SERVICE
# Stop service
sudo systemctl stop $SERVICE
# Check service's log
sudo journalctl -f -u $SERVICE
```

# Ressources
[Installing operating system](https://www.raspberrypi.org/documentation/installation/installing-images/mac.md)
[fail2ban](https://askubuntu.com/questions/745051/iptables-and-hacker-prevention)
[Enable Plex-Media-Server in repository](https://support.plex.tv/articles/235974187-enable-repository-updating-for-supported-linux-server-distributions/)
[Create user on Linux](https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-debian-8)
[Nginx Debian](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-debian-9)
[SSL Certificate for Nginx](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-on-debian-9)

### EOF