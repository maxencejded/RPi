# Raspberry Pi

* [Prerequisite](#prerequisite)
* [Install Rclone](#install-rclone)

## Prerequisite

### Hardware
* Raspberry Pi, for this tutorial I'm using a `RPi 4 Model B Rev 1.1`.
* SD Card (at least 8 Gb), for this tutorial I have a 32 Gb SD Card.
* Unix based host machine to setup and connect to the RPi, for this tutorial I'm using a MacBook Pro (macOS 10.15 Catalina -- every version should works).

### Sofware
* [Rasbian](https://www.raspberrypi.org/downloads/raspbian/) - for this tutorial I download the `Raspbian Buster Lite`

## Install Rclone

Whit Rclone you can mount a Cloud Drive (Optionnal).
### Requirement
* curl (`sudo apt-get install -y curl`)
* fusermount (`sudo apt-get install -y sshfs`)

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

### EOF