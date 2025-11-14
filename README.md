# 🏠 Chromebook Homelab Server

A lightweight, self-hosted server running on repurposed hardware. Built on Arch Linux, managed with Docker, and accessed securely via Tailscale VPN.

## 📊 Hardware Specs

- **Device**: Repurposed Chromebook
- **CPU**: Intel Celeron N4020 @ 1.1 GHz
- **RAM**: 8GB
- **Storage**: 64GB eMMC
- **OS**: Arch Linux
- **Uptime**: 24/7

## 🛠️ Tech Stack

### Core Infrastructure
- **OS**: Arch Linux (btw)
- **Containerization**: Docker + Docker Compose
- **Network**: Tailscale VPN for secure remote access
- **Management**: Dockge (Docker stack manager)
- **Monitoring**: Uptime Kuma

### Services Deployed

| Service | Purpose | Access |
|---------|---------|--------|
| **Vaultwarden** | Self-hosted password manager (Bitwarden compatible) | Public via Cloudflare Tunnel |
| **Syncthing** | P2P file synchronization across devices | Private (Tailscale) |
| **FreshRSS** | Self-hosted RSS feed aggregator | Private (Tailscale) |
| **Uptime Kuma** | Service monitoring and status tracking | Private (Tailscale) |
| **Mumble** | Low-latency VoIP server | Private (Tailscale) |
| **Dockge** | Docker compose stack management UI | Private (Tailscale) |

## 🏗️ Architecture

```
┌───────────────────────────────────────────────┐
│          Chromebook Server (Arch)             │
├───────────────────────────────────────────────┤
│  Docker Containers:                           │
│  ┌───────────┐  ┌───────────┐  ┌──────────┐   │
│  │Vaultwarden│  │ Syncthing │  │ FreshRSS │   │
│  └───────────┘  └───────────┘  └──────────┘   │
│  ┌──────────┐   ┌───────────┐  ┌──────────┐   │
│  │  Mumble  │   │   Dockge  │  │  Uptime  │   │
│  └──────────┘   └───────────┘  └──────────┘   │
└───────────────────────────────────────────────┘
          │                    │
          │                    │
     Cloudflare            Tailscale VPN
      Tunnel              (100.x.x.x/10)
          │                    │
          ▼                    ▼
    Public Access        Private Access
 (passwords only)    (all other services)
```

## 🔒 Security

### Network Security
- **Tailscale VPN**: All management interfaces only accessible via encrypted VPN
- **Cloudflare Tunnel**: Zero-trust tunneling for public services (no exposed ports)
- **No Port Forwarding**: Router firewall remains closed

### Service Security
- **Vaultwarden**: 2FA enabled, strong master password, admin panel protected
- **Container Isolation**: Each service runs in isolated Docker containers
- **Minimal Attack Surface**: Only password manager exposed publicly

## 🚀 Deployment

All services are deployed using Docker Compose for easy management and reproducibility:

```bash
# Example: Deploying Vaultwarden
cd ~/docker/vaultwarden
docker-compose up -d
```

### Directory Structure
```
~/docker/
├── vaultwarden/
│   ├── docker-compose.yml
│   └── vw-data/
├── syncthing/
│   ├── docker-compose.yml
│   └── config/
├── freshrss/
│   ├── docker-compose.yml
│   └── data/
├── uptime-kuma/
│   ├── docker-compose.yml
│   └── data/
├── mumble/
│   ├── docker-compose.yml
│   └── data/
├── dockge/
│   ├── docker-compose.yml
│   └── data/
└── cloudflared/
    └── docker-compose.yml
```

## 📈 Resource Usage

With all services running:
- **RAM Usage**: ~3-4GB (50% utilized)
- **CPU Usage**: <10% idle, spikes during sync operations
- **Storage**: ~15GB used (system + containers + data)
- **Network**: Minimal bandwidth (~1-5 Mbps during syncs)

## 🎯 Use Cases

### Personal Productivity
- **Password Management**: Secure, self-hosted alternative to cloud password managers
- **File Sync**: Keep documents, photos, and files synchronized across devices
- **RSS Reading**: Centralized feed aggregation for news, blogs, and content

### Communication
- **VoIP Server**: Low-latency voice chat for gaming and calls
- **Privacy-focused**: No data collection, no third-party analytics

### Monitoring & Management
- **Service Health**: Real-time monitoring of all hosted services
- **Container Management**: Visual interface for Docker operations

## 🔧 Management Tools

### Dockge Dashboard
Web-based interface for managing Docker Compose stacks:
- Start/stop/restart containers
- View real-time logs
- Edit compose files
- Deploy new services

### Uptime Kuma
Monitors all services and external websites:
- HTTP/HTTPS endpoint monitoring
- Notification support (Discord, Telegram, email)
- Public status pages
- Historical uptime data

## 🌐 Access Methods

### For Me
- **Tailscale VPN**: Full access to all services from anywhere
- **SSH**: Secure shell access via Tailscale network
- **Web UIs**: Browser-based management interfaces

### For Others
- **Vaultwarden**: Public access via `passwords.kawaii-san.org`
- **Mumble VoIP**: Invite-only via Tailscale network

## 💡 Why Self-Host?

1. **Privacy**: Full control over my data, no third-party access
2. **Learning**: Hands-on experience with Linux, Docker, networking, and system administration
3. **Cost**: One-time hardware cost vs. recurring subscription fees
4. **Flexibility**: Customize services exactly how I want them
5. **Ownership**: My data stays mine, no vendor lock-in

## 📚 Skills Demonstrated

- **Linux Administration**: Arch Linux installation, system configuration, package management
- **Containerization**: Docker, Docker Compose, container networking
- **Networking**: VPN configuration, reverse proxies, DNS management
- **Security**: Zero-trust networking, encryption, authentication
- **DevOps**: Infrastructure as code, service orchestration, monitoring
- **Hardware Repurposing**: Breathing new life into old hardware

## 🔮 Future Enhancements

- [ ] Automated backups with restic/borg
- [ ] Grafana + Prometheus for metrics visualization
- [ ] Pi-hole for network-wide ad blocking
- [ ] Gitea for self-hosted Git repositories
- [ ] Home Assistant for IoT device management
- [ ] Paperless-ngx for document management
- [ ] Jellyfin for media streaming (if storage expanded)

## 📝 Notes

This homelab runs on incredibly modest hardware, proving that you don't need enterprise-grade servers to host useful services. The entire setup uses ~4GB RAM and barely touches the CPU, leaving plenty of headroom for expansion.

---

**Built with**: Linux, Docker, open-source software, and too much free time

**Status**: Running 24/7 because why not ¯\\\_(ツ)_/¯
