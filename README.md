# Design Skill Marketplace

Claude Code marketplace for system design using EventStorming and Mermaid diagrams.

## Installation

Add this marketplace to Claude Code:

```bash
/plugin marketplace add lagz0ne/design-skill
```

Then install the system-design plugin:

```bash
/plugin install system-design@design-skill-marketplace
```

## Available Plugins

### System Design

**Description:** Transform requirements into navigable design catalogs using EventStorming methodology, Mermaid diagrams, and progressive elaboration

**Categories:** Architecture, Design, EventStorming, Documentation

**What you get:**
- EventStorming-based design process
- 5-phase progressive elaboration (Requirements → Big Picture → Processes → Data/Flows → Integration)
- Mermaid diagram templates (EventStorming, ERD, State Charts, Sequence Diagrams)
- Standardized catalog structure (`docs/design-catalog/`)
- Token-efficient visual artifacts

**Repository:** https://github.com/lagz0ne/design-skill

**Triggers:** "design a system", "architect a solution", "plan a backend"

---

## How It Works

The system-design skill guides you through 5 phases:

1. **Requirements** - Interactive questions to understand context (no assumptions)
2. **Big Picture** - EventStorming timeline with color-coded events/commands/actors
3. **Processes** - Detail 2-4 critical workflows with aggregates and policies
4. **Data & Flows** - ERD, state charts for complex entities, sequence diagrams for critical flows
5. **Integration** - Assemble navigable catalog with all diagrams embedded

### Output Structure

```
docs/design-catalog/
  README.md              # Navigation hub with all diagrams embedded
  requirements.md
  big-picture.mmd
  processes/
    process-{name}.mmd
  data/
    erd.mmd
    state-{entity}.mmd
  flows/
    sequence-{flow}.mmd
```

All diagrams use Mermaid format and follow EventStorming color conventions.

## Example: E-commerce Platform

The skill transforms a simple request into a complete design catalog:

**Input:** "I want to build an e-commerce platform where artisans can sell handmade products"

**Output:**
- 10 Mermaid diagrams (big picture, 3 processes, ERD, 2 states, 3 sequences)
- 14 hotspots identified for decisions
- Complete navigable catalog
- ~45K tokens (vs 49K baseline without skill)

See `test-scenarios/ecommerce/with-skill/` for full example.

## Marketplace Structure

```
design-skill-marketplace/
├── .claude/
│   └── skills/
│       └── system-design/      # The skill
├── .claude-plugin/
│   └── marketplace.json        # Plugin catalog
├── docs/
│   └── plans/                  # Design documentation
├── test-scenarios/             # TDD test cases
└── README.md                   # This file
```

## Support

- **Issues:** https://github.com/lagz0ne/design-skill/issues
- **Skill Documentation:** `.claude/skills/system-design/SKILL.md`

## License

MIT License

---

## For Developers

This skill was built using **TDD for documentation**:

1. **RED:** Baseline test (49K tokens, 0 questions, ASCII diagrams)
2. **GREEN:** Skill created (45K tokens, 6 questions, Mermaid)
3. **REFACTOR:** Closed loopholes (explicit limits, counters)

See `test-scenarios/` and `docs/plans/` for full development process.
