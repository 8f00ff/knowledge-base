## CHANGELOG.md Template

``` markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- New features that haven't been released yet

### Changed

- Changes to existing functionality

### Deprecated

- Features soon to be removed

### Removed

- Features that have been removed

### Fixed

- Bug fixes

### Security

- Security vulnerability fixes

## [1.0.0] - YYYY-MM-DD

### Added

- Initial release of AstroCore
- Feature X
- Feature Y

### Changed

- Changed something

### Fixed

- Fixed something else

## [0.1.0] - YYYY-MM-DD

### Added
- Initial project structure
- Basic functionality
```

## Key Guidelines

1. **Always Add at the Top**: Newest versions go at the top of the file.
    
2. **Unreleased Section**: Always maintain an "Unreleased" section at the top for tracking changes not yet in a release.
    
3. **Types of Changes**: Group changes into categories:
    - **Added**: New features
    - **Changed**: Changes to existing functionality
    - **Deprecated**: Soon-to-be removed features
    - **Removed**: Removed features
    - **Fixed**: Bug fixes
    - **Security**: Security vulnerability fixes
      
4. **Date Format**: Use ISO date format: `YYYY-MM-DD`
    
5. **Version Links**: Add links to compare versions (when using GitHub):
    
    ```markdown
    [Unreleased]: https://github.com/username/repo/compare/v1.0.0...HEAD
    [1.0.0]: https://github.com/username/repo/compare/v0.1.0...v1.0.0
    [0.1.0]: https://github.com/username/repo/releases/tag/v0.1.0
    ```
    
6. **User-Focused**: Write for users, not developers. Focus on user-facing changes.
    
7. **Changes, Not Commits**: Group related changes, don't just list commits.
    
8. **Skip Maintenance Changes**: Don't list minor code cleanups, dependency updates, etc. unless they affect users.

## Quick Cheat Sheet

### When To Update

- Adding new features
- Changing existing functionality
- Deprecating features
- Removing features
- Fixing bugs
- Security fixes

### When NOT To Update

- Code refactoring that doesn't change behavior
- Dependency updates that don't affect users
- Documentation changes
- Build system changes

## Automation

For larger projects, consider these tools:

- [git-cliff](https://github.com/orhun/git-cliff): Changelog generator tool written in Rust
- [auto-changelog](https://github.com/CookPete/auto-changelog): NodeJS-based changelog generator
- [GitHub Releases](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases): GitHub's built-in release notes system

## Example Workflow

1. Make code changes
2. Add entry to the "Unreleased" section
3. When ready for release:
    - Move "Unreleased" entries to a new version section
    - Add the release date
    - Create a git tag for the version
    - Update version links

## Versioning

See, [Versioning](Versioning).

## Useful Resources

- [Keep a Changelog](https://keepachangelog.com/): The definitive guide to changelog best practices
- [Semantic Versioning](https://semver.org/): Versioning specification
- [Conventional Commits](https://www.conventionalcommits.org/): Structured commit messages that can automate changelog generation
- [GitHub Flow](https://guides.github.com/introduction/flow/): Workflow that integrates well with changelog maintenance
- [Release Drafter](https://github.com/release-drafter/release-drafter): GitHub Action that can draft release notes
- [Changelog Tools in Rust](https://lib.rs/keywords/changelog): Collection of Rust tools for changelog management

---

**Remember**: A good changelog makes your project more approachable, shows active maintenance, and helps users understand what has changed. It's worth the effort!