## Usage

Create a full backup tar archive of your Git repository:

```bash
git-backup [output-dir]
```

By default, outputs to the parent directory.

Extract a backup archive:

```bash
git-unbackup <input-tgz> [output-dir]
```

By default, directory name is generated from the archive name.

## What These Scripts Handle

- Full git history (using git bundle)
- LFS files with proper links
- Uncommitted changes
- Ignored files that aren't in git

## How They Work

- `git-backup` automatically generates a filename with format: `project-name.backup-YYYYMMDDTHHMMSS.tgz`
- `git-unbackup` extracts everything with LFS files properly handled

## Installation

Install the scripts directly from GitHub:

```bash
mkdir -p ~/.local/bin
curl -o ~/.local/bin/git-backup https://gist.githubusercontent.com/8f00ff/d69e92115f32868103007b2dcb5f3ed4/raw/8c55a680e5a195fc5f4e3301e2a8e51a29e405d1/git-backup.sh
curl -o ~/.local/bin/git-unbackup https://gist.githubusercontent.com/8f00ff/d69e92115f32868103007b2dcb5f3ed4/raw/8c55a680e5a195fc5f4e3301e2a8e51a29e405d1/git-unbackup.sh
chmod +x ~/.local/bin/git-backup ~/.local/bin/git-unbackup
```

Ensure `~/.local/bin` is in your PATH by adding this to your `~/.bashrc`:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Set up Git aliases to use them:

```bash
git config --global alias.backup '!git-backup'
git config --global alias.unbackup '!git-unbackup'
```

For more details, see the [full gist](https://gist.github.com/8f00ff/d69e92115f32868103007b2dcb5f3ed4).

## Example Usage

Create a backup in the parent directory (default):

```bash
git backup
```

Create a backup in a specific directory:

```bash
git backup ~/backups
```

Restore a backup (directory name generated from archive):

```bash
git unbackup ~/backups/my-game.backup-20250228T123045.tgz
```

Restore to a specific directory:

```bash
git unbackup ~/backups/my-game.backup-20250228T123045.tgz ~/restored-project
```
