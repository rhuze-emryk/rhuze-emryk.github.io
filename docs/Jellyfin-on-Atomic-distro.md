# Jellyfin on Fedora Kinoite via Podman

## Prerequisites
- Fedora Kinoite/Silverblue (40+ recommended)
- Basic terminal familiarity

## 1. Toolbox Setup
```bash
# Create persistent development environment
toolbox create --container fedora-toolbox-40
toolbox enter fedora-toolbox-40

# Install essential packages (inside toolbox)
sudo dnf install -y podman-compose vim git curl wget

# Exit toolbox when done
exit
```

## 2. Directory Structure
```bash
# Create directories in your home folder
mkdir -p ~/jellyfin/{config,cache}
mkdir -p ~/media/{tvshows,movies,music}

# Verify structure
tree ~/jellyfin ~/media -L 2
```

## 3. Permissions & SELinux Configuration
```bash
# Apply SELinux contexts (replace 'user' with your username)
sudo semanage fcontext -a -t container_file_t "/home/user/jellyfin(/.*)?"
sudo semanage fcontext -a -t container_file_t "/home/user/media(/.*)?"
sudo restorecon -Rvv ~/jellyfin ~/media

# Verify contexts
ls -Z ~/jellyfin
ls -Z ~/media
```

## 4. Podman-Compose File
Create `~/jellyfin/podman-compose.yml`:
```yaml
version: '3.8'
services:
  jellyfin:
    image: docker.io/jellyfin/jellyfin:latest
    container_name: jellyfin
    user: "1000:1000"  # Run as your user (id -u && id -g)
    volumes:
      - ~/jellyfin/config:/config
      - ~/jellyfin/cache:/cache
      - ~/media:/media:ro
    ports:
      - 8096:8096
      - 8920:8920
    environment:
      - TZ=America/New_York
      - JELLYFIN_PublishedServerUrl=http://your-server-ip:8096
    restart: unless-stopped
    security_opt:
      - label=disable
```

## 5. Service Management
```bash
# Start Jellyfin
podman-compose -f ~/jellyfin/podman-compose.yml up -d

# Enable firewall
sudo firewall-cmd --permanent --add-port=8096/tcp
sudo firewall-cmd --permanent --add-port=8920/tcp
sudo firewall-cmd --reload

# Create systemd service (run outside toolbox!)
podman generate systemd --new --name jellyfin > ~/.config/systemd/user/jellyfin.service

# Enable auto-start
systemctl --user enable --now jellyfin.service
```

## 6. Verification
```bash
# Check container status
podman ps

# View logs
podman logs jellyfin

# Test access
curl http://localhost:8096
```

## 7. Maintenance
```bash
# Update container
podman-compose -f ~/jellyfin/podman-compose.yml pull
podman-compose -f ~/jellyfin/podman-compose.yml up -d

# Cleanup old images
podman image prune -a
```

## Key Notes for Kinoite
- **Immutable Advantage**: Containers persist across reboots but won't affect base OS
- **Storage**: All data lives in your home directory
- **Hardware Acceleration**: Add GPU device mappings if needed:
  ```yaml
  devices:
    - /dev/dri/renderD128:/dev/dri/renderD128
  ```
- **Backups**: Regularly backup `~/jellyfin/config`

## Troubleshooting
```bash
# Permission issues
podman unshare chown -R 1000:1000 ~/jellyfin

# SELinux denials
sudo ausearch -m avc -ts recent | audit2why

# Full reset
podman-compose down -v
rm -rf ~/jellyfin/config/*
```

Access Jellyfin at `http://localhost:8096` or `http://your-server-ip:8096`
