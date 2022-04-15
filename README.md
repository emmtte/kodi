# LibreELEC Raspberry Pi 4 Startup 

## Kodi Startup

- Add-ons / Install from repositery / LibreELEC Add-ons / Add-on repository / Services / Docker
- Add-ons / Install from repositery / LibreELEC Add-ons / Add-on repository / LinuxServer.io's Docker Add-ons
- Add-ons / Install from repositery / LinuxServer.io's Docker Add-ons / Services / Docker Image Updater

## Add-on Catch-up TV and more
https://catch-up-tv-and-more.github.io/installation
- Settings / File Manager / Add source / Add network location
  - Protocol : Web server directory (HTTPS)
  - Server address: catch-up-tv-and-more.github.io
  - Remote path : repo
- System settings / Settings / Add-ons / Unknown sources / Confirm
- Add-on browser / Install from zip file / Catch-Up TV & More / stable
- Add-on browser / Install from Repositery / Catch-Up TV & More repository addon / Videos extensions / Catch-Up TV & More

## Add-on IPTV Manager
https://catch-up-tv-and-more.github.io/live_tv_installation/
- Add-ons 

## Add-on PVR Freebox
https://github.com/aassif/pvr.freebox


## Power Saving
System settings / Power saving / Put display to sleep when idle / 5min

- select a screensaver under appearance -> screensaver (even if you don't need it), set it to 4 minutes (it must be set to < 5, if not, it won't work)
- enable "use visualisation if playing audio" in same screen
- select a music visualisation under music -> playback
Appearance / Show video playback in the background / Off
Appearance / Background / Background opacity / very bright

## Skin Embuary
- https://github.com/sualfred/skin.embuary
-

## Enable Context Menu
```
nano /storage/.kodi/userdata/keymaps/remote.xml
<keymap><global><remote><red>contextmenu</red></remote></global></keymap>
```
## Rename external usb drive
```
e2label /dev/sda1 "storage"
reboot
```

## Streaming Soundtracks
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

- select a screensaver under appearance -> screensaver (even if you don't need it), set it to 4 minutes (it must be set to < 5, if not, it won't work)
- enable "use visualisation if playing audio" in same screen
- select a music visualisation under music -> playback


## Docker Startup
### docker-docker-compose
- https://github.com/linuxserver/docker-docker-compose
```
mkdir /storage/docker
curl -L --fail https://raw.githubusercontent.com/linuxserver/docker-docker-compose/master/run.sh -o /storage/docker/docker-compose
chmod +x /storage/docker/docker-compose
cd /storage/docker
./docker-compose --version
```

### docker-transmission
- https://github.com/linuxserver/docker-transmission
```
cd /storage/docker
touch docker-transmission.yml
nano docker-transmission.yml
```
```yaml
---
version: "2.1"
services:
  transmission:
    container_name: transmission
    image: ghcr.io/linuxserver/transmission
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
./docker-compose -f docker-transmission.yml up -d
./docker-compose -f docker-transmission.yml down
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


### docker-pi-hole
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

### Hyperion
- https://github.com/hyperion-project/hyperion.ng
- https://github.com/hyperion-project/hyperion.ng/issues/983
- https://gist.github.com/Paulchen-Panther/f0baf820343bbf800a041b102dd9cadd

Install
```
cd storage
wget -qO- https://git.io/Jz5Qp | bash -s Hyperion-2.0.13-beta.1-Linux-armv7l.tar.gz
systemctl -q enable hyperion.service --now
systemctl -q disable hyperion.service --now
```

Remove
```
rm -r hyperion
```

### TODO
Kodi
- https://github.com/sualfred/skin.embuary
- https://github.com/Catch-up-TV-and-More/plugin.video.catchuptvandmore
Info
- /storage => SD Card
- /var/media => HDD Drive
