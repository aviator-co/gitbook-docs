# Writing effective acceptance criteria

Good acceptance criteria are specific, verifiable, and complete. This guide shows how to write criteria that lead to accurate verification.

### Be specific

Vague criteria can’t be reliably verified.

**Too vague:**

```markdown
-[ ] Endpoint is secure
```

**Specific:**

```markdown
-[ ] Requires authentication via Bearer token
-[ ] Returns 401 for missing or invalid token
-[ ] Returns 403 if user lacks permission
```

**Too vague:**

```markdown
-[ ] Handles errors properly
```

**Specific:**

```markdown
-[ ] Returns 404 if resource not found
-[ ] Returns 503 if downstream service unavailable
-[ ] Error responses include correlation ID
```

### Include both positive and negative requirements

Say what should happen _and_ what shouldn’t.

```markdown
-[ ] Response includes: status, renewal_date, plan_name
-[ ] Response excludes: internal_id, billing_provider_id
```

```markdown
-[ ] Creates new user record
-[ ] Does not modify existing user records
```

Negative requirements often catch bugs that positive requirements miss.

### Cover error cases

Don’t just test the happy path.

```markdown
-[ ] Returns 400 for malformed request body
-[ ] Returns 404 if subscription not found
-[ ] Returns 409 if resource already exists
-[ ] Returns 503 if payment service unavailable
```

Think about what could go wrong and how your code should respond.

### Add performance requirements only when meaningful

```markdown
-[ ] P99 latency under 200ms
-[ ] No N+1 queries
```

Only include these if they’re actual requirements. Don’t add “P99 under 100ms” to everything just because it sounds good.

### Express constraints as criteria

Things that must _not_ happen:

```markdown
-[ ] No new external dependencies
-[ ] Does not modify database schemas
-[ ] Does not call billing-service directly
-[ ] No breaking changes to existing API
```

### Use concrete values

**Vague:**

```markdown
-[ ] Returns appropriate status code
```

**Concrete:**

```markdown
-[ ] Returns HTTP 201 on successful creation
```

**Vague:**

```markdown
-[ ] Response includes required fields
```

**Concrete:**

```markdown
-[ ] Response includes: id, name, created_at
```

### Group related criteria

For readability, group criteria by concern:

```markdown
## Acceptance Criteria

### Endpoint behavior
-[ ] Endpoint: `POST /api/v1/users`
-[ ] Accepts JSON request body
-[ ] Returns HTTP 201 on success

### Validation
-[ ] Returns 400 if email is missing
-[ ] Returns 400 if email format is invalid
-[ ] Returns 409 if email already exists

### Response
-[ ] Response includes: id, email, created_at
-[ ] Response excludes: password_hash

### Security
-[ ] Requires admin authentication
-[ ] Password is hashed before storage
```

### Common patterns

#### REST endpoint

```markdown
-[ ] Endpoint: `GET /api/v1/resource/{id}`
-[ ] Requires authentication
-[ ] Returns 200 with resource data
-[ ] Returns 404 if not found
-[ ] Returns 403 if user lacks access
-[ ] Response format matches ResourceSchema
```

#### Data mutation

```markdown
-[ ] Creates/updates record in database
-[ ] Validates input before persisting
-[ ] Returns created/updated resource
-[ ] Emits event to message queue
-[ ] Operation is idempotent
```

#### Integration

```markdown
-[ ] Calls external-service API
-[ ] Handles timeout with retry
-[ ] Handles 5xx with graceful degradation
-[ ] Logs external call with correlation ID
```

### Criteria to avoid

**Implementation details:**

```markdown
# Bad - specifies how, not what
-[ ] Uses Redis for caching
-[ ] Implements retry with exponential backoff
```

Better to focus on behavior:

```markdown
# Good - specifies observable behavior
-[ ] Caches response for 60 seconds
-[ ] Retries failed requests up to 3 times
```

**Subjective judgments:**

```markdown
# Bad - can't be objectively verified
-[ ] Code is clean and readable
-[ ] Performance is acceptable
```

**Compound criteria:**

```markdown
# Bad - multiple things in one criterion
-[ ] Validates input, creates record, and sends email
```

Split into separate criteria:

```markdown
# Good - one thing per criterion
-[ ] Validates input against schema
-[ ] Creates record in database
-[ ] Sends confirmation email
```

### See also

* Tutorial: Your first spec
* Reference: Spec format
* Explanation: How verification works
