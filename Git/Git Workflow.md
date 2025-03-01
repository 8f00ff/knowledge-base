## Version Control Philosophy

This workflow follows a **stable main branch** pattern where:

- `main` branch always contains the latest stable release
- Development happens in version-specific branches
- Bug fixes may be backported to previous version branches
- Documentation and infrastructure changes can go directly to main

This approach is ideal for:

- Projects requiring long-term version support
- Software with significant testing requirements
- Applications where stability is critical
- Projects with multiple concurrent development streams

## Branch Structure

```
main          A---B---C---F---G---L---M   (stable releases only)
               \         /     /
v1.1.1         D---E---/      /           (bugfix branch)
               \             /
v1.2.0          H---I---J---K             (feature branch)
                    \
feature-X           N---O                 (feature branches)
```

### Branch Types

| Branch Type | Naming         | Purpose                             | Branch From              | Merge To                              |
| ----------- | -------------- | ----------------------------------- | ------------------------ | ------------------------------------- |
| Main        | `main`         | Latest stable release               | N/A                      | N/A                                   |
| Version     | `vX.Y.Z`       | Development of a specific version   | `main` at latest release | `main` when complete                  |
| Feature     | `feature-name` | Individual feature development      | Version branch           | Version branch                        |
| Bugfix      | `bugfix-name`  | Fix for specific issue              | Version branch           | Version branch                        |
| Hotfix      | `hotfix-name`  | Critical fixes for released version | `main`                   | `main` AND current version branch(es) |
| Upkeep      | `upkeep-name`  | Changes unrelated to the codebase   | `main`                   | `main` AND current version branch(es) |

## Version Numbering

See [Versioning](../Decumenting/Versioning).

## Handling Non-Code Changes

Changes that don't affect the actual codebase can follow a simplified workflow:

### Appropriate for Direct Main Commits

- README updates unrelated to features
- Contributor/supporter list updates
- Donation link changes
- License clarifications
- Typo fixes in documentation
- CI/CD tweaks that don't affect builds

```bash
# For small documentation/meta changes
git checkout main
# Make changes
git commit -am "docs: update supporter list"
git push origin main
```

### Better as Branches Then Merge

- Major documentation restructuring
- Significant CI/CD pipeline changes
- Website or community resource updates

```bash
# For larger documentation/infrastructure work
git checkout main
git checkout -b upkeep-docs-overhaul
# Make changes
git commit -am "docs: reorganize tutorial section"
# Create PR to merge back to main
```

These changes don't need version branches and don't need to wait for a release cycle since they don't impact the actual software.

## Workflow Examples

### Starting a New Version

```bash
# After v1.1.0 is released and tagged on main
git checkout main
git checkout -b v1.2.0
# Update version info to v1.2.0-dev
git commit -am "Begin v1.2.0 development"
git tag v1.2.0-dev
git push origin v1.2.0 --tags
```

### Working on Features

```bash
# Create feature branch from version branch
git checkout v1.2.0
git checkout -b feature-awesome-thing
# Work on feature...
git commit -am "Implement awesome thing"
# When ready, create PR to merge into version branch
```

### Releasing a Version

```bash
# When version branch is ready for release
git checkout v1.2.0
# Final testing, then update changelog and version
git commit -am "Prepare v1.2.0 release"
# Merge to main via PR
git checkout main
git merge v1.2.0
git tag v1.2.0
git push origin main --tags
# Create GitHub release from tag
```

### Handling Bug Fixes

```bash
# Critical bug found in v1.1.0
git checkout main  # which is at v1.1.0
git checkout -b v1.1.1
# Fix bug
git commit -am "Fix critical bug"
# Update version and changelog
git commit -am "Prepare v1.1.1 release"
# Merge to main
git checkout main
git merge v1.1.1
git tag v1.1.1
git push origin main --tags
# Also merge or cherry-pick the bug fix to v1.2.0
git checkout v1.2.0
git cherry-pick <bug-fix-commit-hash>
git push origin v1.2.0
```

## Commit Message Guidelines

Follow [Conventional Commits](https://www.conventionalcommits.org/) format:

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

Types:

- **feat**: New feature
- **fix**: Bug fix
- **docs**: Documentation changes
- **style**: Formatting changes
- **refactor**: Code restructuring
- **perf**: Performance improvements
- **test**: Adding/fixing tests
- **chore**: Build/tool changes

Example:

```
feat(warpcore): implement dilithium crystal optimization

Added support for automated crystal matrix realignment with quantum resonance stabilization for 27% increased warp field efficiency.

Closes #42
```

## Changelog Updates

Following [Keep a Changelog](https://keepachangelog.com/) format:

- Add entries to `CHANGELOG.md` as you work
- Keep changes under the `[Unreleased]` section
- Move to proper version section when releasing
- Group changes by type (Added, Changed, Fixed, etc.)
- Focus on user-facing changes, not implementation details

For more information, see [Changelogs](../Documenting/Changelogs).

## Pull Request Process

1. Create PR from feature branch to version branch (for code changes) or to main (for other changes)
2. Ensure all tests pass
3. Update documentation as needed
4. Require at least one approval
5. Merge using squash or rebase to keep history clean

## Release Checklist

- [ ] All tests passing
- [ ] Changelog updated
- [ ] Version number updated in all locations
- [ ] Documentation updated
- [ ] PR reviewed and approved
- [ ] Merged to main
- [ ] Tagged with version
- [ ] GitHub release created with notes

## Additional Considerations

### For Open Source Projects

- Consider using issue templates
- Document this workflow in `CONTRIBUTING.md`
- Set up automation for changelog generation
- Configure branch protection rules

### For Team Projects

- Consider using linear history (rebase instead of merge)
- Create team agreements on PR review requirements
- Establish CI/CD pipelines that respect this branching model
- Determine decision process for version numbering

### For Solo Projects

- While this may seem like overkill for solo work, it builds good habits
- Consider simplifying for very small projects
- The workflow scales nicely as projects grow
- Reduces mental overhead when switching between projects

## Useful Resources

- [GitHub Flow](https://guides.github.com/introduction/flow/) - Simpler alternative for teams that deploy frequently
- [Git Branching Model](https://nvie.com/posts/a-successful-git-branching-model/) - The original "GitFlow" that inspired many workflows
- [Git Cherry Pick](https://git-scm.com/docs/git-cherry-pick) - Essential for backporting fixes
- [Learn Git Branching](https://learngitbranching.js.org/) - Interactive way to learn Git concepts
- [Semantic Versioning](https://semver.org/) - The standard for version numbering
- [Release Drafter](https://github.com/release-drafter/release-drafter) - GitHub Action to automate release notes
- [GitHub Release How-to](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository) - Guide for GitHub releases
- [Conventional Commits](https://www.conventionalcommits.org/) - Standard for commit messages
- [Commitizen](https://github.com/commitizen/cz-cli) - Tool to create standardized commit messages
