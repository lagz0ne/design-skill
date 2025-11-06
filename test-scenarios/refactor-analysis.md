# REFACTOR Phase Analysis

## Date
2025-11-06

## Skill Review

Reviewed `/home/lagz0ne/dev/design-skill/skills/system-design/SKILL.md` (457 lines) for potential loopholes, missing guidance, or areas where agents might rationalize around the rules.

## Current Strengths

### 1. Clear Iron Laws
- 6 laws with explicit "Bad" and "Good" examples
- Covers all major baseline violations
- No ambiguity in requirements

### 2. Comprehensive Rationalization Table
- 8 common excuses documented
- Each has counter-argument
- Directly addresses baseline rationalizations

### 3. Red Flags Section
- Self-check list for violations
- Clear "stop and start over" instruction
- Covers all major infractions

### 4. Phase Discipline
- TodoWrite requirement explicit
- 5 phases clearly defined
- Validation gates at each phase

### 5. Templates Provided
- 6 Mermaid templates
- 1 requirements template
- 1 catalog README template
- All in templates/ directory

## Potential Loopholes Identified

### 1. Token Efficiency Guideline Too Vague

**Issue:** "< 15K for typical project" in success criteria line 393

**Risk:** Agent might say "this isn't typical, it's complex" and use 50K tokens

**Fix:** Add more specific guidance

**Recommendation:**
```markdown
### Law 6: TOKEN EFFICIENCY - Diagrams Over Prose

Target token usage:
- Simple project (single service, 5-10 entities): < 10K tokens
- Medium project (2-3 services, 10-20 entities): < 20K tokens
- Complex project (multiple services, 20+ entities): < 35K tokens

If exceeding these targets:
- Are you writing prose that diagrams could show?
- Are you repeating information across files?
- Are you including implementation details?
```

### 2. Missing Guidance on "How Many" Artifacts

**Issue:** Phase 3 says "2-4 critical processes" but Phase 4 says "2-3 max" for sequences

**Risk:** Agent might create 10 process diagrams or 15 state charts

**Fix:** Add explicit limits

**Recommendation:**
```markdown
### Phase 3: Process EventStorming

Identify **2-4 critical processes** to detail (not more).

Criteria for "critical":
- Touches multiple aggregates
- Has complex business rules
- High business value
- High risk/uncertainty

### Phase 4: System Design Artifacts

**Quantity guidelines:**
- ERD: 1 diagram (all entities)
- State charts: 2-4 entities max (only complex lifecycles)
- Sequence diagrams: 2-4 flows max (only critical/complex)

Don't diagram everything - focus on what adds clarity.
```

### 3. No Guidance on Updating Artifacts During Iteration

**Issue:** Section on iterative refinement doesn't say HOW to update

**Risk:** Agent might create version 2 files instead of updating originals

**Fix:** Add update guidance

**Recommendation:**
```markdown
## Iterative Refinement

When returning to earlier phases:
1. **Update existing files** (don't create v2, v3 versions)
2. **Propagate changes downstream** (if Phase 2 changes, update Phase 3 artifacts)
3. **Mark what changed** (add comments in Mermaid diagrams if helpful)
4. **Re-validate** with user after updates

Example:
User: "Big picture is missing seller onboarding"
You:
- Update big-picture.mmd (add onboarding events)
- Check if processes/ need updating
- Check if ERD needs seller verification entity
- Present updated artifacts
```

### 4. Description Might Not Trigger for All Scenarios

**Current description:**
> "Use when designing a new system from requirements"

**Risk:** Might not match queries like:
- "Help me architect a solution for X"
- "I need to plan out a system for Y"
- "Let's design the backend for Z"

**Fix:** Enhance description with more trigger phrases

**Recommendation:**
```yaml
description: Use when designing, architecting, or planning a new system from requirements - transforms ideas into design catalog using EventStorming, Mermaid diagrams, and progressive elaboration through 5 phases (Requirements, Big Picture, Processes, Data/Flows, Integration)
```

### 5. No Explicit Prohibition on Creating Code Examples

**Issue:** Law 5 forbids SQL schemas, deployment guides, but doesn't mention code

**Risk:** Agent might add "example API endpoint code" to sequence diagrams

**Fix:** Add to Law 5

**Recommendation:**
```markdown
### Law 5: DESIGN NOT IMPLEMENTATION

**Forbidden in design artifacts:**
- ❌ Database schemas (SQL, migrations)
- ❌ Deployment guides (Docker, Kubernetes)
- ❌ CI/CD pipelines
- ❌ Specific technology choices (unless user specified)
- ❌ Implementation timelines (16-week plans)
- ❌ Team structure recommendations
- ❌ Code examples (API endpoints, controllers, services)
- ❌ Configuration files (YAML, JSON, ENV)
```

### 6. Unclear What to Do if User Resists Questions

**Issue:** Law 1 requires questions, but what if user says "just design it"?

**Risk:** Agent might skip questions to please user

**Fix:** Add guidance for resistant users

**Recommendation:**
```markdown
### Law 1: ASK QUESTIONS - No Assumptions

**If user resists questions:**

User: "Just design it, you know what I need"
You: "I need a few clarifications to create the right design for YOUR system. Let me ask 3-5 quick questions to understand context."

User: "Make assumptions, we can refine later"
You: "Assumptions risk wasting time on wrong design. 2-minute conversation now saves hours of rework. First question: {ask anyway}"

**Never skip questions because user seems impatient.**
```

## Recommendations Summary

| # | Issue | Severity | Fix Priority |
|---|-------|----------|--------------|
| 1 | Token limits too vague | Medium | High |
| 2 | No artifact quantity limits | Medium | High |
| 3 | No update guidance for iteration | Low | Medium |
| 4 | Description triggers incomplete | Low | Medium |
| 5 | Code examples not forbidden | Medium | High |
| 6 | No guidance for resistant users | Low | Low |

## Should We Apply Fixes Now?

**Recommendation:** Apply high-priority fixes (1, 2, 5) now. Medium/low priority can wait for actual failures in testing.

## New Rationalizations to Watch For

Based on reviewing the skill, agents under pressure might try:

1. **"This is a complex project, so 50K tokens is reasonable"** → Counter with specific token targets
2. **"Let me create diagrams for all 15 processes"** → Counter with 2-4 limit
3. **"Here's example API code to clarify the sequence diagram"** → Counter: forbidden
4. **"User said 'just design it' so I'll skip questions"** → Counter: ask anyway
5. **"I'll create big-picture-v2.mmd for the updated version"** → Counter: update originals

## Test Coverage Assessment

**Baseline test:** ✅ E-commerce (medium complexity)
**GREEN test:** ✅ E-commerce with skill

**Still need:**
- SaaS Auth test (high complexity, security-critical)
- IoT Platform test (high scale, real-time)
- Test with resistant user (doesn't want to answer questions)
- Test with scope creep mid-design (user adds major features)

## Conclusion

The skill is **fundamentally sound** but would benefit from:
1. More specific token targets (by project complexity)
2. Explicit artifact quantity limits
3. Explicit code example prohibition
4. Minor enhancements for edge cases

**Recommendation:** Apply high-priority fixes, then test with SaaS Auth scenario to validate security-critical design handling.
