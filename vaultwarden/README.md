# Vaultwarden on KawaiiServer

A self-hosted, secure, and adorable password management solution powered by Vaultwarden (a lightweight Bitwarden-compatible server) and Cloudflare Tunnel.

## ✨ What This Achieves

- **Your Own Password Manager**: Stop relying on cloud services and host your passwords on your own hardware
- **Zero-Open Ports**: Uses Cloudflare Tunnel for secure external access without exposing your server to the internet
- **Bitwarden Compatibility**: Works with all Bitwarden clients (browser extensions, mobile apps, desktop apps)
- **Kawaii Security**: Combines Docker isolation, Cloudflare protection, and secure networking

## 🚀 Prerequisites

Before you begin, ensure your KawaiiServer has:

1. **Docker & Docker Compose** installed
2. **Cloudflare Tunnel** configured and running
3. **Domain** pointed to Cloudflare (with proxy enabled)
4. **Docker Network** created: `docker network create kawaiinet`

## 📁 File Structure

```
kawaiiserver/
└── vaultwarden/
    ├── docker-compose.yml    # Main deployment file
    ├── .env                 # Environment variables (optional)
    └── README.md           # This file
```

## 🛠️ Setup Instructions

### 1. Clone and Configure

```bash
cd ~/kawaiiserver/vaultwarden
```

Edit the [`docker-compose.yml`](./docker-compose.yml) file:

```yaml
environment:
  - ADMIN_TOKEN=your_generated_token_here  # ← Change this!
```

**Generate a secure admin token:**
```bash
openssl rand -base64 48
# Example output: wq9s8d7f6g5h4j3k2l1m0n9o8p7q6r5s4t3u2v1w0x9y8z7a6b5c4d3e2f1g0h
```

### 2. Deploy Vaultwarden

```bash
docker compose up -d
```

Verify it's running:
```bash
docker ps
curl -I http://localhost:9001
```

### 3. Configure Cloudflare Tunnel

Ensure your `/etc/cloudflared/config.yml` includes:

```yaml
ingress:
  - hostname: passwords.yourdomain.com  # Your chosen subdomain
    service: http://localhost:9001      # Points to Vaultwarden
  - service: http_status:404
```

Restart the tunnel:
```bash
sudo systemctl restart cloudflared
```

### 4. Create DNS Record

```bash
sudo cloudflared tunnel route dns your-tunnel-name passwords.yourdomain.com
```

## 🔧 Configuration Options

### Environment Variables (Optional)

Create a `.env` file for easier management:

```env
ADMIN_TOKEN=your_secure_token_here
SIGNUPS_ALLOWED=false
WEBSOCKET_ENABLED=true
```

### Volume Persistence

Your password data is stored in a Docker volume named `vaultwarden_data`. To backup:

```bash
# Backup
docker run --rm -v vaultwarden_data:/source -v $(pwd):/backup alpine tar czf /backup/vaultwarden-backup-$(date +%Y%m%d).tar.gz -C /source .

# Restore
docker run --rm -v vaultwarden_data:/target -v $(pwd):/backup alpine sh -c "cd /target && tar xzf /backup/your-backup-file.tar.gz --strip-components=1"
```

## 🌐 Access Points

- **Web Interface**: `https://passwords.yourdomain.com`
- **Admin Panel**: `https://passwords.yourdomain.com/admin` (use your ADMIN_TOKEN)
- **Mobile Apps**: Use your domain in Bitwarden app settings

## 🚨 Common Issues & Solutions

### ❌ "502 Bad Gateway" Error
**Cause**: Vaultwarden isn't running or Cloudflare Tunnel can't connect
**Fix**:
```bash
# Check if Vaultwarden is running
docker ps

# Check logs
docker logs vaultwarden

# Test local access
curl http://localhost:9001
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

### ❌ Admin panel not working
**Cause**: Incorrect ADMIN_TOKEN or token not set
**Fix**:
```bash
# Regenerate token and update compose file
openssl rand -base64 48

# Restart container
docker compose down
docker compose up -d
```

### ❌ Mobile apps can't connect
**Cause**: DNS not propagated or SSL issues
**Fix**:
- Wait for DNS propagation (can take up to 24 hours)
- Ensure Cloudflare proxy is enabled (orange cloud)
- Check `https://passwords.yourdomain.com` works in browser first

## 🔄 Maintenance

### Update Vaultwarden
```bash
cd ~/kawaiiserver/vaultwarden
docker compose pull
docker compose up -d
docker system prune
```

### Check Logs
```bash
docker logs vaultwarden
sudo journalctl -u cloudflared -f
```

### Backup Routine
```bash
# Add to crontab (crontab -e)
0 2 * * * /path/to/your/backup-script.sh
```

## 🎯 Why This Setup?

- **Security**: No ports open on your router → no attack surface
- **Privacy**: Your passwords never leave your server
- **Performance**: Cloudflare CDN provides global accessibility
- **Control**: You own all the data and infrastructure

## 📞 Need Help?

1. Check the logs: `docker logs vaultwarden`
2. Verify tunnel: `sudo systemctl status cloudflared`
3. Test locally: `curl http://localhost:9001`
4. Check DNS: `dig passwords.yourdomain.com`

Remember: Your passwords are precious! Always maintain backups and keep your server updated.

---

*KawaiiServer - Making self-hosting adorable and secure! 💖*
