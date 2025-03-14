---
layout: default
title: "Setting up GitHub Pages with Jekyll"
permalink: /docs/GitHub-Pages-Jekyll/
---

# Jekyll & GitHub Pages Content Management Cheat Sheet

## Setting Up Your Environment

### On Fedora/Red Hat Systems
```bash
# Install Ruby and development tools
sudo dnf install ruby ruby-devel openssl-devel @development-tools

# Install Bundler
gem install bundler

# Clone your repository
git clone https://github.com/[username]/[repository].git
cd [repository]

# Install dependencies
bundle install
```

### On Immutable Linux Systems (Silverblue, Kinoite, etc.)

#### Method 1: Using Toolbox
```bash
# Create a toolbox for Jekyll development
toolbox create jekyll-tools
toolbox enter jekyll-tools

# Install required packages inside toolbox
sudo dnf install ruby ruby-devel openssl-devel @development-tools

# Install Bundler
gem install bundler
```

#### Method 2: Using Homebrew
```bash
# Install Homebrew (if not already installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> ~/.bashrc
source ~/.bashrc

# Install Ruby with Homebrew
brew install ruby

# Add Ruby to your PATH
echo 'export PATH="$(brew --prefix ruby)/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Install Bundler
gem install bundler
```

After either method, clone the repository and install dependencies:
```bash
# Clone the repository
git clone https://github.com/[username]/[repository].git
cd [repository]

# Install dependencies
bundle install
```

## Creating New Content

### Creating a New Blog Post

1. **Create a new file in the `_posts` directory**
   ```bash
   # File must be named: YYYY-MM-DD-title.md
   # Example: 2025-03-15-setting-up-jellyfin.md
   ```

2. **Add front matter at the top of the file**
   ```yaml
   ---
   layout: default
   title: "Your Post Title"
   date: 2025-03-15 10:30:00 -0500
   categories: linux tutorials
   ---
   ```

3. **Add your content below the front matter**
   ```markdown
   Write your post content here using Markdown.

   ## Subheading

   - List item 1
   - List item 2

   [Link text](https://example.com)
   ```

### Creating a New Documentation Page

1. **Create a new file in the `docs` directory**
   ```bash
   # Example: docs/new-guide.md
   ```

2. **Add front matter**
   ```yaml
   ---
   layout: default
   title: "New Guide Title"
   permalink: /docs/new-guide/
   ---
   ```

3. **Add your content below the front matter**

## Updating Existing Content

1. **Find the file to edit**
   ```bash
   # Posts are in _posts directory
   # Documentation pages are in docs directory
   ```

2. **Make your changes to the file**

3. **Test your changes locally**
   ```bash
   bundle exec jekyll serve
   # Visit http://localhost:4000 in your browser
   ```

## Publishing Your Changes

1. **Add your changes to git**
   ```bash
   git add .
   ```

2. **Commit your changes with a descriptive message**
   ```bash
   git commit -m "Add new guide about Jellyfin installation"
   ```

3. **Push your changes to GitHub**
   ```bash
   git push origin main
   ```

4. **Wait for your site to rebuild**
   GitHub Pages will automatically rebuild your site (usually takes 1-5 minutes)

## Common Front Matter Options

```yaml
---
layout: default       # The layout template to use
title: "Page Title"   # The title of the page
permalink: /custom-url/ # Custom URL for the page
date: 2025-03-15      # Publication date (for posts)
categories: category1 category2 # Categories (for posts)
tags: tag1 tag2       # Tags (for posts)
published: true       # Set to false to hide a post/page
excerpt: "Summary text shown in feed" # Custom excerpt
---
```

## Adding Images

1. **Place image files in the `assets/images` directory**

2. **Reference images in your Markdown**
   ```markdown
   ![Alt text](/assets/images/my-image.jpg)
   ```

## Creating Special Content Elements

### Frustration Meter
```html
<div class="frustration-meter">
    <div class="frustration-label">Frustration Level:</div>
    <div class="frustration-level">
        <div class="frustration-fill frustration-medium"></div>
    </div>
</div>
```

### Tip Box
```html
<div class="tip-box">
    <strong>Pro Tip:</strong> This is a helpful tip for users.
</div>
```

### Reality Check
```html
<div class="reality-check">
    <strong>Reality Check:</strong> Important warning or limitation to be aware of.
</div>
```

## Working with RSS Feeds

Your site is configured to automatically generate an RSS feed at `/feed.xml`. This happens whenever you build the site or push to GitHub.

To customize RSS settings, edit your `_config.yml`:

```yaml
feed:
  title: Please Don't Make Me Install Linux
  description: A guide for the reluctant, the confused, and those looking for practical solutions.
  path: feed.xml
```

## Helpful Commands

```bash
# Start local server
bundle exec jekyll serve

# Build site without serving
bundle exec jekyll build

# Start server with draft posts visible
bundle exec jekyll serve --drafts

# See list of installed plugins
bundle info --bundled

# Update all dependencies
bundle update

# Check for outdated dependencies
bundle outdated
```

## Troubleshooting Common Issues

### Ruby Version Issues
If you encounter Ruby version errors, check your Ruby version:
```bash
ruby -v
```

For Fedora/RHEL systems, you might need to use `dnf` to install a specific version:
```bash
sudo dnf install ruby-2.7.0
```

### Jekyll Build Errors
If Jekyll fails to build, check:
1. YAML syntax in front matter (indentation matters)
2. Liquid template syntax errors
3. Missing dependencies in the Gemfile

### Permissions Issues
On Fedora/RHEL systems, you might need to adjust permissions:
```bash
# Fix gem installation permissions
mkdir -p ~/.gem/ruby
echo 'GEM_HOME=$HOME/.gem/ruby' >> ~/.bashrc
echo 'PATH=$HOME/.gem/ruby/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```
