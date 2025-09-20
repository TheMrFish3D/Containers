# Servarr Media Automation Stack

This stack provides a complete media automation solution for managing movies, TV shows, music, and downloads.

## Services

### Content Management
- **Sonarr** (Port 8989): TV show collection manager
- **Radarr** (Port 7878): Movie collection manager  
- **Lidarr** (Port 8686): Music collection manager
- **Bazarr** (Port 6767): Subtitle management for Sonarr/Radarr

### Download Clients
- **SABnzbd** (Port 8080): Usenet/NZB downloader
- **qBittorrent** (Port 8081): BitTorrent client

### Additional Services
- **Prowlarr** (Port 9696): Indexer manager for Sonarr/Radarr/Lidarr
- **Overseerr** (Port 5055): Request management interface

## Prerequisites

### Directory Structure
Ensure these directories exist on your host:
```
/tank/
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