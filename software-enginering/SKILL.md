---
name: software-engineering
description: Universal software development workflow for crafting high-quality code across any language or framework. Use when the user wants to build software, implement features, fix bugs, refactor code, create new projects, or any software development task. Triggers on phrases like "write code for", "implement", "build a", "create an app", "fix this bug", "refactor", "add a feature", "develop", "program", "code"
---

# Software Engineering Skill

This skill guides a rigorous, phased software development workflow. It is language-agnostic and applies to any codebase or framework.

## Core Philosophy

- **Human-in-the-loop**: Stop for human review at the end of EACH phase
- **Quality first**: No cutting corners on testing, observability, or error handling
- **Incremental delivery**: Small, reviewable commits pushed frequently
- **Conversation over forms**: Requirements gathering is a dialogue, not a questionnaire

---

## Phase 0: Pre-Flight Checklist

Before starting, verify:

- Clear understanding of what the user wants to build
- Access to the codebase (existing) or agreement on new project structure
- Definition of "done" understood

If any are unclear, ask clarifying questions now.

---

## Phase 1: Requirements Deciphering

**Goal**: Through conversation, deeply understand what needs to be built.

### Activities

Engage in a dialogue to uncover:

**Functional (The "What")**

- What problem are we solving? Who has this problem?
- What should the user be able to do? (happy path)
- What could go wrong? How should we handle it? (error paths)
- Are there different user types with different permissions?

**Non-Functional (The "How Well")**

- Performance: How fast? How many concurrent users?
- Security: Authentication? Authorization? Data sensitivity?
- Reliability: Uptime requirements? Error budget?
- Scalability: Growth expectations over 6-12 months?

**Constraints & Context**

- Timeline pressure? Hard deadlines?
- Must-use technologies? Must-avoid technologies?
- Existing integrations this must work with?
- Budget constraints (hosting, third-party services)?

### Deliverable: requirements-spec.md, save it to the project's ./docs/{project_name} folder. if it doesn't exist, create one

Document the conversation outcomes:

```markdown
# Requirements Specification: [Feature/Project Name]

## Context
[Why are we building this? Business justification from conversation]

## Stakeholders
- Primary users: 
- Maintainers/owners:

## Functional Requirements

### Core Flow
[Describe the happy path based on user description]

### Error Handling
[Document error scenarios discussed]

### Edge Cases Identified
[List unusual but possible scenarios]

## Non-Functional Requirements
| Category | Requirement | Notes from conversation |
|----------|-------------|------------------------|
| Performance | | |
| Security | | |
| Reliability | | |

## Constraints & Assumptions
- Constraint: [Hard limit]
- Assumption: [Working hypothesis to validate]

## Out of Scope
[Explicitly excluded to prevent scope creep]

## Definition of Done
- [ ] All functional requirements implemented
- [ ] Unit tests >80% coverage
- [ ] Integration tests pass
- [ ] Observability in place
- [ ] Code reviewed and approved
```

### Human Review Gate #1 ✋

Present the requirements-spec.md:

> "Here's what I understood from our conversation. Does this capture everything? Are there gaps or misunderstandings? Please approve or suggest changes before I proceed to analyze the codebase."

**Do not proceed until explicit approval received.**

---

## Phase 2: Codebase Archaeology

**Goal**: Understand the existing system to ensure consistency.

### Activities

1. **Architecture Mapping**
  - High-level structure: How do components interact?
  - Data flow: Where does data enter, transform, exit?
  - Deployment model: How does this run in production?
2. **Pattern Discovery**
  - Coding conventions (naming, structure, style)
  - Design patterns in use
  - Error handling patterns
  - Testing patterns
3. **Integration Points**
  - Databases, APIs, message queues
  - Configuration management
  - Secrets handling
4. **Similar Code Search**
  - Find existing implementations of similar features
  - Identify reusable components
  - Note technical debt to avoid or clean up

### Deliverable: codebase-analysis.md

```markdown
# Codebase Analysis

## Architecture Overview
[Diagram or description of system structure]

## Key Patterns Identified
- Error handling: 
- Configuration:
- Testing approach:

## Integration Points
| Component | Purpose | Interface |
|-----------|---------|-----------|
| | | |

## Reference Implementations
[Links to similar features to model after]

## Technical Debt Notes
[Areas of concern or inconsistency]

## Recommended Approach
[How the new feature fits into existing architecture]
```

### Human Review Gate #2 ✋

Present findings:

> "I've analyzed the codebase. Here's what I found about architecture, patterns, and integration points. [Highlight any surprises or blockers]. Does this analysis align with your understanding? Any concerns before I create the implementation plan?"

**Do not proceed until explicit approval received.**

---

## Phase 3: Planning

**Goal**: Create a detailed, phased execution plan with story point estimates.

See [references/planning-guide.md](references/planning-guide.md) for detailed planning patterns.

### Activities

1. **Work Breakdown**
  - Decompose into small, deliverable phases
  - Each phase should produce something reviewable
  - Maximum phase size: 2-3 days of work
2. **Story Point Estimation**
  - Use Fibonacci sequence: 1, 2, 3, 5, 8, 13, 21
  - 1 = trivial (typo fix)
  - 3 = straightforward, well-understood
  - 5 = moderate complexity
  - 8 = complex, some unknowns
  - 13 = high complexity, needs breaking down
  - > 13 = must decompose smaller
3. **Dependency Mapping**
  - What must happen before what?
  - External dependencies (APIs, approvals, environments)
4. **Risk Identification**
  - What could derail this plan?
  - What's the mitigation?

### Deliverable: project-plan.md

```markdown
# Project Plan: [Feature/Project Name]

## Summary
- **Goal**: [One-liner from requirements]
- **Estimated Total**: [X] story points
- **Risk Level**: Low / Medium / High

## Phases

### Phase 1: [Name] — [X] points
**Goal**: [What this phase delivers]

| Task | Points | Notes |
|------|--------|-------|
| | | |
| | | |

**Definition of Done**:
- [ ] 
- [ ] 

**Review Checkpoint**: Human approval required ✋

---

### Phase 2: [Name] — [X] points
[Repeat structure...]

## Dependency Chain
[Visual or list showing order]

## Risk Register
| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| | Low/Med/High | Low/Med/High | |

## Rollback Strategy
[If we need to abort, how do we unwind?]
```

### Human Review Gate #3 ✋

Present the plan:

> "Here's my proposed plan. I've broken it into [N] phases with story point estimates. [Highlight any high-point phases that might need splitting]. The total estimate is [X] points. Does this sequencing make sense? Any concerns about scope, timeline, or approach?"

**Do not proceed until explicit approval received.**

---

## Phase 4: Implementation (Iterative)

**Goal**: Execute each phase from the plan, with human review between each.

### Per-Phase Workflow

#### 4.1 Pre-Phase Setup

- Create feature branch: `feature/TICKET-short-description`
- Verify dependencies are available
- Run existing tests to ensure baseline is green

#### 4.2 Development

- Write code following [references/implementation-standards.md](references/implementation-standards.md)
- Write tests alongside code (TDD preferred)
- Commit frequently with descriptive messages
- Push commits to remote regularly

#### 4.3 Phase Completion Checklist

- All phase tasks implemented
- Unit tests written and passing (>80% coverage)
- Integration tests passing (if applicable)
- Code follows project conventions
- No TODOs or FIXMEs left in code
- Observing standards for logging and error handling

#### 4.4 Human Review Gate #N ✋

Present to human:

> "Phase [N] complete. Here's what was implemented: [summary]. Key decisions made: [decisions]. Tests: [X] unit, [Y] integration, all passing. Commits pushed to branch [name]. Ready for your review before proceeding to Phase [N+1]."

**Do not proceed to next phase until explicit approval received.**

### If Phase Needs Revision

Sometimes a phase reveals new information that requires replanning:

1. Document what was learned
2. Explain impact on remaining phases
3. Propose plan adjustment
4. Get human approval for new plan

---

## Phase 5: Testing (Comprehensive)

**Goal**: Ensure the implementation is robust and correct.

See [references/testing-strategies.md](references/testing-strategies.md) for detailed testing guidance.

### Activities

1. **Unit Test Verification**
  - All business logic covered
  - Edge cases tested
  - Mock external dependencies properly
  - Coverage report generated
2. **Integration Test Execution**
  - Component interactions verified
  - Database operations tested
  - API contracts validated
  - Test in environment as close to production as possible
3. **Manual Verification**
  - Happy path walkthrough
  - Error scenario validation
  - Performance spot-check

### Deliverable: test-report.md

```markdown
# Test Report

## Unit Tests
- Total: [X]
- Passing: [X]
- Coverage: [X]%

## Integration Tests
- Total: [X]
- Passing: [X]

## Manual Verification
- [ ] Happy path: 
- [ ] Error handling: 
- [ ] Edge cases: 

## Known Gaps
[Any areas not tested and why]
```

### Human Review Gate: Pre-Delivery ✋

> "Testing complete. Unit coverage: [X]%, Integration tests: [Y] passing. [Highlight any gaps or concerns]. Ready to proceed to final review and delivery?"

**Do not proceed until explicit approval received.**

---

## Phase 6: Self-Review

**Goal**: Critical examination of the code before final delivery.

### Review Checklist

**Correctness**

- Logic handles all edge cases from requirements
- No race conditions or concurrency issues
- No security vulnerabilities (injection, leaks, etc.)

**Quality**

- Code is readable and self-documenting
- Complex sections have explanatory comments
- No dead code or unused imports
- No hardcoded values that should be configurable

**Standards Compliance**

- Follows [references/implementation-standards.md](references/implementation-standards.md)
- Observability: logging, metrics, tracing in place
- Error handling: graceful degradation, informative messages
- Code organization: separation of concerns, clean interfaces

**Performance**

- No obvious bottlenecks
- Resource usage is reasonable
- Asymptotic complexity is appropriate

### Deliverable: self-review-notes.md

Document any issues found and how they were resolved.

---

## Phase 7: Delivery

**Goal**: Ship the completed work.

### Final Steps

1. **Final Commit**
  - Clean commit history (squash if needed per project conventions)
  - Reference tickets/issues in commit messages
  - See [references/git-standards.md](references/git-standards.md)
2. **Documentation**
  - Update README if needed
  - Document new configuration options
  - Update API docs if applicable
3. **Pull Request / Merge**
  - Create PR with clear description
  - Link to requirements-spec.md and project-plan.md
  - Highlight any deviations from plan and why
  - Ensure CI passes
  - Merge to main
4. **Post-Delivery**
  - Deploy to staging/production per process
  - Verify in production
  - Monitor for errors
  - Close related tickets

### Final Summary to Human

> "Delivery complete! [Feature] is now merged and [deployed/staged]. Summary:
>
> - Total story points delivered: [X]
> - Phases completed: [N]
> - Test coverage: [X]%
> - Key commits: [SHAs]
>
> [Link to PR/commit]. Please verify in [environment] and let me know if any issues arise."

---

## Abort Criteria

Stop and escalate to human immediately if:

- Requirements change fundamentally mid-implementation
- Technical blockers emerge that invalidate the approach
- Timeline pressure threatens quality gates
- External dependencies fail

Do not proceed past a phase boundary if the phase is incomplete or defective.