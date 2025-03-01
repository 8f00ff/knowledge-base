_For when your brain works in mysterious ways but your repo needs to look professional AF_

## Branch Strategy & Workflow Rules

### The Sacred Rules

- `main` branch is HOLY GROUND - no direct commits, only PRs
- Create feature branches for ALL work: `git checkout -b feature/thing-im-working-on`
- Tag releases instead of using release branches: `git tag -a v1.0.0 -m "Initial release"`
- Small, focused PRs > massive change dumps

### Brain-Friendly Workflow

1. **Work how your brain wants to work**
2. **Clean up before sharing**
3. **Let GitHub Actions do the cross-platform heavy lifting**

## Branching & Release Workflow Summary

1. **Daily Work**:
    - Work on feature branches
    - Commit often, clean up before PR
    - Use stash & worktree when multi-tasking
      
2. **Pull Request Flow**:
    - Clean up commits before PR
    - PR to main when feature is complete
    - Let CI build & test it
      
3. **Release Process**:
    - Tag main when ready: `git tag -a v1.0.0 -m "Release notes"`
    - Push tag: `git push origin v1.0.0`
    - Let release workflow handle builds & distribution

Remember: Your workflow should fit YOUR brain, not the other way around. Git is a tool to help you, not a straightjacket!
