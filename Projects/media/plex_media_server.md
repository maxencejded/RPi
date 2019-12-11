# Raspberry Pi

* [Prerequisite](#prerequisite)
* [Install Plex Media Server](#install-plex-media-server)

## Prerequisite

### Hardware
* Raspberry Pi, for this tutorial I'm using a `RPi 4 Model B Rev 1.1`.
* SD Card (at least 8 Gb), for this tutorial I have a 32 Gb SD Card.
* Unix based host machine to setup and connect to the RPi, for this tutorial I'm using a MacBook Pro (macOS 10.15 Catalina -- every version should works).

### Sofware
* [Rasbian](https://www.raspberrypi.org/downloads/raspbian/) - for this tutorial I download the `Raspbian Buster Lite`

## Install Plex Media Server

### Requirement
* curl (`sudo apt-get install -y curl`)
* apt-transport-https (`sudo apt-get install -y apt-transport-https`)

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

# Ressources
[Enable Plex-Media-Server in repository](https://support.plex.tv/articles/235974187-enable-repository-updating-for-supported-linux-server-distributions/)

### EOF