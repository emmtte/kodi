# LibreELEC Raspberry Pi 4


## Configuration
```
mount -o remount,rw /flash
nano /flash/config.txt
  hdmi_enable_4kp60=1
  config_hdmi_boost=7
mount -o remount,ro /flash
reboot
```


## Update
```
systemctl stop kodi
wget -P /storage/.update https://releases.libreelec.tv/LibreELEC-RPi4.arm-11.0.3.img.gz
reboot
```


## Add-on

####  Add-ons > Repository -> LibreELEC Add-ons -> Program add-ons -> System tools
####  Add-ons > Repository -> LibreELEC Add-ons -> Services -> Docker
####  Add-ons > Repository -> LibreELEC Add-ons -> PVR clients -> PVR Freebox TV
- https://github.com/aassif/pvr.freebox

#### Settings > Settings 
  - Notifications : Off
  
## Power Saving
#### System > System settings > Power saving > Sleep / Shutdown 
- Put display to sleep when idle : 5min

#### Kodi > Interface settings > Screensaver
- Screensaver mode : Black
- Wait time : 4min
- Use visualisation if playing audio : Enable
  
#### System > Player settings > Music > Playback
- Visualisation : Shadertoy


## Skin Embuary
- https://github.com/sualfred/skin.embuary

#### Embuary > Appearance > Window configuration > General > Layout
- Hide main menu and only show widgets : Enable

#### Embuary > Appearance > Window configuration > General > Home and hubs > Home > Edit widget configuration
- 1 : Last tuned channels
- 2 : Continue watching
- 3 : Next up
- 4 : Latest TV shows
- 5 : Latest movies (custom 1)

#### Embuary > Appearance > Window configuration > General > Custom widgets
- Custom 1 : Latest movies
- Edit path : library://video/movies/recentlyaddedmovies.xml/
- Config : Type : Videos, Layout : Wide

#### Embuary > Appearance > General > Appearance > Background
- Show video playback in the background : Disable
- Show fanarts in the backgound : Enable
- Background opacity : very bright


## Context Menu
```
nano /storage/.kodi/userdata/keymaps/remote.xml
```
```xml
<keymap><global><remote>
  <blue>contextmenu</blue>
  <green>System.Exec("systemctl -q enable hyperion.service --now")</green>
  <red>System.Exec("systemctl -q disable hyperion.service --now")</red>
</remote></global></keymap>
```


## External Drive
```
e2label /dev/sda1 "storage"
reboot
```


## Playlist
```
cd music
nano sst.pls
```
```
[playlist]
numberofentries=2
File1=http://hi5.streamingsoundtracks.com
Title1=StreamingSoundtracks.com (hi5.streamingsoundtracks.com - 128k MP3)
File2=http://hi.streamingsoundtracks.com
Title2=StreamingSoundtracks.com (hi.streamingsoundtracks.com - 128k MP3)
Length1=-1
Version=2
```

## Docker

### Compose
- https://github.com/docker/compose/releases

```
mkdir /storage/docker
nano /storage/.profile
```
```bash
PATH=$HOME/docker:$PATH
```
```
wget -O /storage/docker/docker-compose https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-linux-aarch64
chmod +x /storage/docker/docker-compose
reboot
docker-compose --version
```


### Transmission
- https://github.com/linuxserver/docker-transmission

```
mkdir /storage/transmission
touch /storage/transmission/transmission.yml
nano /storage/transmission/transmission.yml
```
```yaml
---
version: "2.1"
services:
  transmission:
    container_name: transmission
    platform: linux/arm64
    image: lscr.io/linuxserver/transmission:latest
    environment:
      - PUID=0
      - PGID=0
      - TZ=Europe/Paris
      - USER=pi
      - PASS=raspberry
    volumes:
      - /storage/transmission/config:/config
      - /storage/transmission/watch:/watch
      - /var/media/storage/.transmission/downloads:/downloads
    ports:
      - 9091:9091/tcp
      - 51413:51413/tcp
      - 51413:51413/udp
    restart: unless-stopped
```
```
nano /storage/transmission/config/settings.json
```
```json
"peer-limit-global": 20,
"peer-limit-per-torrent": 10,
"speed-limit-down": 10000,
"speed-limit-down-enabled": true,
"speed-limit-up": 0,
"speed-limit-up-enabled": true,
```
```
docker-compose -f /storage/transmission/transmission.yml up -d
docker-compose -f /storage/transmission/transmission.yml down
```


### Pi-hole
- https://github.com/pi-hole/docker-pi-hole

```
cd /storage/docker
touch docker-pi-hole.yml
nano docker-pi-hole.yml
```
```yaml
version: "3"
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    environment:
      TZ=Europe/Paris
      WEBPASSWORD=raspberry
    volumes:
      - /storage/pi-hole/etc-pihole/:/etc/pihole/
      - /storage/pi-hole/etc-dnsmasq.d/:/etc/dnsmasq.d/
    ports:
      - 53:53/tcp
      - 53:53/udp
      - 67:67/udp
      - 80:80/tcp
    restart: unless-stopped
```
```
./docker-compose -f docker-pi-hole.yml up -d
./docker-compose -f docker-pi-hole.yml down
```

### Immich
- https://github.com/immich-app/immich
```
mkdir immich
wget -O immich.yml https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml
wget -O .env https://github.com/immich-app/immich/releases/latest/download/example.env
nano /storage/immich/.env
```
```
UPLOAD_LOCATION=/var/media/storage/Photos
DB_PASSWORD=password
TYPESENSE_API_KEY=key
```
```
nano /storage/immich/immich.yml
```
```
  services:
    immich-server:
      platform: linux/arm64
  ...
```
```
docker-compose -f /storage/immich/immich.yml up -d
docker-compose -f /storage/immich/immich.yml down
```
http://192.168.0.250:2283/


### Hyperion
- https://github.com/hyperion-project/hyperion.ng
- https://github.com/hyperion-project/hyperion.ng/issues/983
- https://gist.github.com/Paulchen-Panther/f0baf820343bbf800a041b102dd9cadd

#### Install
```
cd storage
wget -qO- https://git.io/Jz5Qp | bash -s Hyperion-2.0.13-beta.1-Linux-armv7l.tar.gz
systemctl -q enable hyperion.service --now
systemctl -q disable hyperion.service --now
```

#### Remove
```
rm -r hyperion
```
