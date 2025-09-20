# Container Stacks for Portainer Deployment

This repository contains Docker Compose stacks optimized for deployment via Portainer:

## Stacks

### 1. Plex Media Server (`plex/`)
A standalone Plex Media Server with GPU acceleration support.

**Services:**
- Plex Media Server (Port: 32400)

**Features:**
- NVIDIA GPU acceleration enabled
- LinuxServer.io image for easy configuration
- Configured for Brisbane timezone

### 2. Servarr Media Automation Stack (`servarr-stack/`)
A complete media automation solution including downloaders and content management.

**Services:**
- Sonarr (TV Shows) - Port: 8989
- Radarr (Movies) - Port: 7878  
- Lidarr (Music) - Port: 8686
- Prowlarr (Indexer Manager) - Port: 9696
- Bazarr (Subtitles) - Port: 6767
- Overseerr (Request Management) - Port: 5055
- SABnzbd (Usenet Downloader) - Port: 8080
- qBittorrent (Torrent Client) - Port: 8081

### 3. Tailscale VPN (`tailscale/`)
A VPN solution for secure remote access to your network.

**Services:**
- Tailscale Client/Server (Host network mode)

**Features:**
- Subnet routing for local network access
- Exit node capability
- Persistent state storage
- DNS forwarding support

### 4. Minecraft Bedrock Server (`minecraft-bedrock/`)
Cross-platform Minecraft Bedrock Edition server.

**Services:**
- Minecraft Bedrock Server (Port: 19132/udp)

**Features:**
- Cross-platform compatibility (mobile, console, Windows 10)
- Persistent world storage
- Configurable server settings
- Interactive console access

### 5. Minecraft Java Server (`minecraft-java/`)
Full-featured Minecraft Java Edition server with mod support.

**Services:**
- Minecraft Java Server (Port: 25565)
- RCON Remote Admin (Port: 25575)

**Features:**
- Support for Vanilla, Forge, Fabric, Paper, Spigot
- Plugin/mod support
- RCON remote administration
- Extensive configuration options

### 6. Satisfactory Dedicated Server (`satisfactory/`)
Dedicated server for Satisfactory multiplayer factory building.

**Services:**
- Satisfactory Server (Port: 7777/udp)
- Beacon/Discovery (Port: 15000/udp)

**Features:**
- Multiplayer factory building
- Auto-save functionality
- Health monitoring
- Persistent save files

## Portainer Deployment

### Method 1: Git Repository Deployment
1. In Portainer, go to **Stacks** → **Add stack**
2. Choose **Repository** as the build method
3. Enter this repository URL: `https://github.com/TheMrFish3D/Containers`
4. Set the **Compose path** to one of:
   - `plex/docker-compose.yml` for Plex Media Server
   - `servarr-stack/docker-compose.yml` for the full media automation stack
   - `tailscale/docker-compose.yml` for Tailscale VPN
   - `minecraft-bedrock/docker-compose.yml` for Minecraft Bedrock Server
   - `minecraft-java/docker-compose.yml` for Minecraft Java Server
   - `satisfactory/docker-compose.yml` for Satisfactory Dedicated Server
5. Configure environment variables if needed
6. Deploy the stack

### Method 2: Upload Compose File
1. In Portainer, go to **Stacks** → **Add stack**
2. Choose **Upload** as the build method
3. Upload the desired `docker-compose.yml` file from any of the stack directories
4. Configure the stack settings
5. Deploy the stack

### Method 3: Git Repository with Auto-update
For automated deployments with git webhooks:
1. Use Method 1 above
2. Enable **GitOps updates** in the stack settings
3. Configure a webhook in this repository to trigger updates on git push

## Prerequisites

### Storage Requirements
Ensure the following directories exist on your host system:
- `/tank/containers/` - Application configuration storage
- `/tank/data/` - Media storage (for media stacks)
- `/tank/data/movies/` - Movie files (for Plex/Servarr)
- `/tank/data/tv/` - TV show files (for Plex/Servarr)
- `/tank/data/downloads/` - Download storage (for Servarr)

### Additional Requirements by Stack
- **Tailscale**: Requires `tun` kernel module and IP forwarding enabled
- **Minecraft Servers**: Require sufficient RAM allocation and port forwarding for internet access
- **Satisfactory**: Requires 8GB+ RAM and stable internet connection
- **Plex**: Requires NVIDIA Container Toolkit for GPU acceleration

### NVIDIA GPU Support (Plex only)
For GPU transcoding in Plex:
1. Install NVIDIA Container Toolkit on the host
2. Ensure the `nvidia` runtime is configured in Docker
3. Update the `PLEX_CLAIM` environment variable with your claim token

## Configuration

### Environment Variables
Most stacks use the following common environment variables:
- `PUID=1000` - User ID for file permissions
- `PGID=1000` - Group ID for file permissions  
- `TZ=Australia/Brisbane` - Timezone setting

### Security-Sensitive Variables
Some stacks require secure configuration:
- **Tailscale**: Auth key from Tailscale admin console
- **Plex**: Claim token from plex.tv/claim
- **Minecraft Java**: RCON password for remote administration

### Volumes
Update the volume paths in the compose files to match your host system structure before deployment.

## Security Notes
- Update security-sensitive variables before deployment:
  - Plex claim token should be updated
  - Tailscale auth key should be generated fresh
  - Minecraft RCON password should be changed from default
- Consider using Docker secrets or Portainer environment variables for sensitive data
- Ensure proper network segmentation if deploying in production
- Regular backups are essential for game servers
- Monitor logs for unauthorized access attempts

## Support
For issues with individual applications, refer to their respective documentation:
- [LinuxServer.io Documentation](https://docs.linuxserver.io/)
- [Portainer Documentation](https://docs.portainer.io/)