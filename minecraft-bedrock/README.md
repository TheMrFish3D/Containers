# Minecraft Bedrock Server Stack

This stack deploys a Minecraft Bedrock Edition server for cross-platform multiplayer gaming.

## Minecraft Bedrock Server
Runs a Bedrock edition Minecraft server using `itzg/minecraft-bedrock-server`.

## Quick start

1) Set `EULA=TRUE` and adjust environment variables in `docker-compose.yml`.
2) Start: `docker compose up -d`
3) Stop: `docker compose down`
4) Update: `docker compose pull && docker compose up -d`

## Ports

- 19132/udp -> Bedrock server

## Volumes

- `/fileServerMountPath/containers/minecraft-bedrock` -> `/data`

## Links

- Docker Hub: https://hub.docker.com/r/itzg/minecraft-bedrock-server
- GitHub: https://github.com/itzg/docker-minecraft-bedrock-server


- **Minecraft Bedrock Server**: Cross-platform Minecraft server
  - Port: 19132/udp (Bedrock default)

## Features

- Latest Minecraft Bedrock Edition
- Cross-platform compatibility (mobile, console, Windows 10)
- Persistent world storage
- Configurable server settings
- Automatic server updates
- Interactive console access

## Prerequisites

### Directory Structure
Ensure this directory exists on your host:
- `/fileServerMountPath/containers/minecraft-bedrock/` - Server data, worlds, and configuration

### Network Requirements
- Port 19132/UDP must be accessible for players to connect
- For internet access, configure port forwarding on your router

## Configuration

### Server Settings
The server is pre-configured with sensible defaults. Key settings you may want to adjust:

```yaml
environment:
  - SERVER_NAME=Your Server Name        # Server name shown in server list
  - GAMEMODE=survival                   # survival, creative, adventure
  - DIFFICULTY=easy                     # peaceful, easy, normal, hard
  - MAX_PLAYERS=10                     # Maximum concurrent players
  - ALLOW_CHEATS=false                 # Enable/disable cheats
  - ONLINE_MODE=true                   # Require Xbox Live authentication
  - WHITE_LIST=false                   # Enable whitelist (requires setup)
  - VIEW_DISTANCE=32                   # Render distance (chunks)
  - LEVEL_SEED=12345                   # World generation seed (optional)
```

### World Management
- **World Persistence**: Worlds are stored in `/fileServerMountPath/containers/minecraft-bedrock/worlds/`
- **Backups**: Regular backups of the worlds directory are recommended
- **World Name**: Set via `LEVEL_NAME` environment variable

### Player Management
- **Whitelist**: Enable `WHITE_LIST=true` and manage via server console
- **Operators**: Add ops via server console: `op <playername>`
- **Permissions**: Control via `DEFAULT_PLAYER_PERMISSION_LEVEL`

## Network Access

### Local Network
Players on the same network can connect using:
- IP: `<server-local-ip>:19132`

### Internet Access
For internet access:
1. Configure port forwarding on router (19132/UDP → server IP)
2. Players connect using: `<public-ip>:19132`
3. Consider using DDNS for dynamic IPs

## Server Console

Access the interactive server console:
```bash
docker attach minecraft-bedrock
```

Common console commands:
- `list` - Show online players
- `op <player>` - Make player an operator
- `whitelist add <player>` - Add player to whitelist
- `save hold` - Pause world saving
- `save resume` - Resume world saving
- `stop` - Stop server gracefully

Press `Ctrl+P, Ctrl+Q` to detach from console without stopping the server.

## Performance Tuning

For better performance on larger servers:

```yaml
environment:
  - MAX_THREADS=8                      # Adjust based on CPU cores
  - TICK_DISTANCE=6                    # Higher = more CPU usage
  - VIEW_DISTANCE=16                   # Lower = better performance
  - PLAYER_IDLE_TIMEOUT=30             # Kick idle players
```

## Deployment

Deploy this stack in Portainer using the Git repository method or by uploading the docker-compose.yml file directly.

## Troubleshooting

### Connection Issues
1. **Can't connect locally**: Check firewall and port availability
2. **Can't connect from internet**: Verify port forwarding and public IP
3. **Authentication errors**: Check `ONLINE_MODE` setting

### Performance Issues
1. **Lag**: Reduce `VIEW_DISTANCE` and `TICK_DISTANCE`
2. **Memory**: Consider adding memory limits to container
3. **CPU**: Adjust `MAX_THREADS` based on available cores

### Logs
```bash
docker logs minecraft-bedrock
```

## Backup Strategy

Regular backups are essential:
```bash
# Stop the server gracefully
docker exec minecraft-bedrock rcon-cli save hold
docker exec minecraft-bedrock rcon-cli save query
# Copy world data
cp -r /fileServerMountPath/containers/minecraft-bedrock/worlds /backup/location/
# Resume saving
docker exec minecraft-bedrock rcon-cli save resume
```