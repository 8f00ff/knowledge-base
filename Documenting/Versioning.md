## Semantic Versioning

- **Major** version (**X**.y.z): Incompatible API changes
- **Minor** version (x.**Y**.z): Backward-compatible new functionality
- **Patch** version (x.y.**Z**): Backward-compatible bug fixes
- **Pre-release** identifier: Unstable version identifiers
- **Pre-release** version: Incremental version of pre-releases

For example:

- **1.0.0**: Initial release
- **1.1.0**: New features
- **1.1.1**: Bug fixes
- **2.0.0-alpha.1**: Unstable preview of new version
- **2.0.0-beta.1**: Usable preview of new version
- **2.0.0-rc.1**: Full preview of new version
- **2.0.0**: Major changes

## Pre-release Version Identifiers

## Usage Pattern

All pre-release identifiers should follow the pattern `identifier.number` where number is incremental starting from 1.

Example progression:

```
v0.1.0-dev → v0.1.0-alpha.1 → v0.1.0-alpha.2 → v0.1.0-beta.1 → v0.1.0-rc.1 → v0.1.0-rc.2 → v0.1.0
```

## Identifier Descriptions

### `dev`

**Purpose**: Marks the beginning of development for a new version. **When to use**: Immediately after releasing the previous version. **Characteristics**:

- First commit after a release
- Code may be unstable or broken
- Features are in planning/early implementation
- Not intended for any external use
- Does not need changelog entries
- Usually does not have an incremental number (just `-dev`)

### `alpha.x`

**Purpose**: Early testing of new features. **When to use**: When the primary features are implemented but incomplete. **Characteristics**:

- Core functionality works but is unpolished
- Significant features may be missing
- Major bugs are expected
- Suitable for internal testing only
- APIs and data structures may still change dramatically
- For each significant batch of features/changes, increment: alpha.1 → alpha.2 → etc.

### `beta.x`

**Purpose**: Feature-complete testing phase. **When to use**: When all planned features are implemented but need polish/testing. **Characteristics**:

- All planned features are present
- No known critical bugs (non-blockers OK)
- APIs and data structures are relatively stable
- Suitable for brave external testers
- Performance optimizations may not be complete
- Minor features and polish may still be added
- For each significant improvement, increment: beta.1 → beta.2 → etc.

### `rc.x` (Release Candidate)

**Purpose**: Final testing before official release. **When to use**: When the software is believed to be ready for release. **Characteristics**:

- Feature-frozen (no new features added)
- Only bug fixes are added between RC versions
- All known serious bugs are fixed
- Performance is optimized
- Documentation is complete
- Ready for general testing
- Each time critical bugs are fixed, increment: rc.1 → rc.2 → etc.
- If no major issues are found, the last RC becomes the release

## Guidelines

1. **Version Progression**: Always progress forward (never go from beta back to alpha)
2. **Restarting Counters**: Each identifier starts its own counter from 1
3. **Changelog Entries**:
    - `dev` tags don't need changelog entries
    - Significant `alpha`, `beta`, and all `rc` versions should have entries
4. **Skipping Identifiers**: It's OK to skip identifiers if not needed (e.g., go straight from alpha to rc)
5. **Public Communication**: Generally only announce beta and rc versions publicly
6. **Compatibility**: Clarify compatibility expectations with each pre-release

## Special Cases

- **Emergency fixes** to a release can use `patch` identifier (e.g., `v1.0.0-patch.1`)
- **Experimental branches** can use feature identifiers (e.g., `v1.0.0-newui.3`)
- **Long-term development** branches could use `dev.x` with incrementing numbers

## Useful Resources

- [semver](https://semver.org/) - The official semver specification.
- [PEP 440](https://peps.python.org/pep-0440/) - Python's version identification and dependency specification.
- [Cargo's SemVer Guide](https://doc.rust-lang.org/cargo/reference/semver.html) - Rust's Cargo semver compatibility.
- [npm semver calculator](https://semver.npmjs.com/) - npm semver calculator.
