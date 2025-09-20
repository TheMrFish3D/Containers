# Servarr Stack

This stack provides a complete media automation suite:

- Sonarr: automates TV series management
- Radarr: automates movie management
- Lidarr: automates music management
- Prowlarr: central indexer manager for *arr apps
- Bazarr: subtitle management for movies/TV
- Overseerr: media request portal integrated with Plex/Jellyfin and *arr
- SABnzbd: Usenet downloader
- qBittorrent: Torrent downloader

## Quick start

1) Ensure host paths under `/fileServerMountPath` exist and set PUID/PGID to your media user. On Docker Desktop with WSL, this typically lives under `/mnt/<drive>` (e.g., `/mnt/d`).
2) Start: `docker compose up -d`
3) Stop: `docker compose down`
4) Update: `docker compose pull && docker compose up -d`

## Service ports

- Sonarr: http://localhost:8989
- Radarr: http://localhost:7878
- Lidarr: http://localhost:8686
- Prowlarr: http://localhost:9696
- Bazarr: http://localhost:6767
- Overseerr: http://localhost:5055
- SABnzbd: http://localhost:8080
- qBittorrent: http://localhost:8081

## Volumes

- `/fileServerMountPath/containers/<service>` -> `/config`
- `/fileServerMountPath/data` -> `/data`
- `/fileServerMountPath/data/downloads` -> `/downloads`
- `/fileServerMountPath/data/scripts` -> `/scripts` (optional)

## Wiring overview

- Prowlarr holds indexers and syncs them to Sonarr/Radarr/Lidarr.
- Sonarr/Radarr/Lidarr send grabs to SABnzbd/qBittorrent.
- On completion, the *arr apps import from `/downloads` into the `/data` library.

## Mount path guidance

Set `/fileServerMountPath` to the root of where your media and configs are mounted on the Linux host.

- Docker Desktop with WSL: mounts are typically under `/mnt/<drive>` (e.g., `/mnt/d`).
- Using locally mounted host paths simplifies RBAC and file permissions across containers.

## Links

- Docker Hub: [sonarr](https://hub.docker.com/r/linuxserver/sonarr), [radarr](https://hub.docker.com/r/linuxserver/radarr), [lidarr](https://hub.docker.com/r/linuxserver/lidarr), [prowlarr](https://hub.docker.com/r/linuxserver/prowlarr), [bazarr](https://hub.docker.com/r/linuxserver/bazarr), [overseerr](https://hub.docker.com/r/linuxserver/overseerr), [sabnzbd](https://hub.docker.com/r/linuxserver/sabnzbd), [qbittorrent](https://hub.docker.com/r/linuxserver/qbittorrent)
- GitHub: [sonarr](https://github.com/linuxserver/docker-sonarr), [radarr](https://github.com/linuxserver/docker-radarr), [lidarr](https://github.com/linuxserver/docker-lidarr), [prowlarr](https://github.com/linuxserver/docker-prowlarr), [bazarr](https://github.com/linuxserver/docker-bazarr), [overseerr](https://github.com/linuxserver/docker-overseerr), [sabnzbd](https://github.com/linuxserver/docker-sabnzbd), [qbittorrent](https://github.com/linuxserver/docker-qbittorrent)

## Tips

- Use consistent PUID/PGID and UMASK across all services to avoid permission issues.
- In qBittorrent, set the default save path to `/downloads` and categories per *arr app.
- In SABnzbd, set completed download folder to `/downloads` and configure category mapping for *arr.

```
/fileServerMountPath/
├── containers/          # Application configurations
│   ├── sonarr/
│   ├── radarr/
│   ├── lidarr/
│   ├── prowlarr/
│   ├── bazarr/
│   ├── overseerr/
│   ├── sabnzbd/
│   └── qbittorrent/
└── data/               # Media and downloads
  ├── movies/         # Movie files
  ├── tv/            # TV show files
  ├── music/         # Music files
  ├── downloads/     # Download staging area
  └── scripts/       # Post-processing scripts (optional)
```

## Configuration

### Initial Setup Order
1. **Prowlarr**: Configure indexers first
2. **Download Clients**: Set up SABnzbd and/or qBittorrent
3. **Content Managers**: Configure Sonarr, Radarr, Lidarr
4. **Bazarr**: Link to Sonarr/Radarr for subtitles
5. **Overseerr**: Connect to Sonarr/Radarr for requests

### Volume Path Adjustments
Before deployment, update paths to match your system:
```yaml
volumes:
  - /your/config/path:/config
  - /your/data/path:/data
  - /your/downloads/path:/downloads
```

### User/Group Configuration
Adjust PUID/PGID values to match your system:
- `PUID=1000` - User ID for file ownership
- `PGID=1000` - Group ID for file ownership

## Network Access

All services are accessible via their respective web interfaces:
- Sonarr: http://localhost:8989
- Radarr: http://localhost:7878
- Lidarr: http://localhost:8686
- Prowlarr: http://localhost:9696
- Bazarr: http://localhost:6767
- Overseerr: http://localhost:5055
- SABnzbd: http://localhost:8080
- qBittorrent: http://localhost:8081

## Deployment

This stack can be deployed in Portainer as a single unit, creating an isolated network for all services to communicate with each other while exposing necessary ports to the host.