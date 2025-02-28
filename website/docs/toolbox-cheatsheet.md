---
layout: default
title: The Ultimate Guide to Atomic Linux Distributions
permalink: /docs/toolbox-cheatsheet.html
---


# Toolbx Cheatsheet

A container management tool for Linux development environments using Podman/Docker. [Official Docs](https://containertoolbx.org/)

## Installation

```bash
# Fedora
sudo dnf install toolbox podman

# Ubuntu (20.04+)
sudo apt install toolbox podman

# Arch Linux
sudo pacman -S toolbox podman
```

## Basic Commands

| Command | Description |
|---------|-------------|
| `toolbox create --distro <name> <container-name>` | Create new container |
| `toolbox enter <container-name>` | Enter container shell |
| `toolbox list` | List containers |
| `toolbox rm <container-name>` | Delete container |
| `toolbox run <container-name> <command>` | Run command in container |
| `toolbox stop <container-name>` | Stop container |

## Podman/Docker Integration

### Custom Images
```bash
# From Docker Hub
toolbox create --image docker://alpine alpine-docker

# From Podman local image
toolbox create --image podman://localhost/my-image my-podman-container

# Build and use custom image
podman build -t my-toolbox-image .
toolbox create --image my-toolbox-image custom-container
```

### Manage Containers
```bash
# List Podman containers (including Toolbx)
podman ps -a

# Inspect Toolbx container
podman inspect <container-name>

# Access Podman from within Toolbx
toolbox enter my-container
podman --version
```

## Key Features

1. **Shared Home**: Host's `$HOME` mounted at `/home/$USER`
2. **Image Sources**:
   ```bash
   # Official images
   toolbox create --distro ubuntu --release 22.04

   # Custom registries
   toolbox create --image quay.io/centos/centos:stream9
   ```
3. **Storage Management**:
   ```bash
   # Show Podman storage
   podman system df

   # Cleanup unused images
   podman image prune
   ```

## Workflow Examples

**Create Dev Environment**
```bash
toolbox create --distro fedora --release 38 dev
toolbox enter dev
sudo dnf install @development-tools
```

**Run GUI App**
```bash
# Host machine first:
xhost +local:
toolbox run --container dev firefox
```

**Temporary Workspace**
```bash
toolbox create --ephemeral --image docker://alpine temp-env
toolbox enter temp-env
apk add git
# Container destroyed after exit
```

## Troubleshooting

**Permission Issues**
```bash
# Ensure subuid/subgid configured
sudo usermod --add-subuids 100000-165535 --add-subgids 100000-165535 $USER
```

**Reset Toolbx**
```bash
podman system reset  # WARNING: Deletes all containers/images
rm -rf ~/.local/share/containers/toolbox
```

**Check Backend**
```bash
# Verify Podman is default
toolbox --log-level debug create test-container | grep 'Using backend'
```

## Appendix: Podman vs Docker

| Feature              | Podman                           | Docker               |
|----------------------|----------------------------------|----------------------|
| Daemon               | Daemonless                       | Requires dockerd     |
| Root permissions     | Rootless mode supported          | Typically needs root |
| Toolbx integration   | Default backend                  | Works via socket     |
| Compose files        | `podman-compose`                 | `docker-compose`     |

---

> **Note**: Toolbx uses Podman by default. For Docker compatibility:  
> - Install `docker-compose` in containers when needed  
> - Use `--device` flags for hardware access  
> - Share Docker socket: `toolbox create --volume /var/run/docker.sock:/var/run/docker.sock`

