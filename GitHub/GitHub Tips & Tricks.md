## Branch Protection (Keeping Main Sacred)

1. Go to your repository on GitHub
2. Click on "Settings"
3. Select "Branches" from the sidebar
4. Under "Branch protection rules," click "Add rule"
5. Set up protection for `main`:
    - Require pull request reviews before merging
    - Require status checks to pass before merging
    - Require branches to be up to date before merging
    - Do not allow bypassing the above settings
    - Restrict who can push to matching branches

## PR Workflow Tips

### PR Templates

Create a `.github/pull_request_template.md` file:

``` markdown
## Description
[Describe your changes here]

## Related Issues
Closes #[issue number]

## Testing
[How did you test this?]

## Screenshots (if applicable)

## Checklist
- [ ] I've tested my changes
- [ ] I've updated documentation
- [ ] My code follows project style guidelines
- [ ] I've added tests (if applicable)
```

### PR Reviews

Tips for better reviews:

- Keep PRs small and focused
- Add comments to your own PR to explain complex sections
- Use GitHub's "Files changed" view filtering to break up reviews
- Respond to all comments, even with just a thumbs up
- Use "Suggestions" feature for small changes

## GitHub Project Management

### Issue Templates

Create templates in `.github/ISSUE_TEMPLATE/`:

**Bug Report Template**

``` yaml
name: Bug Report
description: Report a bug
body:
  - type: markdown
    attributes:
      value: |
        Thanks for taking the time to report a bug!
  - type: input
    id: description
    attributes:
      label: Bug Description
      description: What happened?
    validations:
      required: true
  - type: input
    id: expected
    attributes:
      label: Expected Behavior
      description: What should have happened?
    validations:
      required: true
  - type: textarea
    id: reproduction
    attributes:
      label: Steps to Reproduce
      description: How can we recreate this bug?
    validations:
      required: true
  - type: dropdown
    id: platform
    attributes:
      label: Platform
      options:
        - Windows
        - macOS
        - Linux
        - Web
        - Android
        - iOS
    validations:
      required: true
```

### GitHub Labels

Useful label sets for game development:

- `type: bug`, `type: feature`, `type: content`
- `priority: critical`, `priority: high`, `priority: medium`, `priority: low`
- `area: gameplay`, `area: graphics`, `area: sound`, `area: ui`
- `status: in-progress`, `status: blocked`, `status: review`

## GitHub Stars and Visibility

### Making a Professional Repository

Essential files to have:

- `README.md` - Game description, screenshots, how to play
- `CONTRIBUTING.md` - Rules for contributors
- `LICENSE.md` - Your open source license
- `.github/CODE_OF_CONDUCT.md` - Community standards

### Game Dev Specific Tips

- Use GitHub Pages to host a web build of your game
- Add animated GIFs of gameplay to your README
- Link to your itch.io page in your repo description
- Use GitHub Releases for official game versions
- Include a "Made with" section acknowledging tools/frameworks

## GitHub Actions for Manual Events

Sometimes you want to trigger workflows manually:

``` yaml
name: Build Game

on:
  workflow_dispatch:
    inputs:
      platform:
        description: 'Platform to build for'
        required: true
        default: 'all'
        type: choice
        options:
        - all
        - linux
        - windows
        - mac
        - web
```

Use this when you want to manually build for specific platforms rather than on every commit.
