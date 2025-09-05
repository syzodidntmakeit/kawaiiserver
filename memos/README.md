# Memos on KawaiiServer

A lightweight, self-hosted memo and note-taking service powered by Memos and secured through Cloudflare Tunnel. Your personal alternative to services like Google Keep or Notion!

## ✨ What This Achieves

- **Your Own Note-Taking Service**: Capture thoughts, ideas, and notes without relying on external services
- **Zero-Open Ports**: Uses Cloudflare Tunnel for secure external access without exposing your server to the internet
- **Lightweight & Fast**: Minimal resource usage compared to full-featured note apps
- **Kawaii Simplicity**: Clean interface focused on writing without distractions

## 🚀 Prerequisites

Before you begin, ensure your KawaiiServer has:

1. **Docker & Docker Compose** installed
2. **Cloudflare Tunnel** configured and running
3. **Domain** pointed to Cloudflare (with proxy enabled)
4. **Docker Network** created: `docker network create kawaiinet`

## 📁 File Structure

```
kawaiiserver/
└── memos/
    ├── docker-compose.yml    # Main deployment file
    ├── memos-data/          # Memos data directory (created automatically)
    └── README.md            # This file
```

## 🛠️ Setup Instructions

### 1. Create the Directory Structure
```bash
mkdir -p ~/kawaiiserver/memos
cd ~/kawaiiserver/memos
```

### 2. Create the [Docker Compose File](./docker-compose.yml)
```bash
nano docker-compose.yml
```

Paste the following configuration:

```yaml
version: '3'

services:
  memos:
    image: ghcr.io/usememos/memos:latest
    container_name: memos
    restart: unless-stopped
    ports:
      - "9005:5230"  # Memos runs on port 5230 internally
    networks:
      - kawaiinet
    environment:
      - MEMOS_MODE=prod
      - MEMOS_PORT=5230
      - MEMOS_DATA=/var/opt/memos
    volumes:
      - ./memos-data:/var/opt/memos
    user: "1000:1000"  # Run as your user to avoid permission issues

networks:
  kawaiinet:
    external: true
```

### 3. Deploy Memos
```bash
docker compose up -d
```

Verify it's running:
```bash
docker ps
curl -I http://localhost:9005
```

### 4. Configure Cloudflare Tunnel

Ensure your `/etc/cloudflared/config.yml` includes:

```yaml
ingress:
  - hostname: memos.kawaii-san.org    # Memos
    service: http://localhost:9005    # Matches your compose port
  - service: http_status:404
```

### 5. Create DNS Record
```bash
sudo cloudflared tunnel route dns kawaiinet memos.kawaii-san.org
```

### 6. Restart Cloudflare Tunnel
```bash
sudo systemctl restart cloudflared
```

## 🔧 Initial Setup

1. **First Access**: Visit `https://memos.kawaii-san.org`
2. **Create Admin Account**: 
   - Set your username and password
   - Choose a display name
3. **Start Memoing**: Begin creating your first memos!

## 🔧 Configuration Options

### Environment Variables (Optional)

Create a `.env` file for easier management:

```env
MEMOS_MODE=prod
MEMOS_PORT=5230
MEMOS_DATA=/var/opt/memos
```

### Volume Persistence

Your memos data is stored in the `memos-data` directory. To backup:

```bash
# Backup memos data
tar czf memos-backup-$(date +%Y%m%d).tar.gz memos-data/
```

## 🌐 Access Points

- **Web Interface**: `https://memos.kawaii-san.org`
- **Browser Extension**: Available for Chrome/Firefox
- **Mobile Access**: Use browser or progressive web app

## 🚨 Common Issues & Solutions

### ❌ "502 Bad Gateway" Error
**Cause**: Memos isn't running or Cloudflare Tunnel can't connect
**Fix**:
```bash
# Check if Memos is running
docker ps

# Check logs
docker logs memos

# Test local access
curl http://localhost:9005
```

### ❌ Permission Denied Errors
**Cause**: Directory permission issues
**Fix**:
```bash
# Fix permissions
sudo chown -R 1000:1000 memos-data/

# Restart container
docker compose down
docker compose up -d
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

### ❌ Mobile apps can't connect
**Cause**: DNS not propagated or SSL issues
**Fix**:
- Wait for DNS propagation (can take up to 24 hours)
- Ensure Cloudflare proxy is enabled (orange cloud)
- Check `https://memos.kawaii-san.org` works in browser first

## 🔄 Maintenance

### Update Memos
```bash
cd ~/kawaiiserver/memos
docker compose pull
docker compose up -d
docker system prune
```

### Check Logs
```bash
docker logs memos
sudo journalctl -u cloudflared -f
```

### Backup Routine
```bash
# Add to crontab (crontab -e)
0 2 * * * tar czf /backup/memos-backup-$(date +\%Y\%m\%d).tar.gz -C /path/to/memos/memos-data .
```

## 🎯 Why This Setup?

- **Security**: No ports open on your router → no attack surface
- **Privacy**: Your notes and thoughts remain private on your server
- **Performance**: Lightweight and fast, even on low-resource hardware
- **Control**: You own all your notes and data

## 📞 Need Help?

1. Check the logs: `docker logs memos`
2. Verify tunnel: `sudo systemctl status cloudflared`
3. Test locally: `curl http://localhost:9005`
4. Check DNS: `dig memos.kawaii-san.org`

Remember: Your thoughts and ideas are precious! While Memos is simple, your notes are valuable.

---

*KawaiiServer - Making note-taking adorable and secure! 📝💖*
