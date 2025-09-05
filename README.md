# 📦 Self-Hosted Server Stack

Welcome to my homelab setup! This repo documents my **self-hosted services, hardware, and philosophy** for running a personal cloud/server stack. If you want to own your digital life, this guide is for you. 🚀

---

## 🖥️ Services

### Media Server
- **Jellyfin** – Open source Netflix replacement, clean UI
- **Navidrome** – Streams FLAC, pulls great metadata
- **Prowlarr** – Indexer for *arr stack
- **Sonarr** – TV shows (→ Jellyfin)
- **Radarr** – Movies (→ Jellyfin)
- **Lidarr** – Music (→ Navidrome)
- **Bazarr** – Subtitles for media servers

### File Storage
- **[Filebrowser](./filebrowser)** – File management in browser ✅
- **[Nextcloud](./nextcloud)** – File syncing across devices ✅

### Socials
- **Mastodon** - Self-hosted Twitter
- **Lemmy** - Self-hosted Reddit
- **Matrix + Element** - Self-hosted Discord (resource-heavy)
- **Pixelfed** - Self-hosted Instagram
- **[Piped](./piped)** - Self-hosted YouTube ✅

### Web Server
- **Traefik** – Docker-integrated, auto-SSL reverse proxy
- **Nginx** – Sophisticated reverse proxy, more control
- **Cloudflared tunnel** - Reverse Proxy with Cloudflare domain name (kawaii-san.org)

### Utility Server
- **[Vaultwarden](./vaultwarden)** – Self-hosted password manager (Bitwarden fork) ✅
- **Watchtower** – Automatic Docker updates
- **qBittorrent** – Torrent client

### Server Management
- **Grafana + Prometheus** – Advanced monitoring & dashboards
- **Netdata** – Deep server insights
- **Portainer** – Docker container manager
- **Dashdot** – Lightweight system stats
- **Tailscale** – Secure SSH / VPN overlay

### Text Editing & Document Management
- **Memos** – Lightweight Markdown notes
- **Trilium** – Encrypted, hierarchical notes
- **VS Code Server** – Browser-based code editor
- **Bookstack** – Wiki-style documentation
- **Paperless-ngx** - Document management system

### Privacy Tools
- **SearXNG** – Private search engine
- **Kiwix** – Local Wikipedia
- **Gluetun** - VPN Client (Using Mullvad VPN)
- **Fail2Ban** – SSH brute force protection
- **UFW** – Firewall

### Local Knowledge
- **Kiwix** – Local Wikipedia
- **Ollama** – Local text generation
- **Stable Diffusion** – Image generation
- **Wiki RAG** - An AI trained on and only focuses on wikipedia

### Navigation
- **OpenStreetMap** – Tile service
- **Geofabrik** – OSM map data

### Finance
- **Firefly III** – Personal finance tracker

### Websites
- **Homepage** – Dashboard hub for services
- **Media Hub** – Aggregated media service hub

---

## 🔐 Privacy-First Client Applications

While self-hosting handles the server side, I've also migrated to privacy-respecting alternatives on the client side. Why give Big Tech your data when better options exist?

### Daily Drivers
- **Mullvad Browser** → Replaced Chrome/Edge (Tor Browser without Tor, €5/month VPN = SGD 7.48)
- **NewPipe** → YouTube without ads/tracking (pairs with self-hosted Piped)
- **OsmAnd** → Google Maps replacement (offline OSM data)
- **Signal** → WhatsApp for secure messaging
- **Thunderbird** → Gmail web client replacement
- **LibreOffice** → Microsoft Office alternative

### Mobile Privacy Stack
- **F-Droid** → Google Play Store for FOSS apps
- **Aurora Store** → Anonymous Google Play access
- **Brave Search** → Google Search (when SearXNG isn't available)
- **Organic Maps** → OsmAnd alternative for lighter usage

### Cost Breakdown (Monthly)
| Service | Cost | Savings vs Big Tech |
|---------|------|-------------------|
| Mullvad VPN | €5 ($7.48 SGD) | vs ExpressVPN $12.95 |
| Domain + CF | ~$1 | vs Google Workspace $6 |
| **Total** | **~$8.50 SGD** | **vs $20+ for equivalent** |

### Why This Matters
- **No tracking** across services
- **Open source** = auditable code
- **One-time costs** vs subscription hell
- **Actually faster** (no bloat/ads)
- **Works offline** when self-hosted services are down
- **Moral superiority** in tech discussions ✨

---

## ⚙️ System Hardware

### Options (SGD Pricing)

| System | CPU | RAM | Storage | Price |
|--------|-----|-----|---------|-------|
| Dell Optiplex 5070 SFF | i5-9500 | 16GB | 512GB NVMe + 1TB SSD | $300 |
| Dell Optiplex 3060 | i5-8500 | 8GB | 256GB SSD | $150 |
| Dell Optiplex 3060 SFF | i7-8700 | 16GB | 480GB SSD | $318 |
| HP EliteDesk 800 G5 Mini | i5-9500T | 16GB | 256GB NVMe + 512GB HDD | $238 |
| Lenovo ThinkCentre M920q | i5-9500T | 16GB | 480GB SSD | $299 |

### Recommended Specs

| Component | Minimum | Recommended | Why |
|-----------|---------|-------------|-----|
| CPU | i5-8500 / Ryzen 5 3600 | i7-8700 / Ryzen 7 3700X | Media transcoding + AI |
| RAM | 16GB | 32GB | Containers are hungry |
| Storage | 256GB SSD | 512GB NVMe | OS + containers |
| Form Factor | SFF | Tower | Upgradability |
| GPU | – | RTX 3060 / RX 6800 | Local AI models |

---

## 📦 NAS Hardware

| Option | Cost (SGD) | Pros | Cons |
|--------|-----------|------|------|
| Orange Pi 5 Kit | ~$290 | RK3588S CPU, PCIe NVMe, USB 3.0 | Less support |
| Raspberry Pi 4 | ~$120 | Big community, reliable | Limited USB 3.0 speeds |
| Used Mini PC | ~$160 | x86, SATA, powerful | Higher power use |

➡️ **Orange Pi 5 is the sweet spot** – strong NAS performance + efficiency.

---

## 💾 Storage Strategy

- **Main Server**: 512GB NVMe → OS, containers, configs
- **NAS Box**: 32GB eMMC (OS) + 4TB WD Red HDD (media)
- **Bonus**: 1TB HDD (overflow/backup)
- **Connection**: SMB/NFS shares mounted from NAS to server

---

## 🛒 Recommended Setup (Singapore Pricing)

| Component | Specs | Price | Source |
|-----------|-------|-------|--------|
| Main Server | Dell Optiplex i5-10500T, 16GB RAM, 512GB NVMe, 1TB HDD | $278 | Carousell |
| NAS Box | Orange Pi 5 Kit (8GB RAM, 32GB eMMC, PSU, Heatsink) | $290 | Shopee |
| Storage | 4TB WD Red HDD | $70 | Carousell |
| Network Switch | D-Link DGS-1100-08V2 (8-Port Managed) | $30 | Carousell |

💡 Additional: Cat6 cables (~$15), USB 3.0 to SATA adapter (~$10-15), UPS (~$60-100)

---

## 🛠️ Core Technologies
- **OS**: Debian 12
- **Containerization**: Docker + Docker Compose
- **Reverse Proxy**: Traefik / Nginx
- **Domain**: Cloudflare DNS
- **VPN**: Mullvad (with *arr stack)
- **Security**: UFW + Fail2Ban

---

## 🚀 Quick Start
1. Get hardware (used office PC + SBC NAS)
2. Install Debian 12
3. Setup Docker + Compose
4. Configure Cloudflare + domain
5. Setup NAS with OpenMediaVault
6. Deploy services gradually
7. Harden with UFW, Fail2Ban, auth

---

## 🌟 Why Self-Host?
- **Privacy** – your data stays with you
- **Control** – no forced updates or price hikes
- **Learning** – networking + Linux mastery
- **Bragging Rights** – "I host my own Netflix"
- **Cost Effective** – one-time hardware vs SaaS bills
- **Reliability** – no random outages

---

## ⚠️ Requirements
- Patience + willingness to learn Linux
- Networking basics
- Time for maintenance
- Ability to break (and fix) things

---

## 🤝 Contributing
Got improvements, optimizations, or security fixes? Open an issue or PR!

---

## 📚 Resources
- [Debian Wiki](https://wiki.debian.org/)
- [Docker Documentation](https://docs.docker.com/)
- [Cloudflare Docs](https://developers.cloudflare.com/)
- [r/selfhosted](https://reddit.com/r/selfhosted)

---

## 🎯 Pro Tips
- Start small → expand later
- Document **everything**
- Use **3-2-1 backup rule**
- Test restores before you need them
- Watch resource usage
- Isolate networks with VLANs

---

> P.S. Yes, this setup runs my Rei Ayanami fanfiction archive. No, you can't have access. Some things are sacred. 🫡
