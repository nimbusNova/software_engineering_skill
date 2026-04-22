# Git Standards

Guidelines for version control hygiene, commit practices, and branch management.

---

## Branch Naming

### Feature Work
```
feature/TICKET-123-short-description
feature/add-user-authentication
feature/JIRA-456-payment-gateway
```

### Bug Fixes
```
bugfix/TICKET-456-fix-null-pointer
hotfix/critical-security-patch
```

### Maintenance
```
refactor/extract-payment-service
docs/update-api-reference
test/add-order-service-tests
chore/upgrade-dependencies
```

### Rules
- Use lowercase letters and hyphens
- Include ticket number when applicable
- Keep under 50 characters
- Describe what the branch does, not just the ticket

---

## Commit Messages

### Format
```
[type] subject

Body: Explain what and why, not how

Refs: TICKET-123
```

### Types

| Type | Use When |
|------|----------|
| `feat` | New feature or capability |
| `fix` | Bug fix |
| `refactor` | Code change without behavior change |
| `test` | Test-only changes |
| `docs` | Documentation changes |
| `chore` | Maintenance, dependencies, config |
| `perf` | Performance improvement |
| `style` | Formatting, semicolons, etc. |

### Subject Line Rules

- Use imperative mood: "Add" not "Added"
- No period at end
- Maximum 50 characters
- Describe the change, not the file

**Examples**
```
[feat] add user authentication flow
[fix] resolve null pointer in order service
[refactor] extract payment calculation logic
[test] add unit tests for cart calculator
[docs] update API authentication guide
```

### Body Guidelines

- Explain motivation for the change
- Contrast with previous behavior
- Reference relevant tickets
- Wrap at 72 characters

**Example**
```
[feat] implement retry logic for payment gateway

The payment gateway occasionally returns 503 errors during
high traffic periods. Without retry, these transient failures
cause orders to fail unnecessarily.

This change adds exponential backoff retry with:
- 3 maximum attempts
- 100ms initial delay, doubling each time
- Jitter to prevent thundering herd

Refs: PAY-456
```

---

## Commit Granularity

### Commit Early, Commit Often

- Commit at logical breakpoints
- Each commit should be reviewable independently
- Don't commit broken code (unless WIP branch)

### Atomic Commits

One logical change per commit:

```bash
# Good: Separate concerns
git commit -m "[feat] add email validation"
git commit -m "[feat] integrate validation into signup flow"
git commit -m "[test] add validation unit tests"

# Bad: Mixed concerns
git commit -m "Add validation and fix bug and update docs"
```

### Work in Progress

For long-running branches:
```
[WIP] implement payment processing

TODO:
- [ ] Handle error cases
- [ ] Add tests
- [ ] Update documentation
```

Clean up before merge:
```bash
git rebase -i main  # Squash WIP commits
git commit --amend  # Rewrite message
```

---

## Commit Frequency Guidelines

### During Development

Commit when you:
- Complete a self-contained function
- Fix a bug
- Pass a test
- Refactor a module
- Update documentation

### Before Push

- Review your commits: `git log --oneline`
- Squash "fix typo" or "address review" commits
- Ensure build passes
- Ensure tests pass

### Squashing Examples

```bash
# Interactive rebase to clean history
git rebase -i HEAD~5

# Mark commits:
# pick    = keep as-is
# reword  = keep, edit message
# squash  = combine with previous
# fixup   = combine, discard message
# drop    = remove
```

---

## Pull Request Workflow

### Before Creating PR

- [ ] Branch up to date with base: `git rebase main`
- [ ] All tests passing
- [ ] Lint/checks passing
- [ ] Self-review completed
- [ ] Commit messages clear

### PR Description Template

```markdown
## Summary
Brief description of changes

## Changes
- [feat] Description of feature 1
- [fix] Description of fix
- [refactor] Description of refactoring

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed

## Checklist
- [ ] Code follows project standards
- [ ] Documentation updated
- [ ] No breaking changes (or documented)

## Related
- Fixes TICKET-123
- Relates to TICKET-456
```

### Review Response

Address feedback with additional commits:
```
[fix] address review: use early return
[refactor] simplify error handling per feedback
```

Before merge, squash review commits:
```bash
git rebase -i main
# squash fixups into original commits
git push --force-with-lease
```

---

## Merge Strategy

### Prefer Rebase + Fast-Forward

Clean, linear history:
```bash
git checkout feature-branch
git rebase main
git checkout main
git merge feature-branch --ff-only
```

### When to Use Merge Commits

- Long-running feature branches
- Multiple contributors to same branch
- Want to preserve branch context

### Never Force Push to Shared Branches

```bash
# Only on your personal feature branches
git push --force-with-lease  # Safer than --force

# Never on main, develop, or shared branches
```

---

## Useful Commands

### Review Before Commit

```bash
git diff              # See unstaged changes
git diff --staged     # See staged changes
git add -p            # Stage changes interactively
```

### Fix Last Commit

```bash
git commit --amend              # Edit message or add changes
git commit --amend --no-edit    # Just add staged changes
```

### Undo Mistakes

```bash
git reset HEAD~1          # Undo last commit, keep changes
git reset --hard HEAD~1   # Undo last commit, discard changes
git revert <commit>       # Create reverse commit
```

### View History

```bash
git log --oneline --graph   # Compact graph view
git log --stat             # Show changed files
git show <commit>          # Show commit details
```

---

## Git Hygiene Checklist

### Daily
- [ ] Pull latest before starting work
- [ ] Commit at logical breakpoints
- [ ] Push commits to remote

### Before PR
- [ ] Rebase on latest base branch
- [ ] Squash WIP/fixup commits
- [ ] Write clear PR description
- [ ] Ensure CI passes

### During Review
- [ ] Respond to feedback promptly
- [ ] Make requested changes in new commits
- [ ] Squash review commits when approved

### After Merge
- [ ] Delete feature branch
- [ ] Verify in staging/production
- [ ] Close related tickets
