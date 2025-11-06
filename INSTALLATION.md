# Installation Instructions

## For Users

### Install the Marketplace

```bash
/plugin marketplace add lagz0ne/design-skill
```

### Install the System Design Skill

```bash
/plugin install system-design@design-skill-marketplace
```

### Verify Installation

The skill should now be available at:
```
~/.claude/plugins/marketplaces/design-skill-marketplace/.claude/skills/system-design/
```

### Using the Skill

The skill automatically activates when you use phrases like:

```
Design a system for a task management app
```

```
Help me architect a solution for an inventory system
```

```
I need to plan a backend for a notification service
```

Claude will:
1. Announce skill usage
2. Ask clarifying questions (no assumptions)
3. Create EventStorming diagrams with Mermaid
4. Build catalog in `docs/design-catalog/`

## Expected Output

After using the skill, you'll have:

```
docs/design-catalog/
  ├── README.md              # All diagrams embedded for easy preview
  ├── requirements.md         # Business goals, actors, constraints
  ├── big-picture.mmd        # EventStorming timeline
  ├── processes/
  │   └── process-*.mmd      # 2-4 detailed processes
  ├── data/
  │   ├── erd.mmd           # Entity-Relationship diagram
  │   └── state-*.mmd       # State charts for complex entities
  └── flows/
      └── sequence-*.mmd    # Sequence diagrams for critical flows
```

All diagrams use Mermaid and follow EventStorming conventions.

## Updating the Skill

To get the latest version:

```bash
/plugin update system-design@design-skill-marketplace
```

## Uninstalling

To remove the skill:

```bash
/plugin uninstall system-design@design-skill-marketplace
```

To remove the marketplace:

```bash
/plugin marketplace remove lagz0ne/design-skill
```

## Troubleshooting

**Skill not activating?**
- Check installation: `ls ~/.claude/plugins/marketplaces/design-skill-marketplace/`
- Verify skill exists: `cat ~/.claude/plugins/marketplaces/design-skill-marketplace/.claude/skills/system-design/SKILL.md`

**Can't find marketplace?**
- Ensure you used the correct command: `/plugin marketplace add lagz0ne/design-skill`
- Check marketplace list: Look for "design-skill-marketplace"

**Getting errors during installation?**
- Check internet connection
- Verify GitHub repository is accessible: https://github.com/lagz0ne/design-skill
- Try removing and re-adding marketplace

## Support

- **Repository:** https://github.com/lagz0ne/design-skill
- **Issues:** https://github.com/lagz0ne/design-skill/issues
- **Skill Documentation:** View SKILL.md in the repository

## What You Get

- ✅ EventStorming methodology for event-driven design
- ✅ 5-phase progressive elaboration process
- ✅ Mermaid diagram templates (7 types)
- ✅ Standardized catalog structure
- ✅ Token-efficient visual artifacts
- ✅ Complete design documentation
- ✅ Integration with brainstorming and writing-plans skills

## Example

See the e-commerce example in the repository:
https://github.com/lagz0ne/design-skill/tree/main/test-scenarios/ecommerce/with-skill

This shows a complete design catalog with all 10 diagrams embedded in the README.
