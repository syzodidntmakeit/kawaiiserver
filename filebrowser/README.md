# FileBrowser on KawaiiServer

A lightweight, web-based file manager that gives you direct access to your server's file system through a beautiful web interface. Perfect for quick file management and downloads!

## ✨ What This Achieves

- **Web-Based File Management**: Access your server's files from any browser
- **Zero-Open Ports**: Uses Cloudflare Tunnel for secure external access
- **Simple & Fast**: Lightweight alternative to full-featured solutions like Nextcloud
- **Direct Filesystem Access**: Manage files and folders directly on your server

## 🚀 Prerequisites

Before you begin, ensure your KawaiiServer has:

1. **Docker & Docker Compose** installed
2. **Cloudflare Tunnel** configured and running
3. **Domain** pointed to Cloudflare (with proxy enabled)
4. **Docker Network** created: `docker network create kawaiinet`

## 📁 File Structure

```
kawaiiserver/
└── filebrowser/
    ├── docker-compose.yml    # Main deployment file
    ├── filebrowser.json      # Configuration file (optional)
    └── README.md            # This file
```

## 🛠️ Setup Instructions

### 1. Create the Directory Structure
```bash
mkdir -p ~/kawaiiserver/filebrowser
cd ~/kawaiiserver/filebrowser
```

### 2. Create the [Docker Compose File](./docker-compose.yml)
```bash
nano docker-compose.yml
```

Paste the provided configuration.

### 3. Create Configuration File (Optional)
```bash
nano filebrowser.json
```

Basic configuration:
```json
{
  "port": 80,
  "baseURL": "",
  "address": "",
  "log": "stdout",
  "database": "/config/database.db",
  "root": "/srv"
}
```

### 4. Deploy FileBrowser
```bash
docker compose up -d
```

### 5. Configure Cloudflare Tunnel
Add to your `/etc/cloudflared/config.yml`:

```yaml
ingress:
  - hostname: files.kawaii-san.org    # FileBrowser
    service: http://localhost:9003    # Matches your compose port
  - service: http_status:404
```

### 6. Create DNS Record
```bash
sudo cloudflared tunnel route dns kawaiinet files.kawaii-san.org
```

### 7. Restart Cloudflare Tunnel
```bash
sudo systemctl restart cloudflared
```

## 🔧 Initial Setup

### First Login
1. **Access**: Visit `https://files.kawaii-san.org`
2. **Default Credentials**:
   - Username: `admin`
   - Password: `admin`

3. **Immediate Actions**:
   - Change the admin password in Settings → Profile
   - Configure any desired settings in the Settings panel

### Security Considerations
⚠️ **Important**: FileBrowser has access to your entire filesystem (`/`). Consider these security measures:

1. **Change default password immediately**
2. **Create limited users** in FileBrowser for specific directories
3. **Consider restricting the mounted path** (e.g., `/home` instead of `/`)
4. **Use read-only access** for certain users if needed

## 🎯 Access Points

- **Web Interface**: `https://files.kawaii-san.org`
- **Default Credentials**: admin/admin (change immediately!)
- **File Access**: Full server filesystem access

## 🔧 Configuration Options

### Custom Mount Points
For better security, consider limiting access to specific directories:

```yaml
volumes:
  - /home:/srv  # Only access home directories
  # - /var/www:/srv  # Only web files
  # - /media:/srv    # Only media files
```

### Environment Variables
Available configuration options:
```yaml
environment:
  - FB_BASEURL=/files
  - FB_PORT=80
  - FB_ROOT=/srv
  - FB_DATABASE=/config/database.db
```

### User Management
Create limited users through the web interface:
1. Go to Settings → Users
2. Click "New User"
3. Set appropriate permissions and scope

## 🚨 Common Issues & Solutions

### ❌ Permission Denied Errors
**Cause**: FileBrowser runs as your user (1000:1000) but needs additional permissions
**Fix**: 
```bash
# Add necessary capabilities or adjust permissions
sudo setcap cap_dac_override=+ep /path/to/filebrowser
```

### ❌ Cannot Write to Certain Directories
**Cause**: User 1000 doesn't have write permissions
**Fix**:
```bash
# Option 1: Change ownership of specific directories
sudo chown -R 1000:1000 /path/to/directory

# Option 2: Run as root (not recommended)
# Remove the "user: 1000:1000" line from compose file
```

### ❌ Container Won't Start
**Cause**: Port conflict or permission issues
**Fix**:
```bash
# Check for port conflicts
sudo netstat -tulpn | grep :9003

# Check Docker logs
docker logs filebrowser
```

### ❌ "Connection Refused" through Cloudflare
**Cause**: FileBrowser not running or wrong port
**Fix**:
```bash
# Check if container is running
docker ps

# Test local access
curl http://localhost:9003
```

## 🔄 Maintenance

### Update FileBrowser
```bash
cd ~/kawaiiserver/filebrowser
docker compose pull
docker compose up -d
docker system prune
```

### Backup Configuration
```bash
# Backup database and config
docker cp filebrowser:/config ./filebrowser-backup-$(date +%Y%m%d)

# Or just backup the important files
docker exec filebrowser tar czf /tmp/backup.tar.gz /config
docker cp filebrowser:/tmp/backup.tar.gz .
```

### Restore from Backup
```bash
# Stop container
docker compose down

# Restore files
docker cp backup.tar.gz filebrowser:/tmp/
docker exec filebrowser tar xzf /tmp/backup.tar.gz -C /

# Restart
docker compose up -d
```

## 🎨 Customization

### Theme and Appearance
Customize through the web interface:
- Settings → Global Settings → Branding
- Custom CSS available in advanced settings

### File Previews
FileBrowser supports previews for:
- Images (JPEG, PNG, GIF, etc.)
- Videos (MP4, WebM, etc.)
- Documents (PDF, Office files)
- Code files with syntax highlighting

### Integration with Other Services
Use FileBrowser to:
- Manage files for other services (Nextcloud, Vaultwarden)
- Upload and download files easily
- Quick file edits and previews

## 🔒 Security Best Practices

1. **Change Default Password**: First thing after login
2. **Create Limited Users**: Avoid using admin for daily tasks
3. **Restrict Access**: Consider mounting only necessary directories
4. **Enable 2FA**: If supported in future versions
5. **Regular Updates**: Keep FileBrowser updated
6. **Monitor Access**: Check logs regularly

## 📊 Performance Tips

1. **Use SSD Storage**: For better file browsing performance
2. **Limit Large Directories**: Avoid browsing directories with 10,000+ files
3. **Enable Caching**: In settings for better performance
4. **Use Search**: Instead of browsing large directories

## 📞 Need Help?

1. **Check Container Logs**:
   ```bash
   docker logs filebrowser -f
   ```

2. **Verify Local Access**:
   ```bash
   curl http://localhost:9003
   ```

3. **Check File Permissions**:
   ```bash
   docker exec filebrowser ls -la /srv
   ```

4. **Test Cloudflare Tunnel**:
   ```bash
   sudo cloudflared tunnel test
   ```

5. **Community Support**:
   - [FileBrowser GitHub](https://github.com/filebrowser/filebrowser)
   - [Documentation](https://filebrowser.org/)

---

*KawaiiServer - Making file management adorable and secure! 📁💖*

*Remember: With great power (over your filesystem) comes great responsibility. Use those permissions wisely!*
