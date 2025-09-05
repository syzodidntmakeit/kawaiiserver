# Trilium Notes on KawaiiServer

A self-hosted, privacy-focused knowledge management system powered by Trilium Notes and Cloudflare Tunnel.

## ✨ What This Achieves

- **Your Personal Knowledge Base**: Organize notes, ideas, and information in a hierarchical structure
- **Zero-Open Ports**: Uses Cloudflare Tunnel for secure external access without exposing your server to the internet
- **Rich Note-Taking**: Supports formatted text, images, code snippets, and more
- **Sync Across Devices**: Access your notes from anywhere with automatic synchronization
- **Kawaii Organization**: Keep all your thoughts and information neatly organized and secure

## 🚀 Prerequisites

Before you begin, ensure your KawaiiServer has:

1. **Docker & Docker Compose** installed
2. **Cloudflare Tunnel** configured and running
3. **Domain** pointed to Cloudflare (with proxy enabled)
4. **Docker Network** created: `docker network create kawaiinet`

## 📁 File Structure

```
9004-trilium/
├── docker-compose.yml    # Main deployment file
└── README.md            # This file
```

## 🛠️ Setup Instructions

### 1. Deploy Trilium Notes

```bash
cd ~/9004-trilium
docker compose up -d
```

Verify it's running:
```bash
docker ps
curl -I http://localhost:9004
```

### 2. Configure Cloudflare Tunnel

Ensure your `/etc/cloudflared/config.yml` includes:

```yaml
ingress:
  - hostname: notes.kawaii-san.org  # Your chosen subdomain
    service: http://localhost:9004  # Points to Trilium
  - service: http_status:404
```

Restart the tunnel:
```bash
sudo systemctl restart cloudflared
```

### 3. Create DNS Record

```bash
sudo cloudflared tunnel route dns kawaiinet notes.kawaii-san.org
```

## 🔧 Configuration Options

### Data Persistence

Your notes and data are stored in a Docker volume named `trilium-data`. This ensures your data persists even if the container is recreated.

### Environment Variables

The current setup uses default configuration. You can add these environment variables to the `docker-compose.yml` if needed:

```yaml
environment:
  - TRILIUM_PORT=8080
  - TRILIUM_DOCUMENT_SECRET=your_secret_here
```

## 🌐 Access Points

- **Web Interface**: `https://notes.kawaii-san.org`
- **Initial Setup**: First visit will prompt you to set a document encryption password

## 🚨 Common Issues & Solutions

### ❌ "502 Bad Gateway" Error
**Cause**: Trilium isn't running or Cloudflare Tunnel can't connect
**Fix**:
```bash
# Check if Trilium is running
docker ps

# Check logs
docker logs trilium

# Test local access
curl http://localhost:9004
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

### ❌ Cannot create notes or save changes
**Cause**: Permission issues with data directory
**Fix**:
```bash
# Stop the container
docker compose down

# Fix permissions (replace 1000:1000 with your actual UID:GID if different)
sudo chown -R 1000:1000 trilium-data/

# Restart
docker compose up -d
```

### ❌ Mobile apps can't connect
**Cause**: DNS not propagated or SSL issues
**Fix**:
- Wait for DNS propagation (can take up to 24 hours)
- Ensure Cloudflare proxy is enabled (orange cloud)
- Check `https://notes.kawaii-san.org` works in browser first

## 🔄 Maintenance

### Update Trilium Notes
```bash
cd ~/9004-trilium
docker compose pull
docker compose up -d
docker system prune
```

### Check Logs
```bash
docker logs trilium
sudo journalctl -u cloudflared -f
```

### Backup Your Notes
```bash
# Backup the data volume
docker run --rm -v trilium-data:/source -v $(pwd):/backup alpine tar czf /backup/trilium-backup-$(date +%Y%m%d).tar.gz -C /source .
```

## 🎯 Why This Setup?

- **Security**: No ports open on your router → no attack surface
- **Privacy**: Your notes and knowledge remain on your server
- **Performance**: Cloudflare CDN provides global accessibility
- **Control**: You own all your data and intellectual property

## 📞 Need Help?

1. Check the logs: `docker logs trilium`
2. Verify tunnel: `sudo systemctl status cloudflared`
3. Test locally: `curl http://localhost:9004`
4. Check DNS: `dig notes.kawaii-san.org`

Remember: Your knowledge is precious! Always maintain backups of your Trilium data.

---

*KawaiiServer - Making self-hosting adorable and secure! 💖*
