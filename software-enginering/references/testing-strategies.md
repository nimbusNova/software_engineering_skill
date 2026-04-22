# Testing Strategies

Comprehensive guide for unit and integration testing across all languages and frameworks.

---

## Testing Pyramid

```
    /\
   /  \  E2E Tests (Few, slow)
  /____\
 /      \  Integration Tests (Some, medium speed)
/________\
/          \  Unit Tests (Many, fast)
/____________\
```

**Distribution Goal**
- 70% Unit tests
- 20% Integration tests
- 10% E2E tests

---

## Unit Testing

### Scope

Test a single unit (function, class, module) in isolation.

**What to Test**
- Business logic and algorithms
- State transitions
- Edge cases and boundary conditions
- Error handling paths

**What NOT to Test**
- Framework code (assumed working)
- Simple getters/setters
- Implementation details that may change

### Test Structure (Arrange-Act-Assert)

```python
def test_calculate_total_with_discount():
    # Arrange
    cart = Cart(items=[Item(price=100), Item(price=50)])
    discount = Discount(percent=10)
    
    # Act
    total = calculate_total(cart, discount)
    
    # Assert
    assert total == 135  # (150 - 15)
```

### Isolation with Mocks

**When to Mock**
- External dependencies (database, API, filesystem)
- Non-deterministic behavior (random, time, UUID)
- Slow operations (network calls)

**What NOT to Mock**
- The unit under test itself
- Value objects and data structures
- Internal implementation details

**Mock Best Practices**
```python
# Good: Mock at boundary
def test_order_service_sends_email(mock_email_client):
    service = OrderService(email_client=mock_email_client)
    service.create_order(items)
    
    mock_email_client.send.assert_called_once()

# Bad: Mocking internals
def test_order_service(mock_internal_helper):
    # Too coupled to implementation
    mock_internal_helper.return_value = ...
```

### Coverage Guidelines

**Target: >80% for business logic**

Coverage is a metric, not a goal. Focus on:
- All branches (if/else paths)
- Loop iterations (zero, one, many)
- Error handling
- Boundary values

**Don't Chase 100%**
- Generated code excluded
- Simple getters/setters optional
- Focus on value, not vanity metric

### Table-Driven Tests

For testing multiple scenarios with same logic:

```go
func TestCalculateShipping(t *testing.T) {
    tests := []struct {
        name     string
        weight   float64
        zone     string
        expected float64
    }{
        {"light domestic", 0.5, "domestic", 5.00},
        {"heavy domestic", 10.0, "domestic", 15.00},
        {"light international", 0.5, "intl", 15.00},
        {"heavy international", 10.0, "intl", 45.00},
        {"zero weight", 0, "domestic", 0},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := CalculateShipping(tt.weight, tt.zone)
            assert.Equal(t, tt.expected, result)
        })
    }
}
```

### Edge Cases to Test

| Category | Examples |
|----------|----------|
| **Empty** | Empty list, zero value, empty string |
| **Single** | One item, minimum value |
| **Many** | Maximum capacity, pagination |
| **Null/Nil** | Missing optional fields |
| **Invalid** | Malformed input, wrong type |
| **Boundary** | Min/max values, off-by-one |
| **Concurrency** | Race conditions, simultaneous access |

---

## Integration Testing

### Scope

Test component interactions and external dependencies.

**What to Test**
- Database operations (read/write)
- API endpoints (request/response)
- Message queue interactions
- External service calls
- Configuration loading

**What NOT to Test**
- External services you don't control (mock these)
- Pure business logic (unit test instead)

### Test Database Strategy

**Options (best to worst)**

1. **TestContainers / Real Database**
   - Spin up real DB in container
   - Most realistic
   - Slower, but acceptable for integration tests

2. **In-Memory Database**
   - SQLite, H2
   - Fast, but may differ from production

3. **Shared Test Database**
   - Dedicated test instance
   - Risk of test pollution
   - Requires transaction rollback

**Best Practices**
```python
@pytest.fixture
database():
    # Setup
    db = TestDatabase()
    db.create_tables()
    yield db
    # Teardown
    db.drop_tables()

def test_user_repository_save(database):
    repo = UserRepository(database)
    user = User(name="Alice")
    
    repo.save(user)
    
    saved = repo.find_by_id(user.id)
    assert saved.name == "Alice"
```

### API Integration Tests

```python
def test_create_order_endpoint(client):
    # Given
    payload = {"items": [{"id": "1", "qty": 2}]}
    
    # When
    response = client.post("/orders", json=payload)
    
    # Then
    assert response.status_code == 201
    assert response.json()["id"]
    assert response.json()["total"] == 199.98
    
    # Verify side effects
    order = db.query(Order).get(response.json()["id"])
    assert order.status == "pending"
```

### External Service Testing

**Real Calls (when safe)**
- Idempotent read-only operations
- Sandbox/test environments
- Contract validation

**Mocked Calls (when risky)**
- Mutating operations
- Rate-limited APIs
- Expensive calls

**Contract Tests**
```python
def test_payment_gateway_contract():
    """Verify our client works with actual API schema"""
    response = payment_client.charge(
        amount=1.00,  # Minimal test charge
        token="test_token"
    )
    
    assert response.transaction_id
    assert response.status in ["approved", "declined"]
```

### Test Data Management

**Factories over Fixtures**

```python
# Factory: Flexible, DRY
def create_user(overrides=None):
    defaults = {
        "name": "Test User",
        "email": "test@example.com",
        "active": True
    }
    defaults.update(overrides or {})
    return User(**defaults)

# Usage
user = create_user({"name": "Alice", "active": False})
```

**State Setup Per Test**
- Each test creates its own data
- Avoid shared state between tests
- Clean up after test (or use transactions)

---

## Test Organization

### File Structure

Mirror production structure:
```
src/
  users/
    service.py
    repository.py
tests/
  unit/
    users/
      test_service.py
      test_repository.py
  integration/
    test_api.py
    test_database.py
```

Or co-locate:
```
src/
  users/
    service.py
    service_test.py
    repository.py
    repository_test.py
```

### Naming Conventions

**Files**
- `*_test.py` (Python, Go)
- `*.spec.js` (JavaScript)
- `*.test.ts` (TypeScript)

**Test Functions**
- `test_<function>_<scenario>()`
- `test_<function>_should_<expected>_when_<condition>()`

Examples:
```python
def test_calculate_total_applies_discount()
def test_create_order_fails_when_out_of_stock()
def test_validate_email_rejects_invalid_format()
```

### Test Categories with Markers

```python
@pytest.mark.unit
def test_calculate_total():
    ...

@pytest.mark.integration
def test_database_save():
    ...

@pytest.mark.slow
def test_end_to_end():
    ...
```

Run selectively:
```bash
pytest -m unit        # Only unit tests
pytest -m "not slow"  # Exclude slow tests
```

---

## Assertion Best Practices

### One Logical Assertion Per Test

```python
# Good: Clear what failed
def test_order_created_with_correct_total():
    order = create_order(items=[Item(price=10), Item(price=20)])
    assert order.total == 30

def test_order_created_with_pending_status():
    order = create_order(items=[Item(price=10)])
    assert order.status == "pending"

# Bad: Multiple unrelated checks
def test_order():
    order = create_order(items=[Item(price=10), Item(price=20)])
    assert order.total == 30
    assert order.status == "pending"
    assert order.id
    assert order.created_at
```

### Descriptive Assertions

```python
# Good: Clear what went wrong
assert order.total == 30, f"Expected total 30, got {order.total}"

# Or use assertion libraries with good defaults
assert.equal(order.total, 30)  # "Expected 30, but got 25"
```

### Property-Based Testing (Advanced)

Generate test cases automatically:

```python
@given(st.lists(st.integers(min_value=0), min_size=1))
def test_sum_always_positive(numbers):
    result = sum(numbers)
    assert result >= 0
```

---

## Common Testing Mistakes

### Testing Implementation Details

```python
# Bad: Tests internal method calls
def test_process_calls_helper(mock_helper):
    process(data)
    mock_helper.assert_called_once()  # Fragile

# Good: Tests outcome
def test_process_calculates_correct_result():
    result = process(data)
    assert result == expected  # Stable
```

### Brittle Tests

- Overly specific mocks (break on refactoring)
- Hardcoded IDs that may change
- Time-dependent tests (use time mocking)

### Flaky Tests

- Race conditions in async code
- Timeouts too aggressive
- Shared state between tests
- External service dependencies

### Slow Tests

- Too much setup
- Testing through many layers
- Real database when in-memory would do
- No test categorization

---

## CI/CD Integration

### Pre-Commit
- Fast unit tests only
- Lint and format checks

### Pre-Merge (CI)
- Full unit test suite
- Integration tests
- Coverage report

### Post-Merge
- E2E tests
- Performance tests
- Security scans

### Coverage Gates
```yaml
# Example CI config
coverage:
  unit: 80%
  integration: 60%
  overall: 75%
```

---

## Test Maintenance

### When to Delete Tests
- Testing deprecated functionality
- Duplicate coverage
- Overly brittle, can't fix
- Requirements changed, test invalid

### When to Update Tests
- Requirements change
- Refactoring affects test (but not behavior)
- Bug found → add regression test first

### Keep Tests Clean
- Refactor test code like production code
- Extract common setup to fixtures/helpers
- Remove commented-out tests
- Document why, not what
