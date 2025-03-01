Get set up with a solid Git configuration in seconds!

## Quick Installation

```bash
curl -o ~/.gitconfig https://gist.githubusercontent.com/8f00ff/22ef146a137c27a85480e00a09796651/raw/7a43eb2264f5690e54e220ba7e8707bf526fded3/.gitconfig
```

Then add your personal info:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

If you use Git LFS:

```bash
git lfs install
```

## What's Included

For more details, see the [full gist](https://gist.githubusercontent.com/8f00ff/22ef146a137c27a85480e00a09796651).

### Core Settings

|Setting|Value|Purpose|
|---|---|---|
|`color.ui`|`auto`|Pretty colorized output|
|`core.editor`|`vim`|Default text editor (change to your preference)|
|`credential.helper`|`cache --timeout=900`|Cache credentials for 15 minutes|
|`init.defaultBranch`|`main`|Modern default branch name|
|`push.autoSetupRemote`|`true`|Auto-track branches on push|

### Included Aliases

- `st` - Compact status with branch info
- `lg` - Beautiful, readable commit history
- `who` - See who contributed and how much
- `undo` - Undo last commit but keep changes
- `amend` - Update last commit without editing message
- `branches` - Show all branches including remote
- `nuke` - Discard ALL changes (use with caution!)
- `wip` - Quick save of all work in progress
- `staged` - See what's staged for commit
- `pr <num>` - Checkout GitHub PR #num locally

## Customize It

Want to add your own aliases? Just edit `~/.gitconfig` or run:

```bash
git config --global alias.YOUR_ALIAS "YOUR COMMAND"
```
