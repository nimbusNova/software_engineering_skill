# Implementation Standards

Universal standards for code quality, observability, error handling, and organization. Applies to all languages and frameworks.

---

## Observability

### Logging

**Log Levels**
- `DEBUG`: Detailed information for development only
- `INFO`: Business-significant events (request received, job completed)
- `WARN`: Degraded functionality but system operational
- `ERROR`: Failure requiring intervention

**What to Log**
- Request/response at service boundaries (sanitized)
- State changes (created, updated, deleted)
- Errors with full context (stack traces, correlation IDs)
- Performance metrics (duration, throughput)

**What NOT to Log**
- PII (names, emails, IDs)
- Secrets (passwords, tokens, keys)
- Excessive detail in hot paths

**Structured Logging Format**
```json
{
  "timestamp": "2024-01-15T10:30:00Z",
  "level": "INFO",
  "message": "Order processed",
  "correlation_id": "abc-123",
  "service": "payment-service",
  "duration_ms": 145,
  "order_id": "order-456",
  "amount": 99.99
}
```

**Correlation IDs**
- Generate at entry point (API request, job start)
- Propagate through all downstream calls
- Include in all log entries
- Return in response headers

### Metrics

**Key Metrics to Track**
- Request rate (requests per second)
- Error rate (errors per request)
- Duration (p50, p95, p99 latencies)
- Business metrics (orders processed, users active)

**Cardinality Awareness**
- Avoid high-cardinality labels (user IDs, timestamps)
- Use bounded buckets for histograms

### Tracing

**When to Use**
- Distributed systems with multiple services
- Async processing pipelines
- Complex multi-step operations

**Span Attributes**
- Service name
- Operation name
- Start/end time
- Status (ok/error)
- Key parameters (sanitized)

---

## Error Handling

### Error Classification

| Type | Description | Example | Handling |
|------|-------------|---------|----------|
| **Input** | Invalid user input | Missing field, bad format | Return 400, clear message |
| **Auth** | Authentication/authorization | Bad token, no permission | Return 401/403, log attempt |
| **Not Found** | Resource doesn't exist | Invalid ID | Return 404 |
| **Transient** | Temporary failure | Timeout, rate limit | Retry with backoff |
| **Dependency** | External service failure | Database down, API error | Circuit breaker, degrade |
| **Fatal** | Unrecoverable error | Disk full, data corruption | Alert immediately, halt |

### Error Handling Patterns

**Fail Fast**
- Validate inputs at boundary
- Check preconditions early
- Throw/return errors immediately

**Graceful Degradation**
- Non-critical features can fail silently
- Return partial data when possible
- Queue for retry instead of failing

**Circuit Breaker**
- Track failure rate to external services
- Open circuit after threshold (fail fast)
- Half-open after cooldown (test recovery)
- Close circuit on success

**Retry Strategy**
- Exponential backoff: 100ms, 200ms, 400ms, 800ms...
- Maximum retries: 3-5
- Jitter to prevent thundering herd
- Only retry idempotent operations

### Error Messages

**For Users**
- Clear, actionable, no jargon
- Suggest next steps
- Do not expose internal details

**For Operators**
- Full context in logs
- Correlation ID for tracing
- Stack traces for unexpected errors

**Example**
```
User sees: "Unable to process payment. Please try again or use a different card."

Log contains: "Payment failed: gateway timeout, correlation_id=abc-123, 
               merchant_id=xyz, amount=99.99, duration_ms=30000"
```

---

## Code Organization

### Separation of Concerns

**Layered Architecture**
```
Presentation (HTTP handlers, CLI, UI)
    ↓
Business Logic (services, use cases)
    ↓
Data Access (repositories, clients)
    ↓
Infrastructure (database, external APIs)
```

**Dependency Direction**
- Inner layers don't know about outer layers
- Dependency injection for testability
- Interfaces define contracts

### Module/Package Organization

**By Feature (preferred)**
```
users/
  handler.go
  service.go
  repository.go
  model.go
ders/
  handler.go
  service.go
  ...
```

**By Layer**
```
handlers/
  user_handler.go
  order_handler.go
services/
  user_service.go
  order_service.go
```

### Configuration Management

**Externalize Configuration**
- Environment variables for secrets
- Config files for non-secrets
- Feature flags for behavior toggles

**Configuration Structure**
```yaml
# config.yaml
environment: production
server:
  port: 8080
  timeout_seconds: 30
database:
  host: ${DB_HOST}
  password: ${DB_PASSWORD}  # From env var
features:
  new_checkout: true
  beta_feature: false
```

**Validation**
- Validate config at startup
- Fail fast on missing required values
- Sensible defaults for optional values

### Interface Design

**Keep Interfaces Small**
```go
// Good: Focused interface
type OrderRepository interface {
    Get(ctx context.Context, id string) (*Order, error)
    Save(ctx context.Context, order *Order) error
}

// Bad: Kitchen sink interface
type Database interface {
    GetOrder(...)
    SaveOrder(...)
    GetUser(...)
    SaveUser(...)
    // 50 more methods
}
```

**Return Concrete Types, Accept Interfaces**
```go
// Accept interface
func ProcessOrder(repo OrderRepository, orderID string) error

// Return concrete type
func NewOrderService(repo OrderRepository) *OrderService
```

---

## Code Quality

### Naming Conventions

**General**
- Be explicit: `customerEmail` not `ce`
- Use verb-noun for functions: `validateInput`, `calculateTotal`
- Use noun for variables: `order`, `paymentMethod`
- Boolean: `isValid`, `hasPermission`, `shouldRetry`

**Consistency**
- Follow project conventions
- Match standard library style
- Be consistent within a file

### Comments

**When to Comment**
- Explain "why", not "what"
- Document non-obvious business logic
- Reference external specs or tickets
- Warn about gotchas

**When NOT to Comment**
- Obvious code: `i++ // increment i` ❌
- Outdated comments (worse than no comment)
- Instead of good naming

**Documentation Comments**
- Public APIs must be documented
- Include parameters, return values, errors
- Example usage when helpful

### Complexity Control

**Function Size**
- Aim for <50 lines
- Single responsibility
- Extract helpers for clarity

**Cyclomatic Complexity**
- Limit branches (if/switch) per function
- Early returns to reduce nesting
- Extract complex conditionals to named functions

**Deep Nesting**
```go
// Bad: Deep nesting
if user != nil {
    if user.Active {
        if hasPermission {
            // finally do work
        }
    }
}

// Good: Guard clauses
if user == nil {
    return error
}
if !user.Active {
    return error
}
if !hasPermission {
    return error
}
// do work
```

---

## Security Essentials

### Input Validation

**Validate at Boundary**
- Check types, ranges, formats
- Whitelist allowed values
- Reject unexpected fields

**Sanitization**
- Escape output (HTML, SQL, shell)
- Normalize Unicode
- Trim whitespace

### Secrets Management

**Never**
- Hardcode secrets in source
- Log secrets
- Return secrets in API responses
- Commit `.env` files

**Always**
- Use environment variables or secret managers
- Rotate credentials regularly
- Use least-privilege access

### Dependency Hygiene

- Pin versions
- Audit dependencies regularly
- Update for security patches promptly
- Prefer well-maintained libraries

---

## Performance Guidelines

### Premature Optimization

- First: Write clear, correct code
- Then: Measure to find bottlenecks
- Finally: Optimize hot paths only

### Common Optimizations

**Database**
- Index frequently queried columns
- Use connection pooling
- Batch operations when possible
- N+1 query elimination

**Caching**
- Cache expensive computations
- Set appropriate TTLs
- Invalidate on data change
- Cache at multiple layers

**Concurrency**
- Use worker pools for bounded concurrency
- Avoid unbounded goroutines/threads
- Protect shared state (locks, channels)
- Beware of deadlocks

### Resource Cleanup

- Close files, connections, handles
- Use defer/try-finally patterns
- Context cancellation for long operations
- Timeout on external calls

---

## Testing Considerations

See [testing-strategies.md](testing-strategies.md) for detailed testing guidance.

**Testability in Design**
- Dependency injection enables mocking
- Pure functions are easiest to test
- Avoid global state
- Separate I/O from logic

**Coverage Targets**
- Business logic: >80%
- Error handling: Test error paths
- Edge cases: Boundary values, empty inputs
