## Starting Fresh

Cloning a repo:

``` bash
git clone https://github.com/username/game-repo.git
```

Initializing a new one:

``` bash
git init
git remote add origin https://github.com/username/game-repo.git
```

## Day-to-Day Work

Create a new branch: (**ALWAYS!**)

``` bash
git checkout -b feature/dialogue-system
```

Commit early & often: (Clean up later!)

``` bash
git add <files>
git commit -m "Add whatever, this is a WIP commit"
```

Save unfinished work when switching tasks:

``` bash
git stash save "halfway through dialogue system"
git checkout -b feature/combat-system
# work on new thing
```

Resume previous work:

``` bash
git checkout feature/dialogue-system
git stash pop
```

Reset all changes: (start fresh)

``` bash
git reset --hard HEAD
git clean -fd # remove untracked files
```

## Working on Multiple Features Simultaneously

Creating separate working directories:

```bash
git worktree add ../game-ui-work feature/ui-improvements
git worktree add ../game-combat-work feature/combat-system
```

Now you have THREE working directories:

- **Original repo**: ~/projects/my-game
- **UI work**: ~/projects/game-ui-work
- **Combat work**: ~/projects/game-combat-work

Remove a worktree when done.

``` bash
git worktree remove ../game-ui-work
```

## "Oh Frell I've Been Working on Main" Recovery

See what you've done with:

``` bash
git log --oneline
```

You should see a log similar to the following:

```
a1b2c3d Feature for health bar (newest)
e4f5g6h New enemy AI
i7j8k9l Base commit (this should stay on main)
```

**Option 1**: Save ALL your accidental commits.

Just create a branch right where you are:

``` bash
git checkout -b feature/stuff-i-did-on-main
git push -u origin feature/stuff-i-did-on-main
```

**Option 2**: Go back to a specific point FIRST, then branch

If you want to branch from a specific earlier commit:

``` bash
git checkout i7j8k9l  # Checkout the specific commit you want
git checkout -b feature/partial-work  # Create branch at THAT point
```

Finally, clean up main:

``` bash
git checkout main
git reset --hard origin/main  # Reset to what's on GitHub
```

Now main is clean again, and your work is safe on a feature branch.

Now create a PR from your feature branch to main.

## Cherry Picking (Move Commits Between Branches)

Find the commits you want to move:

``` bash
git log --oneline
```

The log should look something like this:

```
a1b2c3d Feature A
e4f5g6h Feature B
i7j8k9l Feature C
```

Move specific commits to a new branch:

``` bash
git checkout -b feature/just-feature-a
git cherry-pick a1b2c3d
```

Or grab a range of commits:

``` bash
git checkout -b feature/b-and-c
git cherry-pick e4f5g6h^..i7j8k9l
```

## Time Travel

Set commit time manually when committing:

```
GIT_AUTHOR_DATE="1999-12-31 23:59:59" GIT_COMMITTER_DATE="1999-12-31 23:59:59" git commit -m "Commit with custom timestamp"
```

Modify last commit's timestamp:

```
git commit --amend --date="1999-12-31 23:59:59"
```

For older commits, you can use interactive rebase:

```
git rebase -i HEAD~3
```

In the editor, change 'pick' to 'edit' for the commit you want to modify.

```
edit 45a2ab0 added github docs  
pick b848a6e added distribution docs  
pick f310d35 added bevy engine docs
```

Then run:

```
git commit --amend --date="1999-12-31 23:59:59"
git rebase --continue
```

### Preserving Timestamps

Preserves original commit timestamps when rebasing:

``` bash
git rebase -i <commit-range> --committer-date-is-author-date
```

During interactive rebase, when amending a commit:

``` bash
git commit --amend --no-edit --reset-author
```

Or for maximum timestamp protection:

``` bash
GIT_COMMITTER_DATE="$(git log -1 --format=%ci)" git commit --amend --no-edit
```

## Change Authors

Change both author name and email:

``` bash
git commit --amend --author="New Name <newemail@example.com>" --no-edit
```

### Using git-filter-repo

> **This is potentially destructive, make sure to make backups first!**

> This will remove the origin if there is one, make sure to save it before running the command.

Create a `.mailmap` file:

```
New Name <new-email> <old-email>
```

*Send it*:

``` bash
git filter-repo --force --mailmap .mailmap
```

Add the origin back and push the changes:

``` bash
git remote add origin <repo-url>
git push --force-with-lease
```

You can now remove the mailmap file.

## Commit Cleanup (Before PRs)

### Amending the Last Commit

Change the last commit message:

``` bash
git commit --amend -m "Better commit message"
```

Add forgotten files to the last commit:

``` bash
git add forgotten-file.txt
git commit --amend --no-edit
```

Force push if needed: (safety first!)

``` bash
git push --force-with-lease
```

### Squishing Last Commits

Roll back your commits to last N commit while keeping all changes staged:

``` bash
git reset --soft HEAD~3
```

Add staged changes to that commit:

``` bash
git commit --amend
```

### Interactive Rebase (God Mode)

Rewrite history for your last N commits:

```bash
git rebase -i HEAD~3
```

In the editor that opens, you'll see:

```
pick a1b2c3d First commit
pick e4f5g6h Second commit
pick i7j8k9l Third commit
```

Change to:

```
pick a1b2c3d First commit
squash e4f5g6h Second commit
reword i7j8k9l Third commit
```

Commands available:

- **pick**: keep commit as is
- **reword**: change the commit message
- **squash**: combine with previous commit (keep both messages)
- **fixup**: combine with previous commit (discard this message)
- **drop**: remove the commit entirely

## LFS & Binary Asset Management

### Setting Up LFS

Install Git LFS:

``` bash
git lfs install --local
```

Track binary file types:

``` bash
git lfs track "*.png" "*.jpg" "*.wav" "*.mp3" "*.fbx" "*.blend"
```

Make sure `.gitattributes` is committed:

``` bash
git add .gitattributes
git commit -m "Configure Git LFS tracking"
```

### LFS Troubleshooting

Force download all LFS objects:

```bash
git lfs fetch --all
```

Check LFS status:

``` bash
git lfs status
```

Fix broken LFS pointers:

``` bash
git lfs migrate import --include="*.png,*.jpg"
```

If all else fails, reclone with LFS enabled:

``` bash
rm -rf broken-repo
git clone --recurse-submodules https://github.com/username/game-repo.git
```

## Emergency Git Situations

### "I Frelled Up BIG TIME" Recovery

Check reflog to see previous states:

```bash
git reflog
```

Go back to before you messed up:

```
git reset --hard HEAD@{2}
```

Or if you have a backup bundle:

```
git clone ~/game-backups/my-game-git-2025-02-28.bundle recovered-repo
cd recovered-repo
```

Restore any missing LFS files from your full backup.

See also [Full Git Backup](obsidian://open?vault=Projects&file=Handbook%2FFull%20Git%20Backup)

### "I Force Pushed Wrong" Recovery

If you still have local repo:

```bash
git push --force-with-lease origin my-local-branch:main
```

If someone else force pushed wrong:

```
git fetch origin
git checkout -b recovery
git reset --hard origin/main@{1}  # Go to previous state
git push origin recovery
```

Create PR from recovery to main.

### Rename Branches

Rename your current branch:

```bash
git branch -m new-better-name
```

If renaming a different branch:

```
git branch -m old-name new-better-name
```

If you've already pushed the branch, update remote:

```
git push origin :old-name new-better-name
git push origin -u new-better-name
```

### Keeping vs Deleting Feature Branches

Most teams DELETE branches after merging PRs because:

1. They clutter your repo visualization
2. The code is safely in main now
3. Git history preserves the fact that it came from a branch

Delete locally:

```bash
git branch -d feature/done
```

Delete from remote:

```
git push origin --delete feature/done
```
