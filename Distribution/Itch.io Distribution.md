## Creating Your Itch.io Project

### Setting Up Your Game Page

1. **Create account** at https://itch.io if you don't have one
    
2. Click **Upload new project** in dashboard
    
3. Fill out the basics:
    - **Title**: Your game's name
    - **Project URL**: Creates yourusername.itch.io/your-project-url
    - **Classification**: Probably "Game"
    - **Kind of project**: Select appropriate category
    - **Pricing**: "No payments" or "Suggested payments" for FOSS games
    
4. Click "Save & view page" then "Edit theme" to customize

### Essential Page Elements

For a professional-looking page:

- **Cover image**: 630×500 pixels
- **Game screenshots**: At least 3-4 showing different aspects
- **Clear description**: What's the game about?
- **System requirements**: What platforms? Min specs?
- **Dev log**: Regular updates about development

## Uploading Game Builds

### Manual Upload Process

1. Go to your game page → **Edit game**
2. Scroll to **Uploads** section
3. Click **Upload files**
4. Select your build files
5. Configure each file:
    - Set platform (Windows, macOS, Linux)
    - Set correct file format
    - Add version number
    - Mark as "This file will be played in the browser" for web builds

### Managing Builds

Configure how players download your game:

- **Embed options**: For web games
- **Platforms**: Set which OS each file is for
- **Demo files**: Mark builds as demo if applicable
- **App metadata**: Add version info, tags

## Using Butler for Uploads

### Setting Up Butler on Arch

Install butler from AUR:

``` bash
yay -S butler
```

Or download manually:

``` bash
curl -L -o butler.zip https://broth.itch.ovh/butler/linux-amd64/LATEST/archive/default
unzip butler.zip
chmod +x butler
```

Login to your itch.io account:

``` bash
butler login
```

### Pushing Builds with Butler

Basic upload (auto-creates the channel if it doesn't exist):

``` bash
butler push your-game-linux.tar.gz username/game-name:linux
```

Add version number:

``` bash
butler push your-game-windows.zip username/game-name:windows --userversion 1.0.0
```

Push with notes (shows in version history):

``` bash
butler push your-game-mac.zip username/game-name:mac --userversion 1.0.0 --user-version-file CHANGELOG.md
```

The format is always: `butler push [file] [username]/[game-name]:[channel]`

### Understanding Channels

Channels organize your builds by platform or version:

- Standard channels: `windows`, `mac`, `linux`, `web`
- Custom channels: `windows-demo`, `beta`, `experimental`

Players automatically see the right download for their platform!

## Automating Itch.io Deployment

### Getting Your API Key

Butler already saved your API key when you logged in:

``` bash
cat ~/.config/itch/butler_creds
```

Copy this key for use in GitHub Actions.

### GitHub Action for Itch.io Deployment

Create `.github/workflows/deploy-itch.yml`:

``` yaml
name: Deploy to Itch.io

on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  deploy:
    needs: [build-linux, build-windows, build-macos]  # Your build jobs
    runs-on: ubuntu-latest
    steps:
      - name: Download Linux Build
        uses: actions/download-artifact@v3
        with:
          name: linux-build
      
      - name: Download Windows Build
        uses: actions/download-artifact@v3
        with:
          name: windows-build
          
      - name: Download macOS Build
        uses: actions/download-artifact@v3
        with:
          name: mac-build
      
      - name: Deploy to itch.io
        env:
          BUTLER_API_KEY: ${{ secrets.BUTLER_API_KEY }}
          VERSION: ${{ github.ref_name }}
        run: |
          # Download and setup butler
          curl -L -o butler.zip https://broth.itch.ovh/butler/linux-amd64/LATEST/archive/default
          unzip butler.zip
          chmod +x butler
          
          # Push builds to itch.io
          ./butler push linux-build/your-game-linux.tar.gz username/game-name:linux --userversion ${VERSION:1}
          ./butler push windows-build/your-game-windows.zip username/game-name:windows --userversion ${VERSION:1}
          ./butler push mac-build/your-game-mac.zip username/game-name:mac --userversion ${VERSION:1}
```

### Setting Up Secrets in GitHub

1. Go to your GitHub repo → Settings → Secrets → Actions
2. Create a secret named `BUTLER_API_KEY`
3. Paste your API key from `~/.config/itch/butler_creds`

## Managing Itch.io Game Updates

### Version Numbering

Use semantic versioning for clarity:

- **1.0.0**: Initial release
- **1.1.0**: New features
- **1.1.1**: Bug fixes
- **2.0.0**: Major changes

For more information, see:

- [semver](https://semver.org/) - The official semver specification.
- [PEP 440](https://peps.python.org/pep-0440/) - Python's version identification and dependency specification.
- [Cargo's SemVer Guide](https://doc.rust-lang.org/cargo/reference/semver.html) - Rust's Cargo semver compatibility.
- [npm semver calculator](https://semver.npmjs.com/) - npm semver calculator.

### Communicating Changes

Always keep players informed:

1. Update the game description with latest features
2. Post devlogs for significant updates
3. Include a `CHANGELOG.md` in your repo
4. Use "Edit Embed Information" to update web embed details

### Handling Beta/Experimental Channels

Push to a beta channel:
```
butler push your-game-beta.zip username/game-name:beta --userversion 1.2.0-beta
```

Configure access on itch.io:

- Go to Edit Game → Access & Visibility
- Set "Locked" (requires keys/passwords)
- Or "Also available on the game page" (opt-in)

## Itch.io Community & Analytics

### Engaging with Players

- **Enable comments** in game settings
- **Respond** to feedback and questions
- **Set up a community** section for deeper discussions

### Using Itch.io Analytics

Access game analytics from your dashboard:

- **Views**: Page traffic
- **Downloads**: Build popularity
- **Purchase stats**: If using payments
- **Referrers**: Where players come from

## Complete Itch.io Workflow

1. **Create** your game page early (even in development)
2. **Post devlogs** to build interest
3. **Add builds** when ready (manual or butler)
4. **Tag releases** in git -> GitHub Actions -> Butler -> itch.io
5. **Engage** with your community
