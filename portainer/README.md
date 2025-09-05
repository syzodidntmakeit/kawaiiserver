# Portainer on KawaiiServer

A powerful, web-based Docker management interface powered by Portainer and secured through Cloudflare Tunnel. Manage your Docker containers with a beautiful, intuitive web UI!

## ✨ What This Achieves

- **Visual Docker Management**: Manage containers, images, networks, and volumes through a web interface
- **Zero-Open Ports**: Uses Cloudflare Tunnel for secure external access without exposing your server to the internet
- **Multi-Environment Support**: Manage multiple Docker environments from a single interface
- **Kawaii Control Center**: Beautiful UI for managing your adorable server ecosystem

## 🚀 Prerequisites

Before you begin, ensure your KawaiiServer has:

1. **Docker & Docker Compose** installed
2. **Cloudflare Tunnel** configured and running
3. **Domain** pointed to Cloudflare (with proxy enabled)
4. **Docker Network** created: `docker network create kawaiinet`

## 📁 File Structure

```
kawaiiserver/
└── portainer/
    ├── docker-compose.yml    # Main deployment file
    ├── portainer-data/       # Portainer data directory
    └── README.md            # This file
```

## 🛠️ Setup Instructions

### 1. Create the Directory Structure
```bash
mkdir -p ~/kawaiiserver/portainer
cd ~/kawaiiserver/portainer
```

### 2. Create the [Docker Compose File](./docker-compose.yml)
```bash
nano docker-compose.yml
```

Paste the following configuration:

```yaml
version: '3'

services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9006:9000"  # Portainer runs on port 9000 internally
    networks:
      - kawaiinet
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock  # Docker socket for management
      - ./portainer-data:/data                     # Portainer data persistence
    environment:
      - PORTAINER_SSL=true
      - PORTAINER_HTTP_DISABLE=true

networks:
  kawaiinet:
    external: true
```

### 3. Deploy Portainer
```bash
docker compose up -d
```

Verify it's running:
```bash
docker ps
curl -I http://localhost:9006
```

### 4. Configure Cloudflare Tunnel

Ensure your `/etc/cloudflared/config.yml` includes:

```yaml
ingress:
  - hostname: portainer.kawaii-san.org    # Portainer
    service: http://localhost:9006        # Matches your compose port
  - service: http_status:404
```

### 5. Create DNS Record
```bash
sudo cloudflared tunnel route dns kawaiinet portainer.kawaii-san.org
```

### 6. Restart Cloudflare Tunnel
```bash
sudo systemctl restart cloudflared
```

## 🔧 Initial Setup

1. **First Access**: Visit `https://portainer.kawaii-san.org`
2. **Create Admin Account**: 
   - Set a strong username and password
   - This will be your main admin account for Portainer
3. **Connect to Docker**: 
   - Portainer will automatically detect the local Docker environment
   - Click "Get Started" to begin managing your containers

## 🔧 Configuration Options

### Environment Variables (Optional)

Create a `.env` file for easier management:

```env
PORTAINER_SSL=true
PORTAINER_HTTP_DISABLE=true
TZ=UTC
```

### Volume Persistence

Your Portainer data is stored in the `portainer-data` directory. To backup:

```bash
# Backup portainer data
tar czf portainer-backup-$(date +%Y%m%d).tar.gz portainer-data/
```

## 🌐 Access Points

- **Web Interface**: `https://portainer.kawaii-san.org`
- **Local Access**: `http://localhost:9006` (for troubleshooting)

## 🚨 Common Issues & Solutions

### ❌ "502 Bad Gateway" Error
**Cause**: Portainer isn't running or Cloudflare Tunnel can't connect
**Fix**:
```bash
# Check if Portainer is running
docker ps

# Check logs
docker logs portainer

# Test local access
curl http://localhost:9006
```

### ❌ "Cannot connect to Docker" Error
**Cause**: Docker socket permission issues
**Fix**:
```bash
# Add your user to the docker group
sudo usermod -aG docker $USER

# Re-login or restart docker service
sudo systemctl restart docker
```

### ❌ "Invalid origin certificate" Error
**Cause**: Cloudflare Tunnel certificate issues
**Fix**:
```bash
# Re-authenticate (if needed)
sudo cloudflared tunnel login

# Ensure certificate is accessible
sudo cp /root/.cloudflared/cert.pem ~/.cloudflared/
sudo chown $USER:$USER ~/.cloudflared/cert.pem
```

### ❌ "Access Denied" to Docker Socket
**Cause**: Permission issues with Docker socket
**Fix**:
```bash
# Check socket permissions
ls -la /var/run/docker.sock

# Fix permissions (temporary)
sudo chmod 666 /var/run/docker.sock

# Permanent fix: add user to docker group
sudo usermod -aG docker $USER
```

## 🔄 Maintenance

### Update Portainer
```bash
cd ~/kawaiiserver/portainer
docker compose pull
docker compose up -d
docker system prune
```

### Check Logs
```bash
docker logs portainer
sudo journalctl -u cloudflared -f
```

### Backup Routine
```bash
# Add to crontab (crontab -e)
0 2 * * * tar czf /backup/portainer-backup-$(date +\%Y\%m\%d).tar.gz -C /path/to/portainer/portainer-data .
```

## 🔒 Security Considerations

⚠️ **Important Security Notes**:

1. **Strong Password**: Use a very strong admin password
2. **2FA**: Enable two-factor authentication in Portainer settings
3. **Limited Access**: Consider creating limited user accounts for daily use
4. **Regular Updates**: Keep Portainer updated to latest version
5. **Backups**: Regularly backup your Portainer data

## 🎯 Why This Setup?

- **Security**: No ports open on your router → no attack surface
- **Convenience**: Manage all your Docker containers from a beautiful web UI
- **Visibility**: See resource usage, logs, and container status at a glance
- **Control**: Full control over your Docker environment with an intuitive interface

## 📞 Need Help?

1. Check the logs: `docker logs portainer`
2. Verify tunnel: `sudo systemctl status cloudflared`
3. Test locally: `curl http://localhost:9006`
4. Check Docker: `sudo systemctl status docker`
5. Check DNS: `dig portainer.kawaii-san.org`

Remember: Portainer has full control over your Docker environment. Keep it secure and regularly updated!

---

*KawaiiServer - Making Docker management adorable and secure! 🐳💖*
