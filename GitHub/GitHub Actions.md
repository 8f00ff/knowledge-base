## GitHub Actions Basics

GitHub Actions is a CI/CD platform built right into GitHub that lets you automate your build, test, and deployment workflows.

### Key Concepts

- **Workflow**: A configurable automated process made up of one or more jobs
- **Job**: A set of steps executed on the same runner
- **Step**: A task that can run commands or actions
- **Action**: A reusable unit of code
- **Runner**: A server that runs your workflows (GitHub-hosted or self-hosted)

### Basic Workflow Structure

``` yaml
name: My Workflow

on:  # When this workflow runs
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  my-job:  # Job ID
    runs-on: ubuntu-latest  # The runner type
    
    steps:  # Steps to execute
      - uses: actions/checkout@v3  # Use a pre-built action
      
      - name: Run a script  # Custom step
        run: echo "Hello World!"
```

## GitHub Runner Types

GitHub provides several types of runners:

``` yaml
# Linux
runs-on: ubuntu-latest  # Use when you need a Linux environment

# Windows
runs-on: windows-latest  # Use for Windows-specific builds

# macOS
runs-on: macos-latest  # Use for macOS specific builds
```

## Using Containers in GitHub Actions

For more controlled environments, use containers:

``` yaml
jobs:
  container-job:
    runs-on: ubuntu-latest
    container:
      image: rust:alpine  # Use Alpine for lean containers!
      
    steps:
      - uses: actions/checkout@v3
      - name: Build in container
        run: cargo build
```

## Triggering Workflows

### On Push or PR

``` yaml
on:
  push:
    branches: [ main, dev ]
    paths:
      - 'src/**'  # Only run when code changes
      - 'assets/**'
  pull_request:
    branches: [ main ]
```

### On Tags (for Releases)

``` yaml
on:
  push:
    tags:
      - 'v*.*.*'  # Run on version tags
```

### Manual Triggers

``` yaml
on:
  workflow_dispatch:  # Manual trigger
    inputs:
      platform:
        description: 'Build platform'
        required: true
        default: 'all'
        type: choice
        options:
        - all
        - linux
        - windows
        - mac
```

## GitHub Actions Optimization

### Caching Dependencies

``` yaml
steps:
  - uses: actions/checkout@v3
  
  - name: Cache cargo registry
    uses: actions/cache@v3
    with:
      path: |
        ~/.cargo/registry
        ~/.cargo/git
        target
      key: ${{ runner.os }}-cargo-${{ hashFiles('**/Cargo.lock') }}
```

### Artifacts Management

``` yaml
- name: Upload artifact
  uses: actions/upload-artifact@v3
  with:
    name: my-artifact
    path: path/to/file
    retention-days: 5  # Keep for 5 days

- name: Download artifact
  uses: actions/download-artifact@v3
  with:
    name: my-artifact
    path: path/to/download
```

## Secrets Management

Use secrets for sensitive information:

``` yaml
steps:
  - name: Deploy
    env:
      API_KEY: ${{ secrets.MY_API_KEY }}
    run: ./deploy-script.sh
```

To add secrets:

1. Go to your repository -> Settings → Secrets -> Actions
2. Click "New repository secret"
3. Name it and paste the value

## Debugging GitHub Actions

### Enable Debug Logging

Set secret `ACTIONS_RUNNER_DEBUG` to `true` in repository settings.

### Add Debug Steps

``` yaml
- name: Debug info
  run: |
    pwd
    ls -la
    env | sort
```

### Local Testing with Act

Test workflows locally on Arch:

Install act from AUR:

``` bash
yay -S act
```

Run a specific job:

```
act -j build-linux
```

## Matrix Builds

Run jobs with different configurations:

``` yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        rust: [stable, nightly]
    
    steps:
      - uses: actions/checkout@v3
      - name: Setup Rust
        run: rustup default ${{ matrix.rust }}
      # ...more steps
```

## GitHub Action Events

Some useful event triggers:

- `schedule`: Run on a schedule using cron syntax
- `release: {types: [published]}`: Run when a release is published
- `issues: {types: [opened, labeled]}`: Run when issues are created/labeled
- `workflow_run: {workflows: ["Build"], types: [completed]}`: Run after another workflow

## Common GitHub Actions Issues & Solutions

- **Build times too long**: Use caching and matrix builds
- **Runner timeouts**: Split into multiple jobs
- **Environment issues**: Use containers for consistent builds
- **Secrets exposure**: Never print secrets in logs
- **Cache invalidation**: Use proper cache keys
