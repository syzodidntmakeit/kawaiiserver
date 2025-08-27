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

### Hardware Recommendations

| Component | Minimum Spec | Recommended | Why |
|-----------|-------------|-------------|-----|
| **CPU** | Intel i5-8500 / AMD Ryzen 5 3600 | Intel i7-8700 / AMD Ryzen 7 3700X | Media transcoding & AI workloads |
| **RAM** | 16GB DDR4 | 32GB DDR4 | Containers are hungry |
| **Storage** | 256GB SSD + 2TB HDD | 512GB NVMe + 4TB HDD | OS on SSD, media on HDD |
| **Form Factor** | SFF (Small Form Factor) | Tower | Upgradability matters |
| **GPU** *(Optional)* | - | RTX 3060 / RX 6800 | Local AI models |

### Suggested Hardware (Tested Configs)

| Model | CPU | RAM | Storage | Est. Price |
|-------|-----|-----|---------|------------|
| Dell OptiPlex 5070 SFF | i5-9500 (6C/6T) | 16GB | 512GB NVMe + 1TB SSD | ~$300 |
| HP EliteDesk 800 G5 Mini | i5-9500T (6C/6T) | 16GB | 256GB NVMe + 512GB HDD | ~$238 |
| Lenovo ThinkCentre M920q | i5-9500T (6C/6T) | 16GB | 480GB SSD | ~$299 |

*Pro tip: Old office PCs are perfect for this. Let some corporation's depreciation be your gain.*

### Core Technologies

- **OS**: Arch Linux *(I use Arch btw)*
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
| **Calibre-Web** | eBook library | Because physical books are *so* last century |

### 🗂️ File Management
| Service | Purpose | Why This One |
|---------|---------|--------------|
| **Filebrowser** | File manager | server side files management , intuitive setup |
| **Nextcloud** | File sync & sharing | Your own Google Drive, but better |

### 🏴‍☠️ Content Acquisition *(arr Stack)*
| Service | Purpose | Integration |
|---------|---------|-------------|
| **qBittorrent** | Torrent client | Via Gluetun VPN for safety |
| **Prowlarr** | Indexer manager | Connects to all *arr services |
| **Sonarr** | TV show automation | → Jellyfin |
| **Radarr** | Movie automation | → Jellyfin |
| **Lidarr** | Music automation | → Navidrome |
| **Readarr** | Book automation | → Calibre |
| **Bazarr** | Subtitle management | → Media servers |

### 🛠️ Utility Services
| Service | Purpose | Note |
|---------|---------|------|
| **Vaultwarden** | Password manager | Bitwarden-compatible, self-hosted |
| **Portainer** | Docker management | When things go wrong (they will) |
| **Watchtower** | Auto-updates | Keep containers fresh |
| **Uptime Kuma** | Service monitoring | Pretty green/red status lights |

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
| **Media Hub** | Media recommendations | `review.your-domain.com` |

---

## 🚀 Quick Start

1. **Get Hardware**: Snag an old office PC or use what you have
2. **Install Arch**: Because we're not casuals here
3. **Setup Docker**: The foundation of everything
4. **Configure Cloudflare**: Buy a domain, set up DNS
5. **Deploy Services**: Start with essentials, expand gradually
6. **Secure Everything**: UFW, Fail2Ban, proper authentication

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

**Remember**: *"I use Arch btw"* is not just a meme, it's a way of life. 😎

---

*P.S. - Yes, this setup runs my Rei Ayanami fanfiction archive. No, you can't have access. Some things are sacred.*
