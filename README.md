# System Design Skill

A skill for Claude Code that transforms requirements into navigable design catalogs using EventStorming methodology and Mermaid diagrams.

## Overview

This skill guides Claude through structured system design using:
- **EventStorming** methodology for event-driven thinking
- **Mermaid diagrams** for visual, version-controllable artifacts
- **5-phase progressive elaboration** (Requirements → Big Picture → Processes → Data/Flows → Integration)
- **Catalog structure** for navigable design documentation

## Quick Start

**Location:** `skills/system-design/SKILL.md`

**Use when:**
- Designing a new system from requirements
- Need structured architecture thinking
- Want visual design artifacts
- Team collaboration on design

**Triggers:** "design a system", "architect a solution", "plan a backend"

## Key Features

### 6 Iron Laws

1. **ASK QUESTIONS** - No assumptions without clarification
2. **MERMAID ONLY** - No ASCII diagrams
3. **EVENTSTORMING** - Event-driven thinking required
4. **CATALOG STRUCTURE** - Standardized organization
5. **DESIGN NOT IMPLEMENTATION** - No code, SQL, or deployment details
6. **TOKEN EFFICIENCY** - Diagrams over prose

### Progressive Phases

1. **Requirements** - Ask questions, identify actors/constraints
2. **Big Picture** - EventStorming timeline with events/commands/actors
3. **Processes** - Detail 2-4 critical processes
4. **Data & Flows** - ERD + state charts + sequence diagrams
5. **Integration** - Assemble catalog, plan next steps

### Output Structure

```
docs/design-catalog/
  README.md              # Navigation hub
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

## Development

### TDD Methodology

This skill was created following **Test-Driven Development for documentation**:

1. **RED Phase:** Created baseline test without skill (documented failures)
2. **GREEN Phase:** Wrote skill to address failures, verified compliance
3. **REFACTOR Phase:** Identified loopholes, added explicit counters

### Test Scenarios

Located in `test-scenarios/`:

- **E-commerce:** Medium complexity, multi-actor system
- **SaaS Auth:** High complexity, security-critical, multi-tenancy
- **IoT Platform:** High scale, real-time processing

### Baseline vs With Skill

| Metric | Baseline | With Skill |
|--------|----------|------------|
| Questions Asked | 0 | 6 |
| Mermaid Diagrams | 0 (ASCII) | 10 |
| EventStorming | No | Yes |
| Catalog Structure | Random | Perfect |
| Hotspots Marked | 0 | 14 |

## Documentation

- **Design Document:** `docs/plans/2025-11-06-system-design-skill-design.md`
- **Skill File:** `skills/system-design/SKILL.md`
- **Templates:** `skills/system-design/templates/`
- **Test Results:** `test-scenarios/ecommerce/`

## Templates Provided

- `big-picture-template.mmd` - EventStorming timeline
- `process-template.mmd` - Process EventStorming
- `erd-template.mmd` - Entity-Relationship
- `state-template.mmd` - State chart
- `sequence-template.mmd` - Sequence diagram
- `requirements-template.md` - Requirements structure
- `catalog-readme-template.md` - Catalog README

## Integration

Works with:
- **brainstorming** skill - Phase 1 question patterns
- **writing-plans** skill - Optional handoff after design
- **pumped-design** skill - Map to pumped-fn catalog structure

## Token Efficiency

**Targets by complexity:**
- Simple (5-10 entities): < 10K tokens
- Medium (10-20 entities): < 20K tokens
- Complex (20+ entities): < 35K tokens

**Baseline comparison:** 49K tokens → 45K with skill (8% reduction + better quality)

## Success Criteria

- [x] Asks 3+ clarifying questions
- [x] Uses AskUserQuestion tool
- [x] 100% Mermaid diagrams (no ASCII)
- [x] Follows EventStorming color conventions
- [x] Creates catalog structure correctly
- [x] Marks hotspots instead of assumptions
- [x] Stays conceptual (no SQL, deployment)
- [x] Uses TodoWrite for phase tracking

## License

Part of the design-skill project.

## Contributing

Follow TDD methodology:
1. Create failing test scenario
2. Write/update skill to pass
3. Refactor to close loopholes

## Author

Created: 2025-11-06
