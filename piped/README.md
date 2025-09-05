# Piped YouTube Frontend on KawaiiServer

A privacy-friendly, self-hosted YouTube frontend that removes ads, tracking, and gives you back control of your YouTube experience. Your personal NewPipe-like service, running on your own server!

## ✨ What This Achieves

- **Ad-Free YouTube**: No more video ads or banner ads
- **Privacy Protection**: No Google tracking or analytics
- **SponsorBlock Integration**: Skip sponsored segments automatically
- **Background Play**: Audio keeps playing when app is minimized
- **Subscriptions & Playlists**: Full YouTube feature compatibility

## 🚀 Prerequisites

Before you begin, ensure your KawaiiServer has:

1. **Docker & Docker Compose** installed
2. **Cloudflare Tunnel** configured and running
3. **Domain** pointed to Cloudflare (with proxy enabled)
4. **Docker Network** created: `docker network create kawaiinet`

## 📁 File Structure

```
kawaiiserver/
└── piped/
    ├── docker-compose.yml    # Main deployment file
    └── README.md            # This file
```

## 🛠️ Setup Instructions

### 1. Create the Directory Structure
```bash
mkdir -p ~/kawaiiserver/piped
cd ~/kawaiiserver/piped
```

### 2. Create the Docker Compose File
```bash
nano docker-compose.yml
```

Paste the provided configuration.

### 3. Deploy Piped
```bash
docker compose up -d
```

### 4. Configure Cloudflare Tunnel
Add to your `/etc/cloudflared/config.yml`:

```yaml
ingress:
  # Piped main interface
  - hostname: youtube.kawaii-san.org
    service: http://localhost:9004
  
  # Piped proxy (for video streaming)
  - hostname: pipedproxy.kawaii-san.org
    service: http://localhost:9004
  
  # SponsorBlock service
  - hostname: sponsorblock.kawaii-san.org
    service: http://localhost:9005
  
  - service: http_status:404
```

### 5. Create DNS Records
```bash
sudo cloudflared tunnel route dns kawaiinet youtube.kawaii-san.org
sudo cloudflared tunnel route dns kawaiinet pipedproxy.kawaii-san.org
sudo cloudflared tunnel route dns kawaiinet sponsorblock.kawaii-san.org
```

### 6. Restart Cloudflare Tunnel
```bash
sudo systemctl restart cloudflared
```

## 🔧 Configuration Details

### Environment Variables Explained:
- `FRONTEND_URL=https://youtube.kawaii-san.org` - Your main access URL
- `PROXY_URL=https://pipedproxy.kawaii-san.org` - URL for video proxying
- `SPONSORBLOCK_URL=https://sponsorblock.kawaii-san.org` - SponsorBlock service URL

### Port Mappings:
- **9004**: Piped web interface (http://localhost:9004)
- **9005**: SponsorBlock service (http://localhost:9005)

## 📱 Mobile App Setup

### Android Installation:
1. **Install Piped App** from F-Droid or GitHub Releases
2. **Open App Settings**
3. **Set Custom Instance** to: `https://youtube.kawaii-san.org`
4. **Enable SponsorBlock** in settings

### Alternative Apps:
- **NewPipe**: Can use Piped as an extractor
- **LibreTube**: Supports Piped instances
- **SkyTube**: YouTube alternative client

## 🎯 Access Points

- **Web Interface**: `https://youtube.kawaii-san.org`
- **Video Proxy**: `https://pipedproxy.kawaii-san.org`
- **SponsorBlock**: `https://sponsorblock.kawaii-san.org`
- **Mobile Apps**: Use main web interface URL

## 🔧 Advanced Configuration

### Adding Database Persistence (Optional):
For subscriptions and history, add PostgreSQL:

```yaml
services:
  piped:
    # ... existing config ...
    environment:
      - DATABASE_URL=postgresql://piped:password@piped-db:5432/piped
    depends_on:
      - piped-db

  piped-db:
    image: postgres:15
    environment:
      - POSTGRES_DB=piped
      - POSTGRES_USER=piped
      - POSTGRES_PASSWORD=your_secure_password
    volumes:
      - piped_data:/var/lib/postgresql/data

volumes:
  piped_data:
```

### Adding Redis Caching (Optional):
For better performance:

```yaml
services:
  piped:
    # ... existing config ...
    environment:
      - REDIS_URL=redis://piped-redis:6379
    depends_on:
      - piped-redis

  piped-redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data

volumes:
  redis_data:
```

## 🚨 Common Issues & Solutions

### ❌ "Video unavailable" errors
**Cause**: YouTube API changes or rate limiting
**Fix**:
```bash
# Update Piped to latest version
docker compose pull
docker compose up -d
```

### ❌ Slow video loading
**Cause**: Bandwidth limitations or geographic restrictions
**Fix**:
- Ensure good server bandwidth
- Consider using a CDN for static assets
- Check server location relative to your location

### ❌ SponsorBlock not working
**Fix**:
```bash
# Check SponsorBlock container
docker logs sponsorblock

# Verify URLs match in environment variables
```

### ❌ Mobile app connection issues
**Fix**:
1. Verify URL: `https://youtube.kawaii-san.org`
2. Check HTTPS certificate
3. Ensure Cloudflare proxy is enabled (orange cloud)

## 🔄 Maintenance

### Update Piped
```bash
cd ~/kawaiiserver/piped
docker compose pull
docker compose up -d
docker system prune
```

### Backup Configuration
```bash
# Backup docker-compose file
cp docker-compose.yml docker-compose.backup.yml

# Note down your environment variables
docker inspect piped | grep -A5 -B5 "Environment"
```

### Monitor Performance
```bash
# Check container resource usage
docker stats piped sponsorblock

# View logs
docker logs piped -f
docker logs sponsorblock -f
```

## ⚠️ Important Considerations

### 1. **Bandwidth Usage**
- Video proxying uses significant bandwidth
- Monitor your server's bandwidth limits
- Consider enabling compression

### 2. **Rate Limiting**
- YouTube may rate limit your server IP
- Use multiple proxy backends if needed
- Consider rotating IPs if heavily used

### 3. **Legal Considerations**
- This is a frontend, not content rehosting
- You're still accessing YouTube's infrastructure
- Respect YouTube's Terms of Service

### 4. **Privacy Benefits**
- No Google tracking
- No personalized ads
- No viewing history sent to Google

## 📊 Recommended Settings

### For Best Experience:
1. **Enable SponsorBlock**: Auto-skip sponsors, intros, outros
2. **Use Return YouTube Dislike**: See dislike counts
3. **Background Play**: Audio continues when app closed
4. **Popup Player**: Picture-in-picture mode

### Mobile App Settings:
- **Video Player**: Native (ExoPlayer)
- **Audio Only**: Background play option
- **SponsorBlock**: All categories enabled
- **Default Instance**: Your custom instance

## 📞 Need Help?

1. **Check Container Status**:
   ```bash
   docker ps
   docker logs piped
   ```

2. **Verify Network Connectivity**:
   ```bash
   # Test local access
   curl http://localhost:9004

   # Test SponsorBlock
   curl http://localhost:9005
   ```

3. **Check Cloudflare Tunnel**:
   ```bash
   sudo systemctl status cloudflared
   sudo journalctl -u cloudflared -f
   ```

4. **Community Resources**:
   - [Piped GitHub](https://github.com/TeamPiped/Piped)
   - [Piped Documentation](https://docs.piped.video/)
   - [SponsorBlock GitHub](https://github.com/ajayyy/SponsorBlock)

---

*KawaiiServer - Making YouTube adorable and private again! 🎥💖*

*Enjoy ad-free, tracker-free YouTube browsing on all your devices! Remember to thank the SponsorBlock contributors for making sponsored segments skippable!*
