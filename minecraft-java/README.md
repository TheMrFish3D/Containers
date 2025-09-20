# Minecraft Java Server Stack

This stack deploys a Minecraft Java Edition server with extensive configuration options and mod support.

## Services

  - **Minecraft Java Server**: Provides multiplayer Minecraft Java Edition gameplay.
  - Port: 25565 (Minecraft default)
  - Port: 25575 (RCON - optional)

# Minecraft Java Server

Runs a Java edition Minecraft server using `itzg/minecraft-server`.

## Quick start

1) Set `EULA=TRUE` and adjust TYPE/VERSION/MEMORY as needed in `docker-compose.yml`.
2) Start: `docker compose up -d`
3) Stop: `docker compose down`
4) Update: `docker compose pull && docker compose up -d`

## Ports

- 25565/tcp -> Minecraft server
- 25575/tcp -> RCON

## Volumes

- `/fileServerMountPath/containers/minecraft-java` -> `/data`

## Links
- Docker Hub: https://hub.docker.com/r/itzg/minecraft-server
- GitHub: https://github.com/itzg/docker-minecraft-server

## Features

- Latest Minecraft Java Edition
- Support for Vanilla, Forge, Fabric, Paper, Spigot, and more
- Persistent world storage
- RCON remote administration
- Extensive configuration options
- Plugin/mod support (depending on server type)
- Automatic server updates
- Automatic server updates

## Prerequisites

### Directory Structure
Ensure this directory exists on your host:
- `/fileServerMountPath/containers/minecraft-java/` - Server data, worlds, plugins, and configuration

### System Requirements
- **Memory**: At least 2GB RAM allocated (configurable via MEMORY environment variable)
- **Network**: Port 25565 accessible for players

## Configuration

### Server Type and Version
Choose your server type and version:

```yaml
environment:
  - TYPE=VANILLA                       # VANILLA, FORGE, FABRIC, PAPER, SPIGOT, etc.
  - VERSION=LATEST                     # LATEST, 1.20.1, 1.19.4, etc.
  - MEMORY=4G                          # Allocated memory (2G, 4G, 8G, etc.)
```

### Game Settings
Configure core gameplay options:

```yaml
environment:
  - DIFFICULTY=normal                  # peaceful, easy, normal, hard
  - GAMEMODE=survival                  # survival, creative, adventure, spectator
  - MAX_PLAYERS=20                     # Maximum concurrent players
  - HARDCORE=false                     # Enable hardcore mode
  - ONLINE_MODE=true                   # Require Mojang authentication
  - ENABLE_WHITELIST=false             # Enable player whitelist
  - VIEW_DISTANCE=10                   # Render distance (chunks)
  - SPAWN_PROTECTION=16                # Spawn protection radius
```

### Server Information
Customize server appearance:

```yaml
environment:
  - MOTD=My Awesome Minecraft Server   # Message of the Day
  - LEVEL_NAME=world                   # World/level name
  - LEVEL_SEED=12345                   # World generation seed (optional)
```

### Advanced Settings
Performance and feature toggles:

```yaml
environment:
  - MAX_TICK_TIME=60000               # Server watchdog timeout
  - ENABLE_COMMAND_BLOCK=true         # Enable command blocks
  - FORCE_GAMEMODE=false              # Force gamemode on join
  - MAX_BUILD_HEIGHT=256              # Maximum build height
```

## Mod/Plugin Support

### Forge Server
```yaml
environment:
  - TYPE=FORGE
  - FORGE_VERSION=47.2.0              # Specific Forge version
```

### Fabric Server
```yaml
environment:
  - TYPE=FABRIC
  - FABRIC_LOADER_VERSION=0.14.21     # Specific Fabric Loader version
```

### Paper Server (Bukkit/Spigot plugins)
```yaml
environment:
  - TYPE=PAPER
  - PAPER_DOWNLOAD_URL=https://api.papermc.io/v2/projects/paper/versions/1.20.1/builds/196/downloads/paper-1.20.1-196.jar
```

## Remote Administration (RCON)

RCON is enabled by default for remote server management:

```yaml
environment:
  - ENABLE_RCON=true
  - RCON_PASSWORD=your-secure-password
  - RCON_PORT=25575
```

### Using RCON
Connect using any RCON client or the built-in tool:
```bash
docker exec -i minecraft-java rcon-cli
```

Common RCON commands:
- `list` - Show online players
- `op <player>` - Make player an operator
- `whitelist add <player>` - Add to whitelist
- `save-all` - Save the world
- `stop` - Stop server gracefully

## Network Access

### Local Network
Players connect using: `<server-ip>:25565`

### Internet Access
1. Configure port forwarding: 25565/TCP → server IP
2. Players connect using: `<public-ip>:25565`
3. Consider using a custom domain name

## World Management

### Backups
Regular world backups are essential:
```bash
# Save the world first
docker exec minecraft-java rcon-cli save-all
docker exec minecraft-java rcon-cli save-off
# Copy world data
cp -r /fileServerMountPath/containers/minecraft-java/world /backup/location/
# Resume saving
docker exec minecraft-java rcon-cli save-on
```

### World Import
To import an existing world:
1. Stop the server
2. Copy world files to `/fileServerMountPath/containers/minecraft-java/`
3. Update `LEVEL_NAME` if needed
4. Start the server

## Plugin/Mod Installation

### Plugins (Paper/Spigot)
1. Download plugins (.jar files)
2. Copy to `/fileServerMountPath/containers/minecraft-java/plugins/`
3. Restart server

### Mods (Forge/Fabric)
1. Download mods (.jar files)
2. Copy to `/fileServerMountPath/containers/minecraft-java/mods/`
3. Restart server

## Performance Optimization

### Memory Allocation
Adjust memory based on your needs:
```yaml
environment:
  - MEMORY=4G                         # For 10-15 players
  - MEMORY=8G                         # For 20+ players
  - MEMORY=16G                        # For large servers/heavy mods
```

### JVM Options
For advanced tuning:
```yaml
environment:
  - JVM_OPTS=-XX:+UseG1GC -XX:+ParallelRefProcEnabled -XX:MaxGCPauseMillis=200
```

## Deployment

Deploy this stack in Portainer using the Git repository method or by uploading the docker-compose.yml file directly.

## Troubleshooting

### Server Won't Start
1. Check EULA acceptance
2. Verify sufficient memory allocation
3. Check for port conflicts
4. Review server logs

### Connection Issues
1. Verify port forwarding
2. Check firewall settings
3. Confirm ONLINE_MODE setting
4. Test local connectivity first

### Performance Issues
1. Increase memory allocation
2. Reduce view distance
3. Optimize JVM settings
4. Remove unnecessary plugins/mods

### Logs
```bash
docker logs minecraft-java
```

## Server Console

Access the interactive console:
```bash
docker attach minecraft-java
```

Press `Ctrl+P, Ctrl+Q` to detach without stopping the server.

## Security Notes

- Change the default RCON password
- Use whitelist for private servers
- Keep server software updated
- Regular backups are essential
- Monitor server logs for issues