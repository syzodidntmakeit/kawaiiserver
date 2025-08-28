# ✨ Kawaii Server ✨

> *"If buying isn't ownership, then piracy isn't theft."* - A wise person, probably

### Breaking free from Big Tech, one container at a time 🐱

Welcome to my personal server setup! This is where I've channeled my cybersecurity curiosity and networking passion into building a completely self-hosted digital life. No more giving Google my data, no more subscription fees for basic features, and definitely no more wondering what happens to my Rei Ayanami fanfiction.

---

## 🎯 Philosophy

- **Privacy First**: Your data belongs to YOU, not corporations
- **Open Source**: If you can't audit it, don't trust it
- **Self-Reliance**: Why pay monthly fees when you can learn and own it?
- **Learning by Doing**: Break things, fix them, become unstoppable

---

## 🏗️ Infrastructure

### Main Server Hardware Recommendations

| Component | Minimum Spec | Recommended | Why |
|-----------|-------------|-------------|-----|
| **CPU** | Intel i5-8500 / AMD Ryzen 5 3600 | Intel i7-8700 / AMD Ryzen 7 3700X | Media transcoding & AI workloads |
| **RAM** | 16GB DDR4 | 32GB DDR4 | Containers are hungry |
| **Storage** | 256GB SSD | 512GB NVMe | OS and containers only |
| **Form Factor** | SFF (Small Form Factor) | Tower | Upgradability matters |
| **GPU** *(Optional)* | - | RTX 3060 / RX 6800 | Local AI models |

### NAS Hardware (Orange Pi 5 Recommended)

| Option | Cost (SGD) | Pros | Cons |
|--------|------------|------|------|
| **Orange Pi 5 Kit** | ~$290 | RK3588S CPU, PCIe NVMe, USB 3.0, complete kit | Less community support than Pi |
| **Raspberry Pi 4** | ~$120 | Massive community, proven reliability | Limited to USB 3.0 speeds |
| **Used Mini PC** | ~$160 | Full x86, SATA ports, overkill performance | Higher power consumption |

*The Orange Pi 5 hits the sweet spot - powerful enough for advanced NAS features, efficient enough for 24/7 operation*

*Why separate? Keep your media safe when you inevitably break the main server while tinkering.*

### Storage Strategy
- **Main Server**: 512GB NVMe for OS, containers, and configs
- **NAS Box**: 32GB eMMC for OS + 4TB WD Red for media storage  
- **Bonus Storage**: 1TB HDD from main server (backup/overflow)
- **Connection**: SMB/NFS shares mounted from Orange Pi 5 to main server

### Suggested Hardware (Singapore Pricing - Tested Configs)

### Suggested Hardware (Singapore Pricing - ACTUAL Tested Deals!)

**🎯 Recommended Setup (Total: ~$668 SGD) - *Confirmed Available***

| Component | Specs | Price | Source |
|-----------|-------|--------|---------|
| **Main Server** | Dell OptiPlex - i5-10500T, 16GB RAM, 512GB NVMe, 1TB HDD | $278 | Carousell |
| **NAS Box** | Orange Pi 5 - 8GB RAM, 32GB eMMC, PSU, Heatsink (Complete Kit) | $290 | Shopee |
| **Storage** | 4TB WD Red HDD | $70 | Carousell |
| **Network Switch** | D-Link DGS-1100-08V2 - 8 Port Gigabit Managed | $30 | Carousell |

**💡 Additional Items Needed (~$20-40)**
- Ethernet cables (Cat6) x3: ~$15
- USB 3.0 to SATA adapter for external HDD: ~$10-15
- Optional UPS for power protection: ~$60-100

**🔧 Future Upgrades**
- RAM upgrade to 32GB: ~$50-80 (when needed)
- Additional storage drives as collection grows

*This setup gives you enterprise-grade homelab performance for less than most people spend on a gaming GPU!*

*Pro tip: Old office PCs are perfect for this. Let some corporation's depreciation be your gain.*

### Core Technologies

- **OS**: Debian 12 *(Stability over street cred - trust me on this one)*
- **Containerization**: Docker + Docker Compose
- **Reverse Proxy**: Traefik (auto-SSL) or Nginx
- **Domain**: Cloudflare-managed domain
- **VPN**: Mullvad (for the *arr stack)
- **Security**: UFW + Fail2Ban

---

## 🐳 Services Stack

### 🎬 Media Server
| Service | Purpose | Why This One |
|---------|---------|--------------|
| **Jellyfin** | Media streaming | Open source, no licensing BS, great UI |
| **Navidrome** | Music streaming | FLAC support, excellent metadata handling |

### 🗂️ File Management
| Service | Purpose | Why This One |
|---------|---------|--------------|
| **FileBrowser** | Web file manager | Simple, lightweight, perfect for server files |
| **Nextcloud** | File sync & sharing | Your own Google Drive, but better |
| **OpenMediaVault** | NAS management | *Separate hardware recommended - see below* |

### 🏴‍☠️ Content Acquisition *(arr Stack)*
| Service | Purpose | Integration |
|---------|---------|-------------|
| **qBittorrent** | Torrent client | Via Gluetun VPN for safety |
| **Prowlarr** | Indexer manager | Connects to all *arr services |
| **Sonarr** | TV show automation | → Jellyfin |
| **Radarr** | Movie automation | → Jellyfin |
| **Lidarr** | Music automation | → Navidrome |

### 🛠️ Utility Services
| Service | Purpose | Note |
|---------|---------|------|
| **Vaultwarden** | Password manager | Bitwarden-compatible, self-hosted |
| **Portainer** | Docker management | When things go wrong (they will) |
| **Watchtower** | Auto-updates | Keep containers fresh |
| **Uptime Kuma** | Service monitoring | Pretty green/red status lights |
| **Duplicati** | Backup solution | Encrypted backups to cloud storage |
| **Changedetection.io** | Website monitoring | Get notified when sites change |

### 🎮 Gaming & Entertainment
| Service | Purpose | Why Cool |
|---------|---------|---------|
| **Romm** | ROM management | Organize your *totally legal* game collection |
| **Stirling PDF** | PDF utilities | All-in-one PDF toolkit |
| **IT Tools** | Developer utilities | QR codes, JSON formatting, etc. |

### 🌐 Network Services
| Service | Purpose | Benefit |
|---------|---------|---------|
| **Pi-hole** | DNS ad blocking | Network-wide ad blocking |
| **Unbound** | Recursive DNS | Pair with Pi-hole for ultimate privacy |
| **WireGuard** | Self-hosted VPN | Access your network remotely |
| **NGINX Proxy Manager** | Easy reverse proxy | Alternative to Traefik with GUI |

### 📱 Communication & Social
| Service | Purpose | Replace |
|---------|---------|---------|
| **Matrix (Synapse)** | Chat server | Discord/Slack |
| **Jitsi Meet** | Video conferencing | Zoom/Teams |
| **FreshRSS** | RSS reader | Social media algorithms |

### 📊 Monitoring & Admin
| Service | Complexity | Best For |
|---------|------------|----------|
| **Dashdot** | Simple | Quick system overview |
| **Netdata** | Sophisticated | Detailed performance metrics |
| **Grafana** | Advanced | Custom dashboards & alerts |

### 📝 Productivity & Notes
| Service | Purpose | Style |
|---------|---------|-------|
| **Memos** | Quick notes | Lightweight, Markdown |
| **Trilium** | Knowledge base | Hierarchical, encrypted |
| **VS Code Server** | Code editor | Full IDE in browser |
| **BookStack** | Documentation wiki | Organized like actual books |

### 🔒 Privacy & Security
| Service | Purpose | Benefit |
|---------|---------|---------|
| **SearXNG** | Private search | No tracking, aggregated results |
| **Whoogle** | Google proxy | Google results without Google tracking |
| **Kiwix** | Offline Wikipedia | Knowledge without internet |
| **UFW + Fail2Ban** | Security | Basic protection that works |
| **Tailscale** | Secure access | SSH through encrypted network |

### 🤖 AI & Local Processing
| Service | Purpose | Hardware Need |
|---------|---------|---------------|
| **Ollama** | Text generation | CPU-friendly models |
| **Stable Diffusion** | Image generation | Requires decent GPU |

### 💰 Finance
| Service | Purpose | Why Self-Host |
|---------|---------|---------------|
| **Firefly III** | Personal finance | Your money data stays yours |

### 🌐 Web Interfaces
| Service | Purpose | Access |
|---------|---------|--------|
| **Homepage** | Service dashboard | `home.your-domain.com` |
| **Media Hub** | Media recommendations | Custom interface |

---

## 🚀 Quick Start

1. **Get Hardware**: Snag an old office PC or use what you have
2. **Install Arch**: Because we're not casuals here
2. **Setup Docker**: The foundation of everything
3. **Configure Cloudflare**: Buy a domain, set up DNS  
4. **Setup NAS**: Orange Pi 5 with OpenMediaVault
5. **Configure Network**: D-Link managed switch for proper VLANs
6. **Deploy Services**: Start with Jellyfin + arr stack, expand gradually
7. **Secure Everything**: UFW, Fail2Ban, proper authentication

*Detailed setup guides coming soon™*

---

## 🌟 Why Self-Host?

- **Privacy**: Your data never leaves your hardware
- **Control**: No sudden feature removals or price hikes
- **Learning**: Become a networking wizard
- **Bragging Rights**: "I host my own Netflix" hits different
- **Cost Effective**: One-time hardware cost vs endless subscriptions
- **Reliability**: No "service temporarily unavailable" messages

---

## ⚠️ Fair Warning

This isn't for everyone. You'll need:
- Patience for troubleshooting
- Willingness to learn Linux
- Comfort with breaking (and fixing) things
- Basic networking knowledge
- Time for maintenance

If you want plug-and-play, stick with the cloud services. If you want to own your digital life, welcome aboard! 🚢

---

## 🤝 Contributing

Found a better way to do something? Open an issue! 
Discovered a security flaw? Please let me know responsibly.
Want to share your setup? I'd love to see it!

---

## 📚 Resources

- [Arch Wiki](https://wiki.archlinux.org/) - Your new best friend
- [Docker Documentation](https://docs.docker.com/) - Container mastery
- [Cloudflare Docs](https://developers.cloudflare.com/) - Domain & DNS magic
- [r/selfhosted](https://reddit.com/r/selfhosted) - Community wisdom

---

**Remember**: *"I use Debian btw"* doesn't have the same ring to it, but your uptime will thank you. 😎

### 🎯 Pro Tips for Success

1. **Start Small**: Don't deploy everything at once, you'll hate troubleshooting
2. **Document Everything**: Future you will curse present you for not taking notes
3. **Backup Strategy**: 3-2-1 rule - 3 copies, 2 different media, 1 offsite
4. **Security First**: Change default passwords, use 2FA, keep things updated
5. **Monitor Resources**: Know when you're hitting limits before things crash
6. **Test Restores**: Backups are useless if you can't restore them
7. **Network Isolation**: VLANs aren't just for enterprises anymore

---

*P.S. - Yes, this setup runs my Rei Ayanami fanfiction archive. No, you can't have access. Some things are sacred. And yes, I switched to Debian AND found amazing Singapore deals on Shopee/Carousell. Character growth is real.*

### 🛒 **Singapore Shopping Pro Tips**
- **Carousell**: Best deals on used business PCs AND networking gear (that $30 switch though! 🔥)
- **Shopee**: Great for SBC kits like Orange Pi, watch for flash sales
- **Sim Lim Square**: Components and cables, but Carousell might be cheaper
- **Lazada**: Backup option if Shopee stock runs out
- **Local Facebook Groups**: Sometimes hidden gems in tech swap groups
- **Pro Tip**: Check Carousell notifications daily - good deals disappear fast!
