# Plex Media Server Stack

This stack deploys a Plex Media Server with optional NVIDIA GPU acceleration.

## Services

- Plex Media Server (linuxserver/plex)
  - Port: 32400 (web UI)
  - Port: 32469 (DLNA)

## Features

- NVIDIA GPU transcoding (optional)
- Persistent config and media libraries
- Easy updates with LinuxServer image

## Prerequisites

1. NVIDIA Container Toolkit installed for GPU transcoding (optional)
2. Ensure directories exist:
  - `/fileServerMountPath/containers/plex/` (config)
  - `/fileServerMountPath/data/movies/` (movies)
  - `/fileServerMountPath/data/tv/` (TV)
  - `/fileServerMountPath/data/downloads/complete/` (downloads)

Set `/fileServerMountPath` to your Linux host path. On Docker Desktop with WSL, use `/mnt/<drive>` (e.g., `/mnt/d`). Using local mounts simplifies RBAC.

## Configuration

1. Plex claim token: get from https://plex.tv/claim and set PLEX_CLAIM
2. Update volume paths if needed
3. Set PUID/PGID to match host user

## GPU Acceleration

Enabled via:
- `runtime: nvidia`
- `NVIDIA_VISIBLE_DEVICES=all`
- `NVIDIA_DRIVER_CAPABILITIES=compute,video,utility`

## Operations

- Start: `docker compose up -d`
- Stop: `docker compose down`
- Update: `docker compose pull && docker compose up -d`
- Logs: `docker logs plex`

## Links

- Docker Hub: https://hub.docker.com/r/linuxserver/plex
- GitHub: https://github.com/linuxserver/docker-plex