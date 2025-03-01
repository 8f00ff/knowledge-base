## Local Development Setup (Arch Linux)

### Installing Rust

See: https://wiki.archlinux.org/title/Rust

### Setting up Bevy on Arch

Install dependencies:

``` bash
pacman -S clang lld pkgconf alsa-lib libxkbcommon-x11
```

For Wayland support:

``` bash
pacman -S wayland wayland-protocols
```

For Vulkan support:

``` bash
pacman -S vulkan-headers vulkan-icd-loader shaderc
```

### Optimizing Bevy Builds

Add this to your `~/.cargo/config.toml`:

``` toml
[target.x86_64-unknown-linux-gnu]
linker = "clang"
rustflags = ["-Clink-arg=-fuse-ld=lld"]

# Enable a small amount of optimization in debug mode
[profile.dev]
opt-level = 1

# Enable high optimizations for dependencies, but not for our code
[profile.dev.package."*"]
opt-level = 3
```

## Building for Multiple Platforms

### Linux Build

Debug build:

``` bash
cargo build
```

Release build:

``` bash
cargo build --release
```

### Windows Build (from Linux)

You will need `rustup` and rust installed with `rustup` for this.

Install target:

``` bash
rustup target add x86_64-pc-windows-gnu
```

Install MinGW:

``` bash
pacman -S mingw-w64-gcc
```

Build the game:

``` bash
cargo build --release --target x86_64-pc-windows-gnu
```

### macOS Build

_macOS builds from Linux are complex due to Apple's toolchain. It's better to use GitHub Actions with macOS runners for this._

### Web (WASM) Build

Install target:

``` bash
rustup target add wasm32-unknown-unknown
```

Install `wasm-bindgen`:

``` bash
cargo install wasm-bindgen-cli
```

Build:

``` bash
cargo build --release --target wasm32-unknown-unknown
```

Generate bindings:

``` bash
wasm-bindgen --out-dir ./web/ --target web \
  target/wasm32-unknown-unknown/release/your_game.wasm
```

## Creating Game Packages

### Linux Package

Create game package directory:

``` bash
mkdir -p dist/linux/your-game
```

Copy binary and assets:

``` bash
cp target/release/your-game dist/linux/your-game/
cp -r assets dist/linux/your-game/
```

Create launcher script:

``` bash
#!/bin/bash
cd "$(dirname "$0")"
./your-game
```

``` bash
chmod +x dist/linux/your-game/your-game.sh
```

Package it:

``` bash
tar -czvf dist/linux/your-game-linux.tgz -C dist/linux your-game
```

### Windows Package

Create game package directory:

``` bash
mkdir -p dist/windows/your-game
```

Copy binary and assets:

``` bash
cp target/x86_64-pc-windows-gnu/release/your-game.exe dist/windows/your-game/
cp -r assets dist/windows/your-game/
```

Package it:

``` bash
cd dist/windows
zip -r your-game-windows.zip your-game
```

### macOS Package

For macOS, it's best to bundle on a macOS machine or GitHub runner:

Create `.app` structure:

``` bash
mkdir -p YourGame.app/Contents/{MacOS,Resources}
```

Copy binary and Info.plist:

``` bash
$ cp target/release/your-game YourGame.app/Contents/MacOS/
$ cp Info.plist YourGame.app/Contents/
```

Copy assets:

``` bash
cp -r assets/ YourGame.app/Contents/Resources/
```

Create DMG if needed:

``` bash
hdiutil create -volname "Your Game" -srcfolder YourGame.app -ov -format UDZO your-game-mac.dmg
```

## Cross-Platform GitHub Actions Pipeline

Create `.github/workflows/build.yml`:

``` yaml
name: Build Game

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build-linux:
    runs-on: ubuntu-latest
    container:
      image: rust:alpine
    steps:
      - uses: actions/checkout@v3
        with:
          lfs: true
      - name: Install dependencies
        run: |
          apk add --no-cache build-base pkgconf alsa-dev libxkbcommon-dev
      - name: Build for Linux
        run: cargo build --release
      - name: Create package
        run: |
          mkdir -p dist/your-game
          cp target/release/your-game dist/your-game/
          cp -r assets dist/your-game/
          tar -czvf your-game-linux.tar.gz -C dist your-game
      - name: Upload Linux build
        uses: actions/upload-artifact@v3
        with:
          name: linux-build
          path: your-game-linux.tar.gz

  build-windows:
    runs-on: windows-latest
    steps:
      - uses: actions/checkout@v3
        with:
          lfs: true
      - name: Install Rust
        uses: actions-rs/toolchain@v1
        with:
          toolchain: stable
          override: true
      - name: Build for Windows
        run: cargo build --release
      - name: Create package
        run: |
          mkdir -p dist/your-game
          cp target/release/your-game.exe dist/your-game/
          cp -r assets dist/your-game/
          Compress-Archive -Path dist/your-game -DestinationPath your-game-windows.zip
      - name: Upload Windows build
        uses: actions/upload-artifact@v3
        with:
          name: windows-build
          path: your-game-windows.zip

  build-macos:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v3
        with:
          lfs: true
      - name: Install Rust
        uses: actions-rs/toolchain@v1
        with:
          toolchain: stable
          override: true
      - name: Build for macOS
        run: cargo build --release
      - name: Create package
        run: |
          mkdir -p YourGame.app/Contents/{MacOS,Resources}
          cp target/release/your-game YourGame.app/Contents/MacOS/
          cp -r assets/ YourGame.app/Contents/Resources/
          zip -r your-game-mac.zip YourGame.app
      - name: Upload macOS build
        uses: actions/upload-artifact@v3
        with:
          name: mac-build
          path: your-game-mac.zip

  build-web:
    runs-on: ubuntu-latest
    container:
      image: rust:slim
    steps:
      - uses: actions/checkout@v3
        with:
          lfs: true
      - name: Install dependencies
        run: |
          apt-get update && apt-get install -y pkg-config libssl-dev
      - name: Install wasm target
        run: |
          rustup target add wasm32-unknown-unknown
          cargo install wasm-bindgen-cli
      - name: Build for Web
        run: |
          cargo build --release --target wasm32-unknown-unknown
          wasm-bindgen --out-dir ./web/ --target web target/wasm32-unknown-unknown/release/your-game.wasm
      - name: Create package
        run: |
          mkdir -p dist/web
          cp web/* dist/web/
          cp -r assets dist/web/
          # Create index.html if needed
          tar -czvf your-game-web.tar.gz -C dist web
      - name: Upload Web build
        uses: actions/upload-artifact@v3
        with:
          name: web-build
          path: your-game-web.tar.gz
```

## Release Pipeline for Bevy Games

Create `.github/workflows/release.yml`:

``` yaml
name: Release Game

on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  create-release:
    needs: [build-linux, build-windows, build-macos, build-web]
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Download all artifacts
        uses: actions/download-artifact@v3
      
      - name: Get version from tag
        id: get_version
        run: echo "VERSION=${GITHUB_REF#refs/tags/v}" >> $GITHUB_OUTPUT
      
      - name: Create Release
        uses: softprops/action-gh-release@v1
        with:
          name: Release ${{ steps.get_version.outputs.VERSION }}
          draft: false
          prerelease: false
          files: |
            linux-build/your-game-linux.tar.gz
            windows-build/your-game-windows.zip
            mac-build/your-game-mac.zip
            web-build/your-game-web.tar.gz
```

## Optimizing Your Bevy Game

### Build Size Optimization

Add to your `Cargo.toml`:

``` toml
[profile.release]
opt-level = 'z'   # Optimize for size
lto = true        # Enable link-time optimization
codegen-units = 1 # Maximize size reduction optimizations
panic = 'abort'   # Remove panic unwinding code
strip = true      # Strip symbols
```

### Load Time Optimization

These features help with load times:

``` toml
[dependencies]
bevy = { version = "0.13", features = ["dynamic_linking"] }  # For dev builds
```

And switch to standard features for release:

``` yaml
# In GitHub Actions
- name: Build for Release
  run: |
    cargo build --release --no-default-features --features bevy/default_features
```

## Bevy-specific GitHub Actions Tips

- Use caching for massive build speedups
- CI can take 10+ minutes for clean Bevy builds without caching
- Consider a dedicated self-hosted runner for faster builds
- Add automated tests
- Check for leaking GPU resources
