# Container Stacks for Portainer Deployment

This repository contains two Docker Compose stacks optimized for deployment via Portainer:

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

## Portainer Deployment

### Method 1: Git Repository Deployment
1. In Portainer, go to **Stacks** → **Add stack**
2. Choose **Repository** as the build method
3. Enter this repository URL: `https://github.com/TheMrFish3D/Containers`
4. Set the **Compose path** to either:
   - `plex/docker-compose.yml` for Plex only
   - `servarr-stack/docker-compose.yml` for the full media stack
5. Configure environment variables if needed
6. Deploy the stack

### Method 2: Upload Compose File
1. In Portainer, go to **Stacks** → **Add stack**
2. Choose **Upload** as the build method
3. Upload the desired `docker-compose.yml` file from either the `plex/` or `servarr-stack/` directory
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
- `/tank/data/` - Media storage
- `/tank/data/movies/` - Movie files
- `/tank/data/tv/` - TV show files
- `/tank/data/downloads/` - Download storage

### NVIDIA GPU Support (Plex only)
For GPU transcoding in Plex:
1. Install NVIDIA Container Toolkit on the host
2. Ensure the `nvidia` runtime is configured in Docker
3. Update the `PLEX_CLAIM` environment variable with your claim token

## Configuration

### Environment Variables
Both stacks use the following common environment variables:
- `PUID=1000` - User ID for file permissions
- `PGID=1000` - Group ID for file permissions  
- `TZ=Australia/Brisbane` - Timezone setting

### Volumes
Update the volume paths in the compose files to match your host system structure before deployment.

## Security Notes
- The Plex configuration includes a claim token that should be updated
- Consider using Docker secrets or Portainer environment variables for sensitive data
- Ensure proper network segmentation if deploying in production

## Support
For issues with individual applications, refer to their respective documentation:
- [LinuxServer.io Documentation](https://docs.linuxserver.io/)
- [Portainer Documentation](https://docs.portainer.io/)