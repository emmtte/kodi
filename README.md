## LibreELEC Raspberry Pi 4 Startup 

### Kodi Startup

- Add-ons / Install from repositery / LibreELEC Add-ons / Add-on repository / Services / Docker
- Add-ons / Install from repositery / LibreELEC Add-ons / Add-on repository / LinuxServer.io's Docker Add-ons
- Add-ons / Install from repositery / LinuxServer.io's Docker Add-ons / Services / Docker Image Updater
- Add-ons / Install from repositery / LinuxServer.io's Docker Add-ons / Services / Portainer

### docker compose
- https://github.com/linuxserver/docker-docker-compose/releases
```
mkdir /storage/.docker
curl -L --fail https://raw.githubusercontent.com/linuxserver/docker-docker-compose/master/run.sh -o /storage/.docker/docker-compose
chmod +x /storage/.docker/docker-compose
cd /storage/.docker
./docker-compose --version
```

### transmission
- https://github.com/linuxserver/docker-transmission
```
cd /storage/.docker
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
      - /storage/.transmission/config:/config
      - /storage/.transmission/downloads:/downloads
      - /storage/.transmission/watch:/watch
    ports:
      - 9091:9091/tcp
      - 51413:51413/tcp
      - 51413:51413/udp
    restart: unless-stopped
```
```
docker-compose -f docker-transmission.yml up -d
docker-compose -f docker-transmission.yml down
```

### pi-hole
- https://github.com/pi-hole/docker-pi-hole

```
cd /storage/.docker
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
      - /storage/.pi-hole/etc-pihole/:/etc/pihole/
      - /storage/.pi-hole/etc-dnsmasq.d/:/etc/dnsmasq.d/
    ports:
      - 53:53/tcp
      - 53:53/udp
      - 67:67/udp
      - 80:80/tcp
    restart: unless-stopped
```
```
docker-compose -f docker-pi-hole.yml up -d
docker-compose -f docker-pi-hole.yml down
```

### TODO
Kodi
- https://github.com/sualfred/skin.embuary
- https://github.com/Catch-up-TV-and-More/plugin.video.catchuptvandmore
Info
- /storage => SD Card
- /var/media => HDD Drive
