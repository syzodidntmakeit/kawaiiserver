# 🎀 KawaiiServer - Getting Started Guide

A comprehensive guide to setting up your own adorable and secure self-hosted server using modern tools like Docker, Cloudflare Tunnels, and Tailscale.

## ✨ What is KawaiiServer?

KawaiiServer is a self-hosting setup that emphasizes:
- **Security First**: No open ports on your router
- **Ease of Access**: Secure remote access from anywhere
- **Cute Efficiency**: Modern tools working together harmoniously
- **Privacy Focused**: Your data stays on your hardware

## 📋 Prerequisites

### Hardware Requirements
- **Server**: Any PC (old gaming rig, mini PC, etc.)
- **CPU**: x86-64 (AMD/Intel) 
- **RAM**: 8GB+ recommended (4GB minimum)
- **Storage**: 50GB+ SSD/NVMe recommended
- **Network**: Wired Ethernet connection

### Software Requirements
- **Ubuntu Server** 22.04 LTS or newer
- **Domain name** (for Cloudflare Tunnels)
- **Cloudflare account** (free tier)
- **Tailscale account** (free tier)

## 🛠️ Core Technologies

### 1. Docker & Docker Compose
**Purpose**: Containerization for easy service management
```bash
# Installation
sudo apt update
sudo apt install docker.io docker-compose-plugin

# Verify
docker --version
docker compose version
```

### 2. Tailscale
**Purpose**: Secure mesh VPN for remote access
```bash
# Installation
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/tailscale-archive-keyring.gpg] https://pkgs.tailscale.com/stable/ubuntu jammy main" | sudo tee /etc/apt/sources.list.d/tailscale.list
sudo apt update
sudo apt install tailscale

# Setup
sudo tailscale up
```

### 3. Cloudflare Tunnel (cloudflared)
**Purpose**: Secure external access without open ports
```bash
# Installation
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
sudo apt-get install -f

# Authentication
sudo cloudflared tunnel login
```

### 4. UFW (Uncomplicated Firewall)
**Purpose**: Server firewall management
```bash
# Basic setup
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from 100.64.0.0/10 to any port 22  # Tailscale network
sudo ufw enable
```

## 🚀 Initial Setup Sequence

### Step 1: Server Preparation
1. Install Ubuntu Server
2. Create user account with sudo privileges
3. Update system: `sudo apt update && sudo apt upgrade -y`

### Step 2: Install Core Software
```bash
# Install Docker
sudo apt install docker.io docker-compose-plugin

# Install Tailscale
sudo apt install tailscale
sudo tailscale up

# Install Cloudflared
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
```

### Step 3: Network Configuration
```bash
# Configure UFW
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from 100.64.0.0/10 to any port 22  # Tailscale
sudo ufw allow 41641/udp  # Tailscale port
sudo ufw enable

# Create Docker network
docker network create kawaiinet
```

### Step 4: Service Deployment
1. **Create service directories**: `mkdir -p ~/kawaiiserver/{vaultwarden,nextcloud}`
2. **Add docker-compose.yml** files for each service
3. **Configure Cloudflare Tunnel** for each subdomain
4. **Start services**: `docker compose up -d`

## 🔧 Common Services Setup

### Vaultwarden (Password Manager)
```yaml
# docker-compose.yml
services:
  vaultwarden:
    image: vaultwarden/server:latest
    ports:
      - "127.0.0.1:9001:80"
    environment:
      - SIGNUPS_ALLOWED=false
    networks:
      - kawaiinet
```

### Nextcloud (File Sync)
```yaml
# docker-compose.yml
services:
  nextcloud:
    image: nextcloud:latest
    ports:
      - "127.0.0.1:9002:80"
    networks:
      - kawaiinet
```

## 🚨 Troubleshooting Guide

### Tailscale Issues
**❌ "Cannot connect to Tailscale"**
```bash
# Check status
sudo systemctl status tailscaled

# Restart service
sudo systemctl restart tailscaled

# Reauthenticate
sudo tailscale up
```

### Cloudflare Tunnel Issues
**❌ "Tunnel connection failed"**
```bash
# Check tunnel status
sudo systemctl status cloudflared

# View logs
sudo journalctl -u cloudflared -f

# Reauthenticate if needed
sudo cloudflared tunnel login
```

### Docker Issues
**❌ "Permission denied" for Docker**
```bash
# Add user to docker group
sudo usermod -aG docker $USER

# Log out and back in
exit
ssh yourserver

# Verify
docker run hello-world
```

**❌ "Port already in use"**
```bash
# Find what's using the port
sudo netstat -tulpn | grep :9001

# Stop conflicting service or choose different port
```

### Database Connection Issues
**❌ "MySQL connection refused"**
```bash
# Check if database container is running
docker ps

# View database logs
docker logs nextcloud-db

# Restart the application container after DB is ready
docker restart nextcloud
```

### SSL/HTTPS Issues
**❌ "SSL certificate errors"**
```bash
# Check Cloudflare DNS settings
# Ensure proxy is enabled (orange cloud)
# Verify tunnel is connected
sudo cloudflared tunnel list
```

## 🎯 Access Patterns

### Local Network Access
```bash
ssh username@server-local-ip
```

### Tailscale Access (Recommended)
```bash
ssh username@server-tailscale-hostname
```

### Public Access (via Cloudflare)
```
https://service.yourdomain.com
```

## 🔒 Security Checklist

- [ ] UFW configured to allow only Tailscale SSH
- [ ] All services bind to `127.0.0.1` (localhost only)
- [ ] Strong passwords for all services
- [ ] Regular system updates
- [ ] Docker containers running as non-root
- [ ] Tailscale with key expiry enabled
- [ ] Cloudflare WAF rules configured

## 📊 Monitoring & Maintenance

### Regular Commands
```bash
# Check container status
docker ps

# View logs
docker logs [container-name] -f

# System updates
sudo apt update && sudo apt upgrade -y

# Docker cleanup
docker system prune

# Backup volumes
docker run --rm -v volume_name:/source -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz -C /source .
```

## 🆘 Emergency Recovery

### Service Won't Start
```bash
# Force recreate containers
docker compose up -d --force-recreate

# Check detailed errors
docker compose logs
```

### Database Corruption
```bash
# Stop services
docker compose down

# Restore from backup
docker run --rm -v volume_name:/target -v $(pwd):/backup alpine sh -c "cd /target && tar xzf /backup/backup.tar.gz --strip-components=1"

# Restart
docker compose up -d
```

## 💖 Final Notes

- **Start Simple**: Deploy one service at a time
- **Test Locally**: Verify services work on localhost first
- **Backup Regularly**: Both data and configuration
- **Monitor Logs**: Check logs after each deployment
- **Stay Updated**: Keep all components current

Remember: KawaiiServer is about making self-hosting accessible and secure. Take your time, and don't hesitate to start over if something goes wrong!

---

*Happy self-hosting! May your server be kawaii and your uptime be eternal! 💕*
