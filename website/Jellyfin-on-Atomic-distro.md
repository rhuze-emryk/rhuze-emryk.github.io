---
layout: default
title: "Jellyfin on Atomic Linux"
date: 2025-02-27
---

# Setting Up Jellyfin Media Server on Fedora Kinoite

<div class="intro-note">
    <p>
        Want your own personal Netflix without dealing with traditional Linux server setup?
        This guide walks you through setting up Jellyfin media server on Fedora Kinoite (or any Atomic desktop)
        using Podman containers—making it easier to maintain and less likely to break your system.
    </p>
</div>

## What You'll Need

- Fedora Kinoite, Silverblue, or another Atomic desktop (version 40+ recommended)
- Basic terminal familiarity (don't worry, we'll explain each command)
- Some media files you want to stream
- A bit of patience (but less than you'd need with a traditional distro!)

## Step 1: Setting Up Your Development Environment

<div class="frustration-meter">
    <div class="frustration-label">Frustration Level:</div>
    <div class="frustration-level">
        <div class="frustration-fill frustration-low"></div>
    </div>
</div>

~~~bash
# Create a new toolbox container
toolbox create jellyfin

# Enter your new container
toolbox enter jellyfin

# Install essential tools
sudo dnf install -y podman-compose vim git curl wget
exit
~~~

<div class="tip-box">
    <strong>What's happening:</strong> We're creating a disposable environment that won't affect your core system.
</div>

## Step 2: Creating Directory Structure

<div class="frustration-meter">
    <div class="frustration-label">Frustration Level:</div>
    <div class="frustration-level">
        <div class="frustration-fill frustration-low"></div>
    </div>
</div>

~~~bash
# Create configuration directories
mkdir -p ~/jellyfin/{config,cache}
mkdir -p ~/media/{tvshows,movies,music}
~~~

<div class="reality-check">
    <strong>Pro Tip:</strong> Keep these in your home directory to avoid permission headaches!
</div>

## Step 3: SELinux Configuration

<div class="frustration-meter">
    <div class="frustration-label">Frustration Level:</div>
    <div class="frustration-level">
        <div class="frustration-fill frustration-medium"></div>
    </div>
</div>

~~~bash
# Apply SELinux contexts
sudo semanage fcontext -a -t container_file_t "/home/$USER/jellyfin(/.*)?"
sudo semanage fcontext -a -t container_file_t "/home/$USER/media(/.*)?"
sudo restorecon -Rvv ~/jellyfin ~/media
~~~

## Step 4: Podman Compose Configuration

<div class="frustration-meter">
    <div class="frustration-label">Frustration Level:</div>
    <div class="frustration-level">
        <div class="frustration-fill frustration-medium"></div>
    </div>
</div>

<p>Create <code>~/jellyfin/podman-compose.yml</code>:</p>

~~~yaml
version: '3.8'
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
    restart: unless-stopped
~~~

## Step 5: Deployment & Automation

<div class="frustration-meter">
    <div class="frustration-label">Frustration Level:</div>
    <div class="frustration-level">
        <div class="frustration-fill frustration-medium"></div>
    </div>
</div>

~~~bash
# Start Jellyfin
podman-compose -f ~/jellyfin/podman-compose.yml up -d

# Configure firewall
sudo firewall-cmd --permanent --add-port=8096/tcp
sudo firewall-cmd --permanent --add-port=8920/tcp
sudo firewall-cmd --reload

# Create systemd service (run outside toolbox!)
podman generate systemd --new --name jellyfin > ~/.config/systemd/user/jellyfin.service
systemctl --user enable --now jellyfin.service
~~~

<div class="reality-check">
    <strong>Common Mistake:</strong> Running systemd commands inside toolbox will fail!
</div>

## Step 6: Verification

<div class="frustration-meter">
    <div class="frustration-label">Frustration Level:</div>
    <div class="frustration-level">
        <div class="frustration-fill frustration-low"></div>
    </div>
</div>

~~~bash
# Check container status
podman ps

# View logs
podman logs jellyfin

# Test access
curl http://localhost:8096
~~~

## Step 7: Maintenance

<div class="frustration-meter">
    <div class="frustration-label">Frustration Level:</div>
    <div class="frustration-level">
        <div class="frustration-fill frustration-low"></div>
    </div>
</div>

~~~bash
# Update container
podman-compose -f ~/jellyfin/podman-compose.yml pull
podman-compose -f ~/jellyfin/podman-compose.yml up -d

# Cleanup images
podman image prune -a
~~~

## Troubleshooting Guide

<div class="tip-box">
    ### Permission Issues
    ~~~bash
    podman unshare chown -R $(id -u):$(id -g) ~/jellyfin
    ~~~
</div>

<div class="tip-box">
    ### SELinux Diagnostics
    ~~~bash
    sudo ausearch -m avc -ts recent | audit2why
    ~~~
</div>

<div class="reality-check">
    ### Nuclear Option
    ~~~bash
    podman-compose -f ~/jellyfin/podman-compose.yml down
    rm -rf ~/jellyfin/config/*
    ~~~
</div>

<footer>
    <p>© 2025 Emryk™ - Licensed under <a href="https://creativecommons.org/licenses/by/4.0/">CC BY 4.0</a></p>
</footer>
