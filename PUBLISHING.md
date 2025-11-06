# Publishing the Design Skill Marketplace

This guide explains how to publish this marketplace to GitHub so others can use it.

## Prerequisites

- GitHub account
- Git configured with your credentials
- Repository created on GitHub (e.g., `lagz0ne/design-skill`)

## Step 1: Create GitHub Repository

1. Go to https://github.com/new
2. Repository name: `design-skill`
3. Description: "System design skill for Claude Code using EventStorming and Mermaid"
4. Public repository
5. Do NOT initialize with README (we already have one)
6. Click "Create repository"

## Step 2: Push to GitHub

```bash
# Add remote (replace with your GitHub username)
git remote add origin https://github.com/lagz0ne/design-skill.git

# Push all commits
git push -u origin main
```

## Step 3: Verify Repository Structure

GitHub should show:
```
.claude/
  skills/
    system-design/
      SKILL.md
      templates/
.claude-plugin/
  marketplace.json
docs/
test-scenarios/
LICENSE
README.md
```

## Step 4: Test Installation

Users can now install your marketplace:

```bash
# Add marketplace
/plugin marketplace add lagz0ne/design-skill

# Install the skill
/plugin install system-design@design-skill-marketplace
```

## Step 5: Announce & Share

Share your marketplace:
- Post on GitHub Discussions
- Share in Claude Code community
- Tweet about it
- Add to your profile README

## Updating the Skill

When you make changes:

```bash
# Make changes to .claude/skills/system-design/SKILL.md
git add .
git commit -m "Description of changes"
git push

# Update version in .claude-plugin/marketplace.json
# Users will get updates when they run:
# /plugin update system-design@design-skill-marketplace
```

## Marketplace.json Structure

Key fields:
- `name`: Unique marketplace identifier
- `owner`: Your name and email
- `metadata.version`: Marketplace version (bump on changes)
- `plugins[].version`: Skill version (bump when skill changes)
- `plugins[].source.url`: Your GitHub repository URL

## Version Numbering

Use semantic versioning (MAJOR.MINOR.PATCH):
- **MAJOR**: Breaking changes to skill interface
- **MINOR**: New features, backward compatible
- **PATCH**: Bug fixes, documentation updates

Current version: 1.0.0

## Support

- Issues: https://github.com/lagz0ne/design-skill/issues
- Discussions: https://github.com/lagz0ne/design-skill/discussions

## License

This marketplace uses MIT License. See LICENSE file.
