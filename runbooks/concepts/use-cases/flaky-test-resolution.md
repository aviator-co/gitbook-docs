# Flaky test resolution

Flaky test resolution with Runbooks involves using AI to analyze test failure patterns, identify the underlying causes of intermittent failures, and implement systematic fixes across your test suite. Unlike manual debugging that addresses tests one by one, Runbooks can identify common patterns and fix entire categories of flaky tests simultaneously.

### When to use

#### Ideal scenarios

* Intermittent ci/cd failures
* Timing-dependent test failures
* Environment-specific failures
* Large test suite instability
* Developer productivity impact

#### Perfect for teams that

* Experience frequent "retry CI" requests due to flaky tests
* Struggle with inconsistent test results across environments
* Need to improve CI/CD pipeline reliability
* Want to restore confidence in their test suite
* Have limited time to manually debug intermittent test failures

### Common flaky test patterns and solutions

#### Available flaky test resolution templates

**Async operation timing issues**

* Fixes tests that fail due to inadequate waiting for async operations
* Implements proper wait conditions and timeout handling
* Replaces arbitrary timeouts with condition-based waiting
* Adds retry logic for network-dependent operations

**Test data isolation problems**

* Resolves tests that interfere with each other due to shared state
* Implements proper test setup and teardown procedures
* Adds database transaction isolation for database tests
* Creates independent test data for each test case

**UI test stabilization**

* Fixes browser-based tests with timing and rendering issues
* Implements proper element waiting strategies
* Adds stable locator strategies resistant to UI changes
* Handles dynamic content and loading states

**Network and external service mocking**

* Replaces unreliable external service calls with stable mocks
* Implements proper network timeout and retry handling
* Adds fallback strategies for service unavailability
* Creates deterministic test environments

### Without using template

#### 1. Flaky test analysis

Start by describing the flaky test behavior:

```
"Our test suite has become unreliable with about 20% of CI runs failing due to flaky tests. The failures seem random and include:
- API tests that sometimes timeout
- UI tests that fail to find elements
- Database tests that occasionally fail with constraint violations
- Integration tests that pass locally but fail in CI"
```

**What Runbooks does:**

* Analyzes test failure logs and patterns across multiple CI runs
* Identifies common failure modes and their frequency
* Maps failures to specific test categories and underlying causes
* Creates a prioritized remediation plan based on impact and frequency

#### 2. Root cause identification

Runbooks performs deep analysis to identify:

* Timing issues
* State pollution
* Environment dependencies
* External dependencies
* Resource constraints

#### 3. Systematic fix implementation

The AI creates and executes a comprehensive fix strategy:

1. Immediate stabilization
2. Pattern-based fixes
3. Infrastructure improvements
4. Prevention measures

#### 4. Validation and monitoring

Runbooks implements validation and monitoring:

* Runs tests multiple times to verify stability improvements
* Implements test reliability monitoring
* Creates alerts for new flaky test patterns
* Documents fix patterns for future reference

### Real-world flaky test resolution examples

#### Example 1: async operation timing issues

**Flaky test pattern:**

```javascript
// Flaky: Sometimes fails due to timing
test('user data loads correctly', async () => {
  render(<UserProfile userId="123" />);

  // ❌ Flaky: May not be enough time
  await waitFor(() => {
    expect(screen.getByText('John Doe')).toBeInTheDocument();
  }, { timeout: 1000 });
});
```

**After Runbooks fix:**

```javascript
// Stable: Proper async handling
test('user data loads correctly', async () => {
  // Mock API response for deterministic testing
  const mockUser = { id: '123', name: 'John Doe' };
  jest.spyOn(api, 'getUser').mockResolvedValue(mockUser);

  render(<UserProfile userId="123" />);

  // ✅ Stable: Wait for specific condition, not arbitrary timeout
  await waitFor(() => {
    expect(screen.getByText('John Doe')).toBeInTheDocument();
  }, {
    timeout: 10000,
    interval: 100
  });

  // Verify the API was called correctly
  expect(api.getUser).toHaveBeenCalledWith('123');
});
```

#### Example 2: database test isolation

**Flaky test pattern:**

```javascript
// Flaky: Tests interfere with each other
describe('User Management', () => {
  test('creates new user', async () => {
    const user = await createUser({ email: 'test@example.com' });
    expect(user.email).toBe('test@example.com');
  });

  test('finds existing user', async () => {
    // ❌ Flaky: Depends on previous test state
    const user = await findUser('test@example.com');
    expect(user).toBeDefined();
  });
});
```

**After Runbooks fix:**

```javascript
// Stable: Proper test isolation
describe('User Management', () => {
  let testUserId;

  beforeEach(async () => {
    // Clean slate for each test
    await cleanupTestData();
  });

  afterEach(async () => {
    // Cleanup after each test
    if (testUserId) {
      await deleteUser(testUserId);
      testUserId = null;
    }
  });

  test('creates new user', async () => {
    const uniqueEmail = `test-${Date.now()}@example.com`;
    const user = await createUser({ email: uniqueEmail });
    testUserId = user.id;
    expect(user.email).toBe(uniqueEmail);
  });

  test('finds existing user', async () => {
    // ✅ Stable: Create own test data
    const uniqueEmail = `test-${Date.now()}@example.com`;
    const createdUser = await createUser({ email: uniqueEmail });
    testUserId = createdUser.id;

    const foundUser = await findUser(uniqueEmail);
    expect(foundUser.id).toBe(createdUser.id);
  });
});
```

#### Example 3: UI test stabilization

**Flaky test pattern:**

```javascript
// Flaky: Element may not be ready
test('submits form successfully', async () => {
  await page.goto('/contact');

  // ❌ Flaky: Button might not be clickable yet
  await page.click('#submit-button');

  // ❌ Flaky: Success message might not appear immediately
  const successMessage = await page.textContent('.success-message');
  expect(successMessage).toContain('Form submitted');
});
```

**After Runbooks fix:**

```javascript
// Stable: Proper waiting and error handling
test('submits form successfully', async () => {
  await page.goto('/contact');

  // ✅ Stable: Wait for page to be fully loaded
  await page.waitForLoadState('networkidle');

  // Fill form fields with proper waiting
  await page.fill('[data-testid="name-input"]', 'John Doe');
  await page.fill('[data-testid="email-input"]', 'john@example.com');
  await page.fill('[data-testid="message-input"]', 'Test message');

  // ✅ Stable: Wait for button to be enabled and visible
  const submitButton = page.locator('[data-testid="submit-button"]');
  await submitButton.waitFor({ state: 'visible' });
  await expect(submitButton).toBeEnabled();

  // Submit form and wait for response
  await submitButton.click();

  // ✅ Stable: Wait for success message with retry
  const successMessage = page.locator('[data-testid="success-message"]');
  await expect(successMessage).toBeVisible({ timeout: 10000 });
  await expect(successMessage).toContainText('Form submitted successfully');
});
```

### Advanced flaky test scenarios

#### Network-dependent test stabilization

**API integration test issues:**

```
"Our API integration tests are flaky due to:
- Network timeouts in CI environment
- External service availability issues
- Rate limiting causing intermittent failures
- Different response times in different environments"
```

**Comprehensive solution:**

1. Service mocking
2. Retry logic
3. Circuit breakers
4. Environment parity

**Implementation example:**

```javascript
// Stable API test with proper mocking and retry
test('fetches user data with retry logic', async () => {
  // Mock external API for reliability
  const mockApiResponse = { id: 1, name: 'John Doe' };

  nock('https://api.external-service.com')
    .get('/users/1')
    .reply(200, mockApiResponse);

  const user = await retryOperation(
    () => apiClient.getUser(1),
    { maxRetries: 3, retryDelay: 100 }
  );

  expect(user).toEqual(mockApiResponse);
});
```

#### Parallel test execution issues

**Race condition resolution:**

```
"Our tests fail when run in parallel due to:
- Shared database records being modified simultaneously
- Port conflicts for test servers
- File system conflicts for temporary files
- Shared cache or session state"
```

**Systematic fix approach:**

1. Resource isolation
2. Database partitioning
3. Port management
4. Temporary file isolation

#### Browser test stabilization

**Cross-browser flakiness:**

```
"Our browser tests exhibit different flaky behaviors across browsers:
- Chrome: Timing issues with animations
- Firefox: Element interaction problems
- Safari: Different event handling behavior
- Edge: Font rendering affecting layout tests"
```

**Browser-specific optimization:**

1. Browser configuration
2. Wait strategies
3. Event handling
4. Visual testing



***

### See also

* [improving-code-coverage.md](improving-code-coverage.md "mention")
* [bug-fixes.md](bug-fixes.md "mention")
