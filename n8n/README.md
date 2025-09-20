# n8n Workflow Automation Stack

This stack deploys n8n, a powerful workflow automation platform, with PostgreSQL database backend.

## Services

- **n8n**: Workflow automation platform
  - Port: 5678 (web interface)
- **PostgreSQL**: Database backend for n8n
  - Internal service (no exposed ports)

## Features

- Workflow automation and integration platform
- Web-based visual workflow editor
- PostgreSQL database for persistence
- Basic authentication enabled
- Support for custom nodes and functions
- Health monitoring for database
- Automatic restart policies

## Prerequisites

### System Requirements
- **Memory**: 2GB+ RAM recommended
- **Storage**: 5GB+ free space for application data and database
- **Network**: Port 5678 accessible for web interface

### Directory Structure
Choose one of the following volume configurations:

**Option 1: Docker Volumes (Default)**
- Uses Docker-managed volumes for automatic management

**Option 2: Bind Mounts**
Ensure these directories exist on your host:
- `/fileServerMountPath/data/n8n/postgres/` - PostgreSQL database files
- `/fileServerMountPath/data/n8n/data/` - n8n application data and workflows

## Configuration

### Required Changes Before Deployment

1. **Update Authentication**: Change the default credentials:
   ```yaml
   environment:
     - N8N_BASIC_AUTH_USER: your-username
     - N8N_BASIC_AUTH_PASSWORD: your-secure-password
   ```

2. **Update Database Password**: Change the default database password:
   ```yaml
   environment:
     - POSTGRES_PASSWORD: your-secure-db-password
     - DB_POSTGRESDB_PASSWORD: your-secure-db-password
   ```

3. **Update Encryption Key**: Change the encryption key for sensitive data:
   ```yaml
   environment:
     - N8N_ENCRYPTION_KEY: your-32-character-encryption-key
   ```

### Optional Configurations

- **Timezone**: Adjust timezone if needed:
  ```yaml
  environment:
    - GENERIC_TIMEZONE: your-timezone
  ```

- **Volume Configuration**: Uncomment bind mount volumes if preferred:
  ```yaml
  volumes:
    - /fileServerMountPath/data/n8n/postgres:/var/lib/postgresql/data
    - /fileServerMountPath/data/n8n/data:/home/node/.n8n
  ```

### User/Group Configuration
The service runs as user 1000:1000 following the repository pattern:
- `user: "1000:1000"` - User and group ID for file ownership

## Network Access

- n8n Web Interface: http://localhost:5678
- Login with the credentials configured in `N8N_BASIC_AUTH_USER` and `N8N_BASIC_AUTH_PASSWORD`

## Security Notes

- **Change default credentials**: Update basic auth username and password before deployment
- **Secure database password**: Use a strong password for PostgreSQL
- **Encryption key**: Use a secure 32-character encryption key for workflow data
- **Network access**: Consider restricting access to port 5678 if exposing to internet
- **Regular backups**: Essential for workflow and database data
- **Monitor access**: Review logs for unauthorized access attempts

## Deployment

Deploy this stack in Portainer using the Git repository method or by uploading the docker-compose.yml file directly. Ensure all security-sensitive variables are updated before deployment.

## Troubleshooting

### Common Issues

1. **Database connection failed**: Check PostgreSQL health status and credentials
2. **n8n won't start**: Verify database is healthy before n8n starts
3. **Authentication issues**: Verify basic auth credentials are correct
4. **Workflow execution errors**: Check node logs and function permissions

### Logs
```bash
# n8n application logs
docker logs n8n

# PostgreSQL database logs
docker logs postgres
```

### Database Management
```bash
# Access PostgreSQL console
docker exec -it postgres psql -U n8n -d n8n

# Database backup
docker exec postgres pg_dump -U n8n -d n8n > n8n-backup.sql

# Database restore
docker exec -i postgres psql -U n8n -d n8n < n8n-backup.sql
```

## Backup Strategy

### Manual Backup
```bash
# Stop services
docker-compose down

# Backup application data and database
cp -r /fileServerMountPath/data/n8n /backup/location/n8n-backup-$(date +%Y%m%d-%H%M%S)

# Or if using Docker volumes
docker run --rm -v n8n-data:/data -v n8n-postgres:/postgres -v $(pwd):/backup alpine tar czf /backup/n8n-backup-$(date +%Y%m%d-%H%M%S).tar.gz /data /postgres

# Start services
docker-compose up -d
```

### Automated Backup Script
```bash
#!/bin/bash
BACKUP_DIR="/backup/n8n"
DATE=$(date +%Y%m%d-%H%M%S)
mkdir -p "$BACKUP_DIR"

# Export database
docker exec postgres pg_dump -U n8n -d n8n > "$BACKUP_DIR/n8n-db-$DATE.sql"

# Backup application data (if using bind mounts)
cp -r /fileServerMountPath/data/n8n/data "$BACKUP_DIR/n8n-data-$DATE"

# Keep only last 7 backups
ls -t "$BACKUP_DIR" | tail -n +15 | xargs -I {} rm -rf "$BACKUP_DIR/{}"
```