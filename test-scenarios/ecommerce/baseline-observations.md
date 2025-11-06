# Baseline Test Observations: E-commerce Platform

## Test Date
2025-11-06

## Agent Behavior WITHOUT system-design Skill

### What Happened

**Questions Asked:** ZERO
- Agent made assumptions instead of clarifying requirements
- No use of AskUserQuestion tool
- No iterative dialogue

**Artifacts Created:** 12 documents, 4,180 lines, ~49,000 tokens consumed

**Format:**
- ASCII text diagrams (not Mermaid)
- Markdown documentation
- SQL schema

**Methodology:**
- Traditional waterfall approach
- 7 design phases
- NOT EventStorming, NOT DDD

**Token Usage:** ~49,000 tokens (VERY HIGH)

### Key Problems Identified

#### 1. **No User Engagement**
- Zero questions asked
- Made assumptions about scale, budget, timeline
- No AskUserQuestion usage
- No iterative refinement

#### 2. **Token Inefficiency**
- 49,000 tokens for one scenario
- Verbose prose over diagrams
- ASCII diagrams instead of Mermaid
- Walls of text instead of visual artifacts

#### 3. **Wrong Diagram Format**
- Used ASCII art instead of Mermaid
- Not version-control friendly
- Not reusable
- Not following conventions

#### 4. **No EventStorming Methodology**
- Traditional waterfall instead of event-driven thinking
- Missed domain events identification
- No color-coding for elements
- No hotspot identification

#### 5. **Premature Implementation Detail**
- Created database schema (too early)
- Specified exact technologies (Node.js, Stripe)
- 16-week implementation plan (not design phase)
- Deployment guides (wrong phase)

#### 6. **No Catalog Structure**
- Random file organization
- No standardized naming
- No navigation hub concept
- Mixed concerns (design + implementation)

#### 7. **No Phase Discipline**
- Jumped from requirements to implementation
- No intermediate design artifacts
- No progressive elaboration
- No validation gates

#### 8. **Completeness Without Clarity**
- Created comprehensive docs but hard to navigate
- Token-heavy means hard to maintain
- No clear design story
- Mixed abstraction levels

### Expected vs Actual

| Expected (with skill) | Actual (baseline) |
|----------------------|------------------|
| 5 clear phases | 7 waterfall phases |
| Mermaid diagrams | ASCII art |
| EventStorming | Traditional analysis |
| Token-efficient | 49,000 tokens |
| Catalog structure | Random files |
| Iterative dialogue | Zero questions |
| Requirements → Events → Design | Requirements → Implementation |
| Hotspots marked | All assumptions made |

### Rationalizations Used

1. **"I proceeded with industry-standard assumptions"** - Instead of asking questions
2. **"ASCII is version control friendly"** - Instead of using Mermaid
3. **"Comprehensive documentation"** - Instead of focused design artifacts
4. **"Production-ready blueprint"** - Jumped to implementation details too early
5. **"Traditional waterfall methodology"** - No structured design thinking
6. **"All aspects covered"** - Token explosion from completeness
7. **"Based on proven patterns"** - Generic instead of project-specific

### What the Skill MUST Address

#### Critical Rules to Enforce

1. **ASK QUESTIONS** - No assumptions without clarification
2. **USE MERMAID** - No ASCII diagrams, no exceptions
3. **FOLLOW EVENTSTORMING** - Event-driven thinking required
4. **5 PHASES** - Progressive elaboration, validation gates
5. **TOKEN EFFICIENCY** - Diagrams over prose
6. **CATALOG STRUCTURE** - Standardized organization
7. **DESIGN NOT IMPLEMENTATION** - No database schemas, no deployment guides
8. **MARK HOTSPOTS** - Don't make assumptions, mark unclear areas

#### Patterns to Include

- Phase-by-phase checklist with TodoWrite
- Mermaid templates with examples
- EventStorming color conventions
- Catalog structure enforcement
- Token efficiency reminders
- Iterative refinement support

#### Rationalizations to Counter

| Rationalization | Counter |
|----------------|---------|
| "Industry standard assumptions" | "Ask questions to understand THIS project" |
| "ASCII is fine" | "Mermaid is required - version control + rendering" |
| "Comprehensive is better" | "Token-efficient diagrams are better" |
| "Traditional approach works" | "EventStorming reveals domain events you'd miss" |
| "Production-ready blueprint" | "Design phase stays at design level" |
| "All aspects covered" | "Focus on design artifacts, not implementation" |

### Success Metrics for Skill

When skill is working correctly:

- [ ] Agent asks 3+ questions in Phase 1
- [ ] Agent uses AskUserQuestion tool
- [ ] All diagrams in Mermaid format
- [ ] Follows EventStorming color conventions
- [ ] Creates catalog structure correctly
- [ ] Token usage < 15,000 for similar scenario
- [ ] Marks hotspots instead of making assumptions
- [ ] Stays in design phase (no SQL schemas, no deployment)
- [ ] Uses TodoWrite to track phases

### Next Steps

1. Write SKILL.md addressing these specific failures
2. Include explicit counters to rationalizations
3. Add templates for Mermaid diagrams
4. Test with same scenario - should be dramatically different
5. Measure token reduction and question count
