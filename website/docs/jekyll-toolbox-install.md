---
layout: default
title: The Ultimate Guide to Atomic Linux Distributions
---

# Installing Jekyll on an Immutable Linux Distribution (e.g., Aurora Linux/Fedora Silverblue) with Toolbox

This guide provides a robust method for installing and using Jekyll on an immutable Linux distribution, such as Aurora Linux (assuming it's based on Fedora Silverblue/Kinoite) or any Fedora-based system using Toolbox. It utilizes `rbenv` for Ruby version management within the Toolbox container.

**Assumptions:**

*   You are using an immutable Linux distribution that supports Toolbox.
*   You have Toolbox installed and configured.
*   You have a basic understanding of the command line.

**Steps:**

1.  **Enter Toolbox:**

    ```bash
    toolbox enter
    ```

2.  **Install Development Dependencies:**

    ```bash
    sudo dnf install -y @development-tools zlib-devel libyaml-devel openssl-devel readline-devel bzip2-devel autoconf automake libtool bison sqlite-devel
    ```
    *   `@development-tools`: Installs essential build tools (compiler, etc.).
    *   `-devel` packages: Provide headers and libraries for compiling Ruby.

3.  **Install rbenv:**

    ```bash
    git clone https://github.com/rbenv/rbenv.git ~/.rbenv
    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
    echo 'eval "$(rbenv init - bash)"' >> ~/.bashrc
    source ~/.bashrc
    ```
    *   Clones rbenv into your home directory.
    *   Configures your shell to use rbenv.

4.  **Install ruby-build (rbenv plugin):**

    ```bash
    git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
    ```

5.  **Install a Ruby Version:**

    ```bash
    rbenv install --list  # (Optional) List available Ruby versions
    rbenv install 3.2.2  # Replace with your desired (or latest stable) Ruby version
    rbenv global 3.2.2  # Set as the default for this Toolbox
    ```

6.  **Verify Ruby Installation:**

    ```bash
    ruby -v
    gem -v
    ```

7.  **Install Bundler:**

    ```bash
    gem install bundler
    ```

8.  **Install Jekyll and Create a New Site (Option 1: Inside a Git Repo Subdirectory):**

    This is the recommended approach if you have an existing Git repository and want to add a Jekyll site *within* it.

    ```bash
    cd /path/to/your/git/repo  # Replace with the path to your Git repository
    jekyll new my-jekyll-site  # Create the site in a *new* subdirectory
    cd my-jekyll-site        # Navigate into the new Jekyll site directory
    bundle install           # Install project dependencies
    bundle exec jekyll serve  # Start the development server
    ```
    *   Replace `my-jekyll-site` with your desired site name.

9. **Install Jekyll and Create a New Site (Option 2: Outside a Git Repo):**

    This creates a completely independent Jekyll site.

    ```bash
    cd ~  # Go to your home directory
    jekyll new my-new-site
    cd my-new-site
    bundle install
    bundle exec jekyll serve
    ```
10. **Install Jekyll in an existing project, but preserve git:**
    ```bash
    cd /var/home/rhuze/GitIt/Atomic-Linux-Docs
    jekyll new --blank --skip-bundle .
    git add .
    git commit -m "Initial commit after jekyll init"
    bundle install
    bundle exec jekyll serve
    ```

**Important Notes and Best Practices:**

*   **`bundle exec`:** *Always* use `bundle exec` before running Jekyll commands (e.g., `bundle exec jekyll serve`, `bundle exec jekyll build`). This ensures you're using the gems specified in your project's `Gemfile`.
*   **`Gemfile`:**  The `Gemfile` and `Gemfile.lock` files in your Jekyll project are critical. They define your project's dependencies.
*   **Project Location:**  Choose a location for your Jekyll project within your home directory *inside* the Toolbox.  You can mount directories from the host if needed.
*   **Toolbox Persistence:** rbenv configuration is automatically persistent across Toolbox sessions because it's added to `~/.bashrc`.
*   **Troubleshooting:**
    *   If you encounter errors, carefully read the error messages.
    *   Ensure `g++` is available ( `which g++` ) after installing `@development-tools`. If not, exit and re-enter Toolbox.
    *   For package issues, try `sudo dnf clean all` and `sudo dnf makecache`.
* **Multiple Toolboxes:** For complete isolation, create different Toolbox containers for different projects.


