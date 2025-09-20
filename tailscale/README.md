# Tailscale VPN Stack

This stack deploys Tailscale as a subnet router and exit node for secure remote access to your network.

## Services

- **Tailscale**: VPN client/server for secure remote access
  - Network Mode: Host (required for subnet routing)

## Features

- Subnet routing for local network access
- Exit node capability
- Persistent state storage
- Automatic reconnection
- DNS forwarding support

## Prerequisites

### Host Requirements
1. **Kernel modules**: Ensure `tun` module is loaded:
   ```bash
   sudo modprobe tun
   echo 'tun' | sudo tee -a /etc/modules
   ```

2. **Directory Structure**: Ensure this directory exists:
   - `/tank/containers/tailscale/` - Tailscale state and configuration

3. **IP Forwarding**: Verify IP forwarding is enabled:
   ```bash
   # Check current setting
   cat /proc/sys/net/ipv4/ip_forward
   # Should return 1, if not, enable it:
   echo 'net.ipv4.ip_forward=1' | sudo tee -a /etc/sysctl.conf
   sudo sysctl -p
   ```

## Configuration

### Required Changes Before Deployment

1. **Update Auth Key**: 
   - Go to https://login.tailscale.com/admin/settings/keys
   - Generate a new auth key with appropriate permissions
   - Replace `tskey-auth-XXXXXXXXXXXXXXXXX-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` in the compose file

2. **Configure Subnet Routes**: Update the `TS_ROUTES` environment variable to match your network:
   ```yaml
   environment:
     - TS_ROUTES=192.168.1.0/24,10.0.0.0/8
   ```

3. **Set Hostname**: Adjust the hostname to identify your server:
   ```yaml
   hostname: your-server-name
   ```

### Optional Configurations

- **Exit Node**: To enable as exit node, add:
  ```yaml
  environment:
    - TS_EXTRA_ARGS=--advertise-exit-node
  ```

- **SSH Server**: To enable Tailscale SSH:
  ```yaml
  environment:
    - TS_EXTRA_ARGS=--ssh
  ```

## Network Access

- Tailscale operates on the host network for optimal performance
- Access your services through Tailscale IPs from any connected device
- No specific ports exposed as it uses the host network directly

## Security Notes

- Store your auth key securely (consider using Portainer secrets)
- Use least-privilege auth keys when possible
- Regularly rotate auth keys
- Monitor connected devices in Tailscale admin console

## Deployment

Deploy this stack in Portainer using the Git repository method or by uploading the docker-compose.yml file directly. Ensure the host system meets all prerequisites before deployment.

## Troubleshooting

### Common Issues

1. **Container won't start**: Check if `/dev/net/tun` exists and `tun` module is loaded
2. **No connectivity**: Verify IP forwarding is enabled on the host
3. **DNS issues**: Check `TS_ACCEPT_DNS` setting and local DNS configuration

### Logs
```bash
docker logs tailscale
```