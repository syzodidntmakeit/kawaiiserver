# Nextcloud on KawaiiServer

A self-hosted, private cloud storage solution powered by Nextcloud and secured through Cloudflare Tunnel. Your personal Dropbox alternative, running on your own hardware!

## ✨ What This Achieves

- **Your Own Cloud Storage**: Complete control over your files, calendars, contacts, and more
- **Zero-Open Ports**: Uses Cloudflare Tunnel for secure external access without exposing your server to the internet
- **Cross-Platform Sync**: Works with all devices (desktop, mobile, web)
- **Kawaii Privacy**: Your data stays on your server, not in someone else's cloud

## 🚀 Prerequisites

Before you begin, ensure your KawaiiServer has:

1. **Docker & Docker Compose** installed
2. **Cloudflare Tunnel** configured and running
3. **Domain** pointed to Cloudflare (with proxy enabled)
4. **Docker Network** created: `docker network create kawaiinet`

## 📁 File Structure

```
kawaiiserver/
└── nextcloud/
    ├── docker-compose.yml    # Main deployment file
    ├── apps/                 # Custom Nextcloud apps
    ├── config/               # Nextcloud configuration
    ├── data/                 # User data
    └── README.md            # This file
```

## 🛠️ Setup Instructions

### 1. Create the Directory Structure
```bash
mkdir -p ~/kawaiiserver/nextcloud
cd ~/kawaiiserver/nextcloud
```

### 2. Create the [Docker Compose File](./docker-compose.yml)
```bash
nano docker-compose.yml
```

Paste the provided configuration.

### 3. Generate Secure Passwords

**Generate secure passwords for your database:**

```bash
# Generate database user password
openssl rand -base64 12
# Example: aBcDeFg1H2j3K4

# Generate database root password (different from user password)
openssl rand -base64 12
# Example: xYzAbC1d2E3f4G
```

### 4. Configure the Docker Compose

Edit the [`docker-compose.yml`](./docker-compose.yml) file with your actual passwords:

```yaml
environment:
  - NEXTCLOUD_TRUSTED_DOMAINS=cloud.kawaii-san.org
  - MYSQL_HOST=nextcloud-db
  - MYSQL_DATABASE=nextcloud
  - MYSQL_USER=nextcloud
  - MYSQL_PASSWORD=aBcDeFg1H2j3K4  # ← YOUR GENERATED PASSWORD
  - MYSQL_ROOT_PASSWORD=xYzAbC1d2E3f4G  # ← YOUR GENERATED ROOT PASSWORD
```

### 5. Deploy Nextcloud

```bash
docker compose up -d
```

### 6. Configure Cloudflare Tunnel

Add to your `/etc/cloudflared/config.yml`:

```yaml
ingress:
  - hostname: cloud.kawaii-san.org    # Nextcloud
    service: http://localhost:9002    # Matches your compose port
  - service: http_status:404
```

### 7. Create DNS Record

```bash
sudo cloudflared tunnel route dns kawaiinet cloud.kawaii-san.org
```

### 8. Restart Cloudflare Tunnel

```bash
sudo systemctl restart cloudflared
```

## 🔧 Initial Setup

1. **First Access**: Visit `https://cloud.kawaii-san.org`
2. **Create Admin Account**: 
   - Username: `admin` (or your choice)
   - Password: **Create a strong admin password** (different from database passwords!)
3. **Database Configuration**:
   - Database: MySQL/MariaDB
   - Username: `nextcloud`
   - Password: Your generated `MYSQL_PASSWORD`
   - Database name: `nextcloud`
   - Host: `nextcloud-db:3306`

## 🔧 Configuration Options

### Environment Variables (Optional)

Create a `.env` file for easier management:

```env
MYSQL_PASSWORD=your_secure_password_here
MYSQL_ROOT_PASSWORD=your_secure_root_password_here
NEXTCLOUD_TRUSTED_DOMAINS=cloud.kawaii-san.org
```

### Volume Persistence

Your Nextcloud data is stored in Docker volumes. To backup:

```bash
# Backup database
docker exec nextcloud-db mysqldump -u nextcloud -p"$MYSQL_PASSWORD" nextcloud > nextcloud-backup-$(date +%Y%m%d).sql

# Backup config and data
tar czf nextcloud-data-backup-$(date +%Y%m%d).tar.gz apps/ config/ data/
```

## 🌐 Access Points

- **Web Interface**: `https://cloud.kawaii-san.org`
- **Desktop Client**: Download from nextcloud.com
- **Mobile Apps**: Available on iOS and Android
- **WebDAV**: `https://cloud.kawaii-san.org/remote.php/dav`

## 🚨 Common Issues & Solutions

### ❌ "Connection refused" to Database
**Cause**: Nextcloud starts before MariaDB is ready
**Fix**: 
```bash
docker restart nextcloud
```

### ❌ "Invalid trusted domain" Error
**Cause**: Domain mismatch in configuration
**Fix**: Ensure `NEXTCLOUD_TRUSTED_DOMAINS` matches your actual domain

### ❌ File Upload Size Limits
**Fix**: Increase PHP limits by creating `.user.ini` in Nextcloud root:
```ini
upload_max_filesize = 10G
post_max_size = 10G
memory_limit = 1G
```

### ❌ Performance Issues
**Solutions**:
1. **Enable Redis caching** (add to config.php):
   ```php
   'memcache.distributed' => '\OC\Memcache\Redis',
   'memcache.locking' => '\OC\Memcache\Redis',
   'redis' => [
     'host' => 'redis',
     'port' => 6379,
   ],
   ```
2. **Add Redis container** to your compose file

## 🔄 Maintenance

### Update Nextcloud
```bash
cd ~/kawaiiserver/nextcloud
docker compose pull
docker compose up -d
docker system prune
```

### Check Logs
```bash
docker logs nextcloud
docker logs nextcloud-db
sudo journalctl -u cloudflared -f
```

### Backup Routine
```bash
# Add to crontab (crontab -e)
0 2 * * * /path/to/your/nextcloud-backup-script.sh
```

## 📊 Recommended Apps to Install

1. **Calendar** - Personal and shared calendars
2. **Contacts** - Address book sync
3. **Notes** - Markdown note taking
4. **Tasks** - Todo list management
5. **Two-Factor Authentication** - Extra security
6. **Contacts Interaction** - Better contact management

## 🔒 Security Best Practices

1. **Enable 2FA** for admin accounts
2. **Regular updates** - Keep Nextcloud and apps updated
3. **Strong passwords** - Use different passwords for each service
4. **Backup regularly** - Both database and files
5. **Monitor logs** - Check for suspicious activity

## 📞 Need Help?

1. Check the logs: `docker logs nextcloud`
2. Verify tunnel: `sudo systemctl status cloudflared`
3. Test locally: `curl http://localhost:9002`
4. Check DNS: `dig cloud.kawaii-san.org`

Remember: Your data is precious! Always maintain backups and keep your server updated.

---

*KawaiiServer - Your data deserves a cute and secure home! 💖*
