<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jellyfin on Fedora Kinoite | Please Don't Make Me Install Linux</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;500;600;700&family=Literata:ital,wght@0,400;0,600;0,700;1,400;1,600&display=swap');
        
        :root {
            --background: #f8f5f0;
            --text-color: #3a2f28;
            --heading-color: #c45d3c;
            --accent-color: #5f9ea5;
            --button-color: #d9a566;
            --button-hover: #c9884a;
            --border-color: #e0d8cc;
            --card-bg: #ffffff;
            --code-bg: #2d2d2d;
            --code-color: #f8f8f2;
            --font-main: 'Outfit', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            --font-heading: 'Literata', Georgia, serif;
            --font-code: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, monospace;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: var(--font-main);
            line-height: 1.7;
            color: var(--text-color);
            background-color: var(--background);
            max-width: 800px;
            margin: 0 auto;
            padding: 40px 20px;
        }

        .nav-header {
            display: flex;
            align-items: center;
            gap: 20px;
            margin-bottom: 2rem;
        }

        .nav-button {
            padding: 8px 16px;
            background-color: transparent;
            color: var(--text-color);
            border: 2px solid var(--button-color);
            text-decoration: none;
            font-size: 0.95rem;
            transition: all 0.3s ease;
            border-radius: 6px;
            font-weight: 500;
        }

        .nav-button:hover {
            background-color: var(--button-color);
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }

        h1 {
            font-family: var(--font-heading);
            font-size: 2.4rem;
            margin-bottom: 1.5rem;
            color: var(--heading-color);
            font-weight: 700;
            line-height: 1.2;
        }

        h2 {
            font-family: var(--font-heading);
            font-size: 1.8rem;
            margin-top: 2rem;
            margin-bottom: 1rem;
            color: var(--heading-color);
            font-weight: 600;
            line-height: 1.3;
            padding-bottom: 0.3rem;
            border-bottom: 1px solid var(--border-color);
        }

        h3 {
            font-family: var(--font-heading);
            font-size: 1.3rem;
            margin-top: 1.5rem;
            margin-bottom: 0.8rem;
            color: var(--text-color);
            font-weight: 600;
        }

        p {
            font-size: 1.1rem;
            margin-bottom: 1rem;
        }

        ul, ol {
            margin-bottom: 1.5rem;
            margin-left: 2rem;
        }

        li {
            margin-bottom: 0.5rem;
        }

        a {
            color: var(--accent-color);
            text-decoration: underline;
            text-decoration-thickness: 1px;
            text-underline-offset: 2px;
            transition: color 0.2s ease;
        }

        a:hover {
            color: var(--heading-color);
            text-decoration: none;
        }

        pre {
            background-color: var(--code-bg);
            color: var(--code-color);
            padding: 1rem;
            margin: 1rem 0 1.5rem 0;
            border-radius: 8px;
            overflow-x: auto;
            font-family: var(--font-code);
            font-size: 0.9rem;
            line-height: 1.5;
        }

        code {
            font-family: var(--font-code);
            background-color: rgba(0,0,0,0.05);
            padding: 2px 4px;
            border-radius: 3px;
            font-size: 0.9em;
        }

        pre code {
            background-color: transparent;
            padding: 0;
            border-radius: 0;
            font-size: 1em;
        }

        .intro-note {
            background-color: rgba(95, 158, 165, 0.1);
            border-radius: 8px;
            padding: 1.5rem;
            margin: 1.5rem 0 2rem;
        }

        .tip-box {
            background-color: rgba(217, 165, 102, 0.15);
            border-radius: 8px;
            padding: 1.5rem;
            margin: 1.5rem 0;
        }

        .tip-box strong {
            color: var(--heading-color);
        }

        .reality-check {
            background-color: rgba(196, 93, 60, 0.08);
            border-radius: 8px;
            padding: 1.5rem;
            margin: 1.5rem 0;
            border-left: 5px solid var(--heading-color);
        }

        .step-container {
            margin-bottom: 3rem;
        }

        .frustration-meter {
            display: flex;
            align-items: center;
            gap: 10px;
            margin: 1rem 0;
        }

        .frustration-label {
            font-weight: 500;
            min-width: 180px;
        }

        .frustration-level {
            height: 12px;
            border-radius: 6px;
            background-color: var(--border-color);
            width: 200px;
            position: relative;
        }

        .frustration-fill {
            position: absolute;
            left: 0;
            top: 0;
            height: 100%;
            border-radius: 6px;
            background-color: var(--heading-color);
        }

        .frustration-low {
            width: 25%;
        }

        .frustration-medium {
            width: 50%;
        }

        .frustration-high {
            width: 75%;
        }

        .frustration-extreme {
            width: 100%;
        }

        .note {
            background-color: var(--card-bg);
            padding: 1rem;
            margin: 1.5rem 0;
            border-left: 4px solid var(--accent-color);
            border-radius: 0 8px 8px 0;
        }

        footer {
            margin-top: 3rem;
            font-size: 0.95rem;
            color: #78655d;
            padding-top: 1.5rem;
            border-top: 1px solid var(--border-color);
        }

        @media (max-width: 600px) {
            h1 {
                font-size: 2rem;
            }
            
            h2 {
                font-size: 1.6rem;
            }
            
            p {
                font-size: 1rem;
            }
            
            pre {
                padding: 0.8rem;
                font-size: 0.8rem;
            }
        }
    </style>
</head>
<body>
    <header>
        <div class="nav-header">
            <a href="../" class="nav-button">Home</a>
            <a href="./" class="nav-button">Survival Guides</a>
        </div>
    </header>

    <main>
        <h1>Setting Up Jellyfin Media Server on Fedora Kinoite</h1>

        <div class="intro-note">
            <p>
                Want your own personal Netflix without dealing with traditional Linux server setup? 
                This guide walks you through setting up Jellyfin media server on Fedora Kinoite (or any Atomic desktop) 
                using Podman containers—making it easier to maintain and less likely to break your system.
            </p>
        </div>

        <h2>What You'll Need</h2>
        <ul>
            <li>Fedora Kinoite, Silverblue, or another Atomic desktop (version 40+ recommended)</li>
            <li>Basic terminal familiarity (don't worry, we'll explain each command)</li>
            <li>Some media files you want to stream</li>
            <li>A bit of patience (but less than you'd need with a traditional distro!)</li>
        </ul>

        <div class="step-container">
            <h2>Step 1: Setting Up Your Development Environment</h2>
            
            <div class="frustration-meter">
                <div class="frustration-label">Frustration Level:</div>
                <div class="frustration-level">
                    <div class="frustration-fill frustration-low"></div>
                </div>
            </div>
            
            <p>
                One of the first things you'll notice about Atomic distros is that you can't just install tools 
                directly into the system. Instead, we use something called Toolbox, which creates a container where 
                you can install all the development tools you need without affecting your main system.
            </p>

            <pre><code># Create a new toolbox container called "jellyfin"
toolbox create jellyfin

# Enter your new container
toolbox enter jellyfin

# Install the tools we'll need (inside the toolbox)
sudo dnf install -y podman-compose vim git curl wget

# When you're done, you can exit the toolbox
exit</code></pre>

            <div class="tip-box">
                <strong>What's happening here:</strong> We're creating a separate environment where we can install 
                development tools without modifying the immutable core system. It's like having a sandbox to play in—if 
                something breaks, it won't affect the rest of your computer.
            </div>
        </div>

        <div class="step-container">
            <h2>Step 2: Creating Your Directory Structure</h2>
            
            <div class="frustration-meter">
                <div class="frustration-label">Frustration Level:</div>
                <div class="frustration-level">
                    <div class="frustration-fill frustration-low"></div>
                </div>
            </div>
            
            <p>
                Now we need to set up the folders where Jellyfin will store its configuration and where 
                it will look for your media files.
            </p>

            <pre><code># Create directories in your home folder
mkdir -p ~/jellyfin/{config,cache}
mkdir -p ~/media/{tvshows,movies,music}

# Verify the structure looks right
tree ~/jellyfin ~/media -L 2</code></pre>

            <p>
                If you don't have the <code>tree</code> command installed, you might see an error. Don't worry—it's 
                just a convenient way to visualize the directory structure. You can skip it or install it in your toolbox.
            </p>

            <div class="reality-check">
                <strong>Personal note:</strong> The first time I did this, I tried to put these directories 
                in system locations like <code>/opt</code> or <code>/srv</code> and kept getting permission errors. 
                Keeping everything in your home directory is much simpler on an Atomic system!
            </div>
        </div>

        <div class="step-container">
            <h2>Step 3: Handling Permissions & SELinux</h2>
            
            <div class="frustration-meter">
                <div class="frustration-label">Frustration Level:</div>
                <div class="frustration-level">
                    <div class="frustration-fill frustration-medium"></div>
                </div>
            </div>
            
            <p>
                SELinux is a security feature that can sometimes get in the way if not properly configured. Let's set it up 
                correctly for our container:
            </p>

            <pre><code># Apply SELinux contexts (replace 'user' with your actual username)
sudo semanage fcontext -a -t container_file_t "/home/user/jellyfin(/.*)?"
sudo semanage fcontext -a -t container_file_t "/home/user/media(/.*)?"
sudo restorecon -Rvv ~/jellyfin ~/media

# Verify the contexts are applied correctly
ls -Z ~/jellyfin
ls -Z ~/media</code></pre>

            <div class="reality-check">
                <p>
                    <strong>When things go wrong:</strong> If your Jellyfin container keeps crashing or can't access files, 
                    SELinux is often the culprit. Look for error messages containing "permission denied" or "avc: denied" 
                    in the system logs.
                </p>
                <p>
                    If you're just testing and getting frustrated, you can temporarily set SELinux to permissive mode with 
                    <code>sudo setenforce 0</code>, but remember to turn it back on with <code>sudo setenforce 1</code> when 
                    you're done troubleshooting!
                </p>
            </div>
        </div>

        <div class="step-container">
            <h2>Step 4: Creating Your Podman Compose File</h2>
            
            <div class="frustration-meter">
                <div class="frustration-label">Frustration Level:</div>
                <div class="frustration-level">
                    <div class="frustration-fill frustration-medium"></div>
                </div>
            </div>
            
            <p>
                Now we'll create a configuration file that tells Podman how to run the Jellyfin container:
            </p>

            <p>Create a file called <code>~/jellyfin/podman-compose.yml</code> with the following content:</p>

            <pre><code>version: '3.8'
services:
  jellyfin:
    image: docker.io/jellyfin/jellyfin:latest
    container_name: jellyfin
    user: "1000:1000"  # Run as your user (use "id -u && id -g" to find your IDs)
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
      - label=disable</code></pre>

            <div class="tip-box">
                <p><strong>Customization options:</strong></p>
                <ul>
                    <li>Change <code>user: "1000:1000"</code> to match your user ID (find it with <code>id -u</code> and <code>id -g</code>)</li>
                    <li>Adjust <code>TZ=America/New_York</code> to your time zone</li>
                    <li>Replace <code>your-server-ip</code> with your computer's actual IP address or hostname</li>
                </ul>
            </div>
        </div>

        <div class="step-container">
            <h2>Step 5: Starting and Managing Jellyfin</h2>
            
            <div class="frustration-meter">
                <div class="frustration-label">Frustration Level:</div>
                <div class="frustration-level">
                    <div class="frustration-fill frustration-medium"></div>
                </div>
            </div>
            
            <p>
                Now let's start the Jellyfin container and configure it to start automatically when your computer boots:
            </p>

            <pre><code># Start Jellyfin
podman-compose -f ~/jellyfin/podman-compose.yml up -d

# Configure the firewall to allow access to Jellyfin
sudo firewall-cmd --permanent --add-port=8096/tcp
sudo firewall-cmd --permanent --add-port=8920/tcp
sudo firewall-cmd --reload

# Create a systemd service for auto-starting (run this outside toolbox!)
mkdir -p ~/.config/systemd/user/
podman generate systemd --new --name jellyfin > ~/.config/systemd/user/jellyfin.service

# Enable auto-start
systemctl --user enable --now jellyfin.service</code></pre>

            <div class="reality-check">
                <p>
                    <strong>When I messed this up:</strong> I initially ran the <code>podman generate systemd</code> command inside 
                    the toolbox, which created the service file in the wrong place. Remember to exit the toolbox before running this command!
                </p>
                <p>
                    Also, if you've previously run Jellyfin and it didn't shut down cleanly, you might get errors about the container 
                    already existing. Fix this with <code>podman rm jellyfin</code> before trying again.
                </p>
            </div>
        </div>

        <div class="step-container">
            <h2>Step 6: Verifying Your Setup</h2>
            
            <div class="frustration-meter">
                <div class="frustration-label">Frustration Level:</div>
                <div class="frustration-level">
                    <div class="frustration-fill frustration-low"></div>
                </div>
            </div>
            
            <p>
                Let's make sure everything is working correctly:
            </p>

            <pre><code># Check if the container is running
podman ps

# View container logs
podman logs jellyfin

# Test access to the Jellyfin web interface
curl http://localhost:8096</code></pre>

            <p>
                If everything is working, you should be able to access the Jellyfin web interface by opening a web browser 
                and navigating to <code>http://localhost:8096</code> or <code>http://your-server-ip:8096</code> from another 
                device on your network.
            </p>
        </div>

        <div class="step-container">
            <h2>Step 7: Ongoing Maintenance</h2>
            
            <div class="frustration-meter">
                <div class="frustration-label">Frustration Level:</div>
                <div class="frustration-level">
                    <div class="frustration-fill frustration-low"></div>
                </div>
            </div>
            
            <p>
                One of the benefits of using containers on an Atomic system is that updates are simple and safe:
            </p>

            <pre><code># Update the Jellyfin container
podman-compose -f ~/jellyfin/podman-compose.yml pull
podman-compose -f ~/jellyfin/podman-compose.yml up -d

# Clean up old container images to save space
podman image prune -a</code></pre>

            <p>
                You can update your Jellyfin container independently from your system updates, and if anything goes wrong, 
                you can easily roll back to the previous version.
            </p>
        </div>

        <h2>Key Benefits of Running Jellyfin This Way</h2>
        <ul>
            <li><strong>Isolation:</strong> The Jellyfin container is separate from your core system, so issues with it won't affect system stability</li>
            <li><strong>Persistence:</strong> The container and its configuration persist across system updates and reboots</li>
            <li><strong>Easier upgrades:</strong> You can update Jellyfin independently from your system updates</li>
            <li><strong>Rollback capability:</strong> If a Jellyfin update causes problems, you can easily roll back to the previous version</li>
        </ul>

        <h2>Troubleshooting Common Issues</h2>

        <h3>Permission Problems</h3>
        <pre><code># Fix permission issues
podman unshare chown -R 1000:1000 ~/jellyfin</code></pre>

        <h3>SELinux Denials</h3>
        <pre><code># Find and analyze SELinux denials
sudo ausearch -m avc -ts recent | audit2why</code></pre>

        <h3>Complete Reset</h3>
        <pre><code># Stop and remove Jellyfin
podman-compose -f ~/jellyfin/podman-compose.yml down
# Remove all configuration (be careful—this erases all settings!)
rm -rf ~/jellyfin/config/*</code></pre>

        <div class="note">
            <p>
                Access your Jellyfin media server at <a href="http://localhost:8096">http://localhost:8096</a> from your 
                local machine or <code>http://your-server-ip:8096</code> from other devices on your network.
            </p>
        </div>

        <div class="tip-box">
            <p>
                <strong>Hardware Acceleration:</strong> If you want to use GPU acceleration for video transcoding, you'll need to 
                add the appropriate device mappings to your <code>podman-compose.yml</code> file:
            </p>
            <pre><code>devices:
  - /dev/dri/renderD128:/dev/dri/renderD128</code></pre>
            <p>
                The exact device path might differ depending on your GPU. Intel GPUs typically use the path shown above,
                while AMD and NVIDIA GPUs might use different paths.
            </p>
        </div>
    </main>

    <footer>
        <p>© 2025 Emryk™ - Licensed under <a href="https://creativecommons.org/licenses/by/4.0/">CC BY 4.0</a> - Made with patience and occasional frustration.</p>
    </footer>
</body>
</html>
