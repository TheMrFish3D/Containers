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

### 7. n8n Workflow Automation (`n8n/`)
Workflow automation platform with PostgreSQL database backend.

**Services:**
- n8n Workflow Engine (Port: 5678)
- PostgreSQL Database (Internal)

**Features:**
- Visual workflow editor
- Web-based automation platform
- PostgreSQL persistence
- Basic authentication
- Custom node support

### 8. NVIDIA Triton Inference Server (`triton-inference-server/`)
AI model serving platform for high-performance inference.

**Services:**
- Triton Server (Port: 8000 REST, 8001 gRPC, 8002 Metrics)

**Features:**
- Multi-framework AI model serving
- GPU acceleration with NVIDIA runtime
- Dynamic model loading
- REST and gRPC APIs
- Prometheus metrics

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
   - `n8n/docker-compose.yml` for n8n Workflow Automation
   - `triton-inference-server/docker-compose.yml` for NVIDIA Triton Inference Server
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
Ensure the following directories exist on your host system (replace `/fileServerMountPath` with your Linux host mount root; on Docker Desktop with WSL this is typically under `/mnt/<drive>`, e.g., `/mnt/d`). Using locally mounted paths simplifies RBAC/permissions across containers:
- `/fileServerMountPath/containers/` - Application configuration storage
- `/fileServerMountPath/data/` - Media storage (for media stacks)
- `/fileServerMountPath/data/movies/` - Movie files (for Plex/Servarr)
- `/fileServerMountPath/data/tv/` - TV show files (for Plex/Servarr)
- `/fileServerMountPath/data/downloads/` - Download storage (for Servarr)
- `/fileServerMountPath/data/models/` - AI model repository (for Triton)
- `/fileServerMountPath/data/n8n/` - n8n application and database data (optional, can use Docker volumes)

### Additional Requirements by Stack
- **Tailscale**: Requires `tun` kernel module and IP forwarding enabled
- **Minecraft Servers**: Require sufficient RAM allocation and port forwarding for internet access
- **Satisfactory**: Requires 8GB+ RAM and stable internet connection
- **Plex**: Requires NVIDIA Container Toolkit for GPU acceleration
- **n8n**: Requires PostgreSQL database (included in stack)
- **Triton Inference Server**: Requires NVIDIA GPU and NVIDIA Container Toolkit

### NVIDIA GPU Support (Plex and Triton)
For GPU acceleration in Plex and AI inference in Triton:
1. Install NVIDIA Container Toolkit on the host
2. Ensure the `nvidia` runtime is configured in Docker
3. Update the `PLEX_CLAIM` environment variable with your claim token (Plex only)
4. Prepare model repository at `/fileServerMountPath/data/models/` (Triton only)

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
- **n8n**: Basic auth credentials, database password, and encryption key

### Volumes
Update the volume paths in the compose files to match your host system structure before deployment.

## Security Notes
- Update security-sensitive variables before deployment:
  - Plex claim token should be updated
  - Tailscale auth key should be generated fresh
  - Minecraft RCON password should be changed from default
  - n8n basic auth credentials should be changed from defaults
  - n8n database password should be changed from default
  - n8n encryption key should be set to a secure 32-character string
- Consider using Docker secrets or Portainer environment variables for sensitive data
- Ensure proper network segmentation if deploying in production
- Regular backups are essential for game servers and workflow data
- Monitor logs for unauthorized access attempts
- For Triton Inference Server, secure access to model repository and API endpoints

## Support
For issues with individual applications, refer to their respective documentation:
- [LinuxServer.io Documentation](https://docs.linuxserver.io/)
- [Portainer Documentation](https://docs.portainer.io/)
- [n8n Documentation](https://docs.n8n.io/)
- [NVIDIA Triton Documentation](https://docs.nvidia.com/deeplearning/triton-inference-server/)