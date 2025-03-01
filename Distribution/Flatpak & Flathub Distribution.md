## Flatpak Basics

### What Is Flatpak?

Flatpak is a universal package format for Linux that:

- Works across ALL Linux distributions
- Includes all dependencies inside the package
- Isolates applications in sandboxes
- Appears in software centers like GNOME Software and KDE Discover

### Why Use Flatpak for Games?

Flatpak is PERFECT for game distribution because:

1. Players don't need to worry about dependencies
2. Your game will work the same on ALL Linux distros
3. Players can find your game in their app stores
4. Updates are seamless through Flathub

## Creating a Flatpak Package Manually

### Setting Up Flatpak on Arch

Install flatpak and builder:

``` bash
pacman -S flatpak flatpak-builder
```

Add Flathub repo:

``` bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

### Creating Your Flatpak Manifest

Create a file named `io.github.yourusername.yourgame.yml`:

``` yaml
app-id: io.github.yourusername.yourgame
runtime: org.freedesktop.Platform # Base dependencies
runtime-version: '23.08' # Version of the runtime
sdk: org.freedesktop.Sdk # Development tools
command: your-game # Executable name

# Sandbox permissions
finish-args:
  - --share=ipc
  - --socket=wayland
  - --socket=fallback-x11
  - --socket=pulseaudio
  - --device=dri # For GPU acceleration
  - --filesystem=xdg-documents # For save files

# Build instructions
modules:
  - name: your-game
    buildsystem: simple
    build-commands:
      # Install executable
      - install -D your-game /app/bin/your-game

      # Install game assets
      - mkdir -p /app/share/your-game
      - cp -r assets/* /app/share/your-game/

      # Install metadata
      - install -D icon.png /app/share/icons/hicolor/128x128/apps/io.github.yourusername.yourgame.png
      - install -D io.github.yourusername.yourgame.desktop /app/share/applications/io.github.yourusername.yourgame.desktop
      - install -D io.github.yourusername.yourgame.metainfo.xml /app/share/metainfo/io.github.yourusername.yourgame.metainfo.xml

    # Source files (binaries and assets)
    sources:
      - type: archive
        path: your-game-linux.tar.gz
      - type: file
        path: icon.png
      - type: file
        path: io.github.yourusername.yourgame.desktop
      - type: file
        path: io.github.yourusername.yourgame.metainfo.xml
```

### Required Metadata Files

#### Desktop File

`io.github.yourusername.yourgame.desktop`:

``` ini
[Desktop Entry]
Name=Your Game
Comment=Your awesome game description
Exec=your-game
Icon=io.github.yourusername.yourgame
Terminal=false
Type=Application
Categories=Game;
```

#### AppStream Metadata

`io.github.yourusername.yourgame.metainfo.xml`:

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<component type="desktop-application">
  <id>io.github.yourusername.yourgame</id>
  <name>Your Game</name>
  <summary>A short description of your game</summary>
  <description>
	<p>A longer description of your awesome game.</p>
	<p>Multiple paragraphs explaining features.</p>
  </description>
  <screenshots>
	<screenshot type="default">
	  <image>https://yourusername.github.io/yourgame/screenshot1.png</image>
	</screenshot>
  </screenshots>
  <url type="homepage">https://yourusername.github.io/yourgame</url>
  <developer_name>Your Name</developer_name>
  <releases>
	<release version="1.0.0" date="2025-02-28"/>
  </releases>
  <content_rating type="oars-1.1"/>
</component>
```

### Building Your Flatpak Locally

Build the flatpak:

``` bash
flatpak-builder --user --install --force-clean build-dir io.github.yourusername.yourgame.yml
```

Run it!

``` bash
flatpak run io.github.yourusername.yourgame
```

Create distributable package:

``` bash
flatpak-builder --repo=repo build-dir io.github.yourusername.yourgame.yml
flatpak build-bundle repo your-game.flatpak io.github.yourusername.yourgame
```

## Automating Flatpak Builds with GitHub Actions

Create `.github/workflows/flatpak.yml`:

``` yaml
name: Build Flatpak

on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  build-flatpak:
    runs-on: ubuntu-latest
    container:
      # Using freedesktop runtime for maximum compatibility
      image: bilelmoussaoui/flatpak-github-actions:freedesktop-23.08
    steps:
      - uses: actions/checkout@v3
        with:
          lfs: true
      
      # Download game artifacts from previous build jobs
      - name: Download Linux Build
        uses: actions/download-artifact@v3
        with:
          name: linux-build
          
      # Update version in metainfo
      - name: Update version info
        run: |
          VERSION=${GITHUB_REF#refs/tags/v}
          DATE=$(date +%Y-%m-%d)
          sed -i "s/<release version=\"[0-9]*\.[0-9]*\.[0-9]*\" date=\"[0-9-]*\"\/>/<release version=\"$VERSION\" date=\"$DATE\"\/>/" io.github.yourusername.yourgame.metainfo.xml
      
      # Build the flatpak
      - name: Build Flatpak
        uses: bilelmoussaoui/flatpak-github-actions/flatpak-builder@v6
        with:
          bundle: your-game.flatpak
          manifest-path: io.github.yourusername.yourgame.yml
          cache-key: flatpak-builder-${{ github.sha }}
      
      # Upload the flatpak
      - name: Upload Flatpak
        uses: actions/upload-artifact@v3
        with:
          name: flatpak
          path: your-game.flatpak
```

## Distributing on Flathub

### What is Flathub?

Flathub is the central repository for Flatpak applications:

- It's like the "App Store" for Linux
- Integrated into software centers
- Trusted by users
- Curated with quality standards

### First-Time Flathub Submission

1. **Prepare Your Application**:
    - Complete manifest file
    - AppStream metadata with screenshots
    - Desktop file
    - Icon
    - Working flatpak package
      
2. **Create a GitHub Repository**:
    - Create a new repository with your manifest
    - Name format: `flathub/io.github.yourusername.yourgame`
    - Add the manifest and metadata files
      
3. **Submit to Flathub**:
    - Fork https://github.com/flathub/flathub
    - Create an issue requesting a new application
    - Link to your manifest repository
    - Provide testing instructions
      
4. **Review Process**:
    - Maintainers will review your submission
    - They'll check packaging, metadata, screenshots, etc.
    - Fix issues as they come up
    - Once approved, they'll create your official Flathub repo
      
5. **Get Your App Published**:
    - After approval, your game appears on Flathub!
    - It's now available in Linux app stores

### Getting Your FOSS Game Approved

Tips for smooth approval:

- Ensure your game has a clear open source license
- Include quality screenshots
- Make sure it actually works (test your flatpak!)
- Follow the [Flathub guidelines](https://github.com/flathub/flathub/wiki/App-Requirements)
- Be responsive to reviewer feedback

## Updating Your Game on Flathub

Once your game is accepted on Flathub, you'll need to know how to update it when you release new versions. Here's how to keep your Flatpak fresh!

### Manual Updates

After your game is on Flathub, updating it is straightforward:

1. **Clone your Flathub repo**:
    
    ``` bash
    git clone https://github.com/flathub/io.github.yourusername.yourgame.git
    cd io.github.yourusername.yourgame
    ```
    
2. **Update the source in your manifest**:
    
    ``` yaml
    sources:
      - type: git
        url: https://github.com/yourusername/yourgame
        tag: v1.1.0  # Update this to your new tag
    ```
    
3. **Update the release info in metainfo.xml**:
    
    ``` xml
    <releases>
      <release version="1.1.0" date="2025-03-15"/>
      <release version="1.0.0" date="2025-02-28"/>
    </releases>
    ```
    
4. **Create a PR to your Flathub repo**:
    
    ``` bash
    git checkout -b update-1.1.0
    git add .
    git commit -m "Update to version 1.1.0"
    git push origin update-1.1.0
    ```
    
5. **Create PR on GitHub** to your Flathub repo
    

### Automating Flathub Updates

Why do things manually when you can automate them? Create `.github/workflows/update-flathub.yml`:

``` yaml
name: Update Flathub

on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  update-flathub:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Flathub repo
        uses: actions/checkout@v3
        with:
          repository: flathub/io.github.yourusername.yourgame
          token: ${{ secrets.FLATHUB_TOKEN }}  # Personal access token with repo scope
          
      - name: Update manifest version
        run: |
          VERSION=${GITHUB_REF#refs/tags/v}
          DATE=$(date +%Y-%m-%d)
          # Update source tag in manifest
          sed -i "s/tag: v[0-9]*\.[0-9]*\.[0-9]*/tag: v$VERSION/g" io.github.yourusername.yourgame.yml
          # Update release info in metainfo
          sed -i "0,/<release version=\"[0-9]*\.[0-9]*\.[0-9]*\" date=\"[0-9-]*\"\/>/s//<release version=\"$VERSION\" date=\"$DATE\"\/>\n    &/" io.github.yourusername.yourgame.metainfo.xml
          
      - name: Create PR
        uses: peter-evans/create-pull-request@v5
        with:
          commit-message: "Update to version $VERSION"
          title: "Update to version $VERSION"
          body: |
            Automated update to version $VERSION
            
            Changes in this version:
            - [Add your release notes here]
          branch: "update-v$VERSION"
          base: master
```

### GitHub Personal Access Token for Flathub

To automate Flathub updates:

1. Go to GitHub → Settings → Developer settings → Personal access tokens
2. Generate a new token with `repo` scope
3. Add it as a secret in your GitHub repo: `FLATHUB_TOKEN`

### Testing Before Submit

Always test your updated Flatpak before submitting the PR:

Clone your update branch:

``` bash
git clone -b update-v1.1.0 https://github.com/yourusername/io.github.yourusername.yourgame.git
cd io.github.yourusername.yourgame
```

Build and test:

``` bash
flatpak-builder --user --install --force-clean build-dir io.github.yourusername.yourgame.yml
flatpak run io.github.yourusername.yourgame
```

### The Complete Workflow

Once your automation is set up, your workflow becomes beautifully simple:

1. Develop amazing features in your game
2. Tag a new version: `git tag -a v1.1.0 -m "Version 1.1.0"`
3. Push the tag: `git push origin v1.1.0`
4. GitHub Actions creates a PR to update your Flathub repo
5. You review and merge the PR (or Flathub maintainers do)
6. Users get the update automatically in their app stores!
