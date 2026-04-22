# Planning Guide

Guidance for creating effective project plans with story point estimation.

## Story Point Reference

Use Fibonacci sequence: 1, 2, 3, 5, 8, 13, 21

| Points | Complexity | Description |
|--------|------------|-------------|
| 1 | Trivial | Typo fix, rename variable, config change |
| 2 | Simple | Well-understood, no surprises |
| 3 | Straightforward | Standard pattern, clear path |
| 5 | Moderate | Some complexity, may need research |
| 8 | Complex | Multiple moving parts, unknowns |
| 13 | Hard | High complexity, significant risk |
| 21+ | Epic | Must decompose into smaller phases |

**Rule**: If a phase estimates >8 points, break it down further.

## Phase Design Principles

### Size Guidelines
- **Minimum**: 1 point (don't create phases smaller than this)
- **Maximum**: 8 points (split if larger)
- **Sweet spot**: 3-5 points (1-2 days work)

### Phase Boundaries

Good phase boundaries produce something reviewable:

| Good Phase | Bad Phase |
|------------|-----------|
| "Database schema + migration" | "Part 1 of database work" |
| "API endpoint with tests" | "API work" |
| "Frontend component with unit tests" | "More frontend code" |
| "Integration with external API" | "Keep working on integration" |

### Phase Sequencing

Order phases to minimize risk and enable early feedback:

1. **Risk-first**: Address highest-risk items early
2. **Foundation-first**: Build bottom-up (data → logic → UI)
3. **Integration points**: Connect to external systems early to discover issues
4. **User-visible**: Deliver user-facing pieces early for feedback

## Dependency Mapping

### Types of Dependencies

**Technical Dependencies**
- Database schema must exist before DAO
- API contract must be defined before implementation
- Authentication must work before protected endpoints

**External Dependencies**
- Third-party API access
- Infrastructure provisioning
- Security review
- Legal/compliance approval

**Resource Dependencies**
- Specialist knowledge needed
- Shared resources (databases, environments)
- Review bandwidth

### Visualizing Dependencies

Use simple arrows or a table:

```
Phase 1 (Schema) → Phase 2 (DAO) → Phase 3 (API) → Phase 4 (UI)
                        ↓
                  Phase 2.5 (Migration)
```

## Risk Assessment

For each phase, ask:

1. **What could go wrong?**
   - Technical unknowns
   - External dependencies
   - Integration complexity

2. **How likely is it?**
   - Low: We've done this before
   - Medium: Some uncertainty
   - High: Uncharted territory

3. **What's the impact?**
   - Low: Can work around easily
   - Medium: Requires replanning
   - High: Blocks entire project

4. **Mitigation strategy**
   - Spike/prototype first
   - Fallback approach
   - Early validation
   - Contingency time

## Plan Template

```markdown
# Project Plan: [Name]

## Summary
- Goal: [One sentence]
- Total Points: [X]
- Phases: [N]
- Risk Level: Low / Medium / High

## Phase Breakdown

### Phase 1: [Name] — [X] points
**What**: [Deliverable description]
**Why this order**: [Dependency or risk rationale]

Tasks:
- [ ] Task 1
- [ ] Task 2

Done when:
- [ ] Criteria 1
- [ ] Criteria 2

---

[Repeat for each phase]

## Dependency Graph
```
[Visual or list]
```

## Risk Register
| # | Risk | Likelihood | Impact | Mitigation |
|---|------|------------|--------|------------|
| 1 | | | | |

## Change Buffer
Recommended: 20% contingency for unknowns
- Estimated: [X] points
- Buffer: [X * 0.2] points
- Total with buffer: [X * 1.2] points

## Rollback Plan
If we need to abort after Phase [N]:
1. [Steps to safely stop]
2. [Cleanup required]
3. [State to leave system in]
```

## Common Planning Pitfalls

### Over-Optimism
- **Symptom**: Everything estimates 2-3 points
- **Fix**: Be honest about uncertainty; use 5+ when appropriate

### Phase Bleed
- **Symptom**: Phases don't have clear boundaries
- **Fix**: Each phase must produce a concrete, reviewable output

### Hidden Dependencies
- **Symptom**: "Oh, we need X first" discovered mid-phase
- **Fix**: Map dependencies explicitly before starting

### Missing Edge Cases
- **Symptom**: Testing reveals unexpected scenarios
- **Fix**: Include edge case handling in phase estimates

## Estimation Calibration

If consistently off:

**Over-estimating** (finishing early)
- Good: Buffer for unknowns
- Bad: May be sandbagging or unclear requirements

**Under-estimating** (running late)
- Common causes: Unknown technical complexity, unclear requirements, interruptions
- Fix: Add spike phases for research, buffer for uncertainty

Track actual vs. estimated to improve over time.
