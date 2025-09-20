# Plex Media Server Stack

This stack deploys a Plex Media Server with NVIDIA GPU acceleration support.

## Services

- **Plex Media Server**: Full-featured media server
  - Port: 32400 (main web interface)
  - Port: 32469 (DLNA, optional)

## Features

- NVIDIA GPU hardware acceleration for transcoding
- LinuxServer.io image for easy configuration
- Automatic restart policy
- Pre-configured volume mounts for media access

## Prerequisites

1. **NVIDIA GPU Support**: Install NVIDIA Container Toolkit on the host
2. **Directory Structure**: Ensure these directories exist:
   - `/tank/containers/plex/` - Plex configuration
   - `/tank/data/movies/` - Movie files
   - `/tank/data/tv/` - TV show files
   - `/tank/data/downloads/complete/` - Downloaded media

## Configuration

### Required Changes Before Deployment

1. **Update Plex Claim Token**: 
   - Get a new claim token from https://plex.tv/claim
   - Replace `claim-269yU8bN9o6uioMrs8Yc` in the compose file

2. **Adjust Volume Paths**: Update paths to match your system:
   ```yaml
   volumes:
     - /your/config/path:/config
     - /your/movies/path:/data/movies
     - /your/tv/path:/data/tv
     - /your/downloads/path:/data/downloads
   ```

3. **Set User/Group IDs**: Adjust PUID/PGID to match your system users

## GPU Acceleration

This configuration enables NVIDIA GPU transcoding with:
- `runtime: nvidia` - Uses NVIDIA container runtime
- `NVIDIA_VISIBLE_DEVICES=all` - Makes all GPUs available
- `NVIDIA_DRIVER_CAPABILITIES=compute,video,utility` - Required capabilities

## Deployment

Deploy this stack in Portainer using the Git repository method or by uploading the docker-compose.yml file directly.