# Satisfactory Dedicated Server Stack

This stack deploys a Satisfactory dedicated server for multiplayer factory building gameplay.

## Services

- **Satisfactory Server**: Dedicated server for Satisfactory multiplayer
  - Port: 7777/udp (Game connection)
  - Port: 15000/udp (Beacon/discovery)
  - Port: 15777/udp (Query port - optional)

## Features

- Latest Satisfactory dedicated server
- Automatic Steam updates
- Persistent save file storage
- Configurable player limits
- Auto-save functionality
- Health monitoring
- Easy backup and restore

## Prerequisites

### System Requirements
- **CPU**: 4+ cores recommended for smooth gameplay
- **Memory**: 8GB+ RAM recommended (depends on factory complexity)
- **Storage**: 20GB+ free space for game files and saves
- **Network**: Stable internet connection for multiplayer

### Directory Structure
Ensure this directory exists on your host:
- `/tank/containers/satisfactory/` - Server configuration, saves, and game files

### Network Requirements
- Ports 7777/UDP and 15000/UDP must be accessible
- For internet play, configure port forwarding on your router

## Configuration

### Player and Session Settings

```yaml
environment:
  - MAXPLAYERS=4                      # Maximum concurrent players (1-4)
  - TIMEOUT=30                        # Client timeout in seconds
  - MAXTICKRATE=30                    # Server tick rate (performance)
```

### Auto-Save Configuration

```yaml
environment:
  - AUTOSAVENUM=3                     # Number of auto-save files to keep
  - AUTOSAVEINTERVAL=300              # Auto-save interval in seconds (5 min)
  - AUTOSAVEONDISCONNECT=true         # Save when last player disconnects
```

### Update Settings

```yaml
environment:
  - SKIPUPDATE=false                  # Skip Steam updates on startup
  - STEAMBETA=false                   # Use Steam beta branch
```

### User/Group Configuration
Following the repository pattern:

```yaml
environment:
  - PUID=1000                         # User ID for file ownership
  - PGID=1000                         # Group ID for file ownership
  - TZ=Australia/Brisbane             # Timezone setting
```

## Server Management

### Starting a New Game
1. Deploy the stack
2. Wait for initial setup (downloads game files)
3. Connect via game client to create a new session
4. The server will auto-save progress

### Loading Existing Save
1. Stop the server
2. Copy your save file to `/tank/containers/satisfactory/saved/server/`
3. Start the server
4. Connect and select your save

### Save File Location
Save files are stored in:
```
/tank/containers/satisfactory/saved/server/
```

## Network Access

### Local Network
Players on the same network can:
1. Use in-game server browser (if beacon is working)
2. Connect directly via IP: `<server-local-ip>:7777`

### Internet Access
For internet multiplayer:
1. Configure port forwarding on router:
   - 7777/UDP → server IP
   - 15000/UDP → server IP
2. Share your public IP with friends
3. Players connect using: `<public-ip>:7777`

## Performance Tuning

### For Better Performance

```yaml
environment:
  - MAXTICKRATE=30                    # Lower for weaker hardware (20-25)
  - MAXPLAYERS=2                      # Reduce for better performance
```

### For Large Factories
Consider increasing container resources:
```yaml
deploy:
  resources:
    limits:
      memory: 16G
      cpus: '6'
```

## Health Monitoring

The stack includes health checks to monitor server status:
- Check interval: 30 seconds
- Timeout: 10 seconds
- Retries: 3 before marking unhealthy
- Start period: 120 seconds (initial download time)

## Backup Strategy

### Manual Backup
```bash
# Stop the server gracefully
docker stop satisfactory
# Backup save files
cp -r /tank/containers/satisfactory/saved /backup/location/satisfactory-backup-$(date +%Y%m%d-%H%M%S)
# Start the server
docker start satisfactory
```

### Automated Backup Script
Create a backup script for regular saves:
```bash
#!/bin/bash
BACKUP_DIR="/backup/satisfactory"
DATE=$(date +%Y%m%d-%H%M%S)
mkdir -p "$BACKUP_DIR"
cp -r /tank/containers/satisfactory/saved "$BACKUP_DIR/satisfactory-$DATE"
# Keep only last 7 backups
ls -t "$BACKUP_DIR" | tail -n +8 | xargs -I {} rm -rf "$BACKUP_DIR/{}"
```

## Deployment

Deploy this stack in Portainer using the Git repository method or by uploading the docker-compose.yml file directly. Allow extra time for initial deployment as the server needs to download game files.

## Troubleshooting

### Server Won't Start
1. Check available disk space (game files are ~15GB)
2. Verify network connectivity for Steam downloads
3. Check container logs for errors
4. Ensure ports aren't in use by other services

### Connection Issues
1. **Can't find server**: Check beacon port (15000/UDP) accessibility
2. **Can't connect directly**: Verify game port (7777/UDP) is open
3. **Timeout on join**: Check TIMEOUT setting and network stability
4. **Friends can't connect**: Verify port forwarding configuration

### Performance Issues
1. **Server lag**: Reduce MAXTICKRATE or MAXPLAYERS
2. **Memory issues**: Increase container memory limits
3. **Storage issues**: Ensure sufficient disk space
4. **Network lag**: Check bandwidth and connection stability

### Save File Issues
1. **Save not loading**: Check file permissions and ownership
2. **Corrupted save**: Restore from backup
3. **Save not found**: Verify file location and naming

### Logs and Diagnostics
```bash
# Server logs
docker logs satisfactory

# Health check status
docker inspect satisfactory | grep -A 5 Health

# Resource usage
docker stats satisfactory
```

## Game Client Connection

### In-Game Server Browser
1. Open Satisfactory
2. Go to "Multiplayer" → "Server Manager"
3. Look for your server in the list
4. Join the session

### Direct Connection
1. Open Satisfactory
2. Go to "Multiplayer" → "Server Manager"
3. Click "Add Server"
4. Enter server IP and port (7777)
5. Connect to server

## Security Notes

- Satisfactory doesn't have built-in authentication
- Anyone with your server IP can potentially join
- Consider using VPN or firewall rules for private servers
- Regular backups are essential as there's no rollback feature
- Monitor server logs for unusual activity

## Mods and Customization

The dedicated server supports mods through the Satisfactory Mod Manager:
1. Install mods in the client
2. Upload mod files to server
3. All players need the same mods installed
4. Refer to Satisfactory modding community for specific instructions