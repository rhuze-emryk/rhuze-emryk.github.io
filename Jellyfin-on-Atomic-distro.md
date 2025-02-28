---
layout: default
title: "Jellyfin on Atomic Linux"
date: 2025-02-27
---

<h1>Setting Up Jellyfin Media Server on Fedora Kinoite</h1>

<div class="intro-note">
    <p>
        Want your own personal Netflix without dealing with traditional Linux server setup? 
        This guide walks you through setting up Jellyfin media server on Fedora Kinoite (or any Atomic desktop) 
        using Podman containers—making it easier to maintain and less likely to break your system.
    </p>
</div>

<section>
    <h2>What You'll Need</h2>
    <ul>
        <li>Fedora Kinoite, Silverblue, or another Atomic desktop (version 40+ recommended)</li>
        <li>Basic terminal familiarity (don't worry, we'll explain each command)</li>
        <li>Some media files you want to stream</li>
        <li>A bit of patience (but less than you'd need with a traditional distro!)</li>
    </ul>
</section>

<section>
    <h2>Step 1: Setting Up Your Development Environment</h2>
    <div class="frustration-meter">
        <div class="frustration-label">Frustration Level:</div>
        <div class="frustration-level">
            <div class="frustration-fill frustration-low"></div>
        </div>
    </div>

    <pre><code># Create a new toolbox container
toolbox create jellyfin

# Enter your new container
toolbox enter jellyfin

# Install essential tools
sudo dnf install -y podman-compose vim git curl wget
exit</code></pre>

    <div class="tip-box">
        <strong>What's happening:</strong> We're creating a disposable environment that won't affect your core system.
    </div>
</section>

<section>
    <h2>Step 2: Creating Directory Structure</h2>
    <div class="frustration-meter">
        <div class="frustration-label">Frustration Level:</div>
        <div class="frustration-level">
            <div class="frustration-fill frustration-low"></div>
        </div>
    </div>

    <pre><code># Create configuration directories
mkdir -p ~/jellyfin/{config,cache}
mkdir -p ~/media/{tvshows,movies,music}</code></pre>

    <div class="reality-check">
        <strong>Pro Tip:</strong> Keep these in your home directory to avoid permission headaches!
    </div>
</section>

<section>
    <h2>Step 3: SELinux Configuration</h2>
    <div class="frustration-meter">
        <div class="frustration-label">Frustration Level:</div>
        <div class="frustration-level">
            <div class="frustration-fill frustration-medium"></div>
        </div>
    </div>

    <pre><code># Apply SELinux contexts
sudo semanage fcontext -a -t container_file_t "/home/$USER/jellyfin(/.*)?"
sudo semanage fcontext -a -t container_file_t "/home/$USER/media(/.*)?"
sudo restorecon -Rvv ~/jellyfin ~/media</code></pre>
</section>

<section>
    <h2>Step 4: Podman Compose Configuration</h2>
    <div class="frustration-meter">
        <div class="frustration-label">Frustration Level:</div>
        <div class="frustration-level">
            <div class="frustration-fill frustration-medium"></div>
        </div>
    </div>

    <p>Create <code>~/jellyfin/podman-compose.yml</code>:</p>
    
    <pre><code>version: '3.8'
services:
  jellyfin:
    image: docker.io/jellyfin/jellyfin:latest
    container_name: jellyfin
    user: "$(id -u):$(id -g)"
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
    restart: unless-stopped</code></pre>
</section>

<section>
    <h2>Step 5: Deployment & Automation</h2>
    <div class="frustration-meter">
        <div class="frustration-label">Frustration Level:</div>
        <div class="frustration-level">
            <div class="frustration-fill frustration-medium"></div>
        </div>
    </div>

    <pre><code># Start Jellyfin
podman-compose -f ~/jellyfin/podman-compose.yml up -d

# Configure firewall
sudo firewall-cmd --permanent --add-port=8096/tcp
sudo firewall-cmd --permanent --add-port=8920/tcp
sudo firewall-cmd --reload

# Create systemd service (run outside toolbox!)
podman generate systemd --new --name jellyfin > ~/.config/systemd/user/jellyfin.service
systemctl --user enable --now jellyfin.service</code></pre>

    <div class="reality-check">
        <strong>Common Mistake:</strong> Running systemd commands inside toolbox will fail!
    </div>
</section>

<section>
    <h2>Step 6: Verification</h2>
    <div class="frustration-meter">
        <div class="frustration-label">Frustration Level:</div>
        <div class="frustration-level">
            <div class="frustration-fill frustration-low"></div>
        </div>
    </div>

    <pre><code># Check container status
podman ps

# View logs
podman logs jellyfin

# Test access
curl http://localhost:8096</code></pre>
</section>

<section>
    <h2>Step 7: Maintenance</h2>
    <div class="frustration-meter">
        <div class="frustration-label">Frustration Level:</div>
        <div class="frustration-level">
            <div class="frustration-fill frustration-low"></div>
        </div>
    </div>

    <pre><code># Update container
podman-compose -f ~/jellyfin/podman-compose.yml pull
podman-compose -f ~/jellyfin/podman-compose.yml up -d

# Cleanup images
podman image prune -a</code></pre>
</section>

<section>
    <h2>Troubleshooting Guide</h2>
    
    <div class="tip-box">
        <h3>Permission Issues</h3>
        <pre><code>podman unshare chown -R $(id -u):$(id -g) ~/jellyfin</code></pre>
    </div>

    <div class="tip-box">
        <h3>SELinux Diagnostics</h3>
        <pre><code>sudo ausearch -m avc -ts recent | audit2why</code></pre>
    </div>

    <div class="reality-check">
        <h3>Nuclear Option</h3>
        <pre><code>podman-compose -f ~/jellyfin/podman-compose.yml down
rm -rf ~/jellyfin/config/*</code></pre>
    </div>
</section>

<footer>
    <p>© 2025 Emryk™ - Licensed under <a href="https://creativecommons.org/licenses/by/4.0/">CC BY 4.0</a></p>
</footer>
