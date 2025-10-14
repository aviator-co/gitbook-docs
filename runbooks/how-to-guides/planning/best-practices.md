# Best practices

#### 1. Start with Clear Requirements <a href="#id-1-start-with-clear-requirements" id="id-1-start-with-clear-requirements"></a>

Before Runbooks generates the plan, provide comprehensive context:

```
"I want to migrate our authentication from OAuth1 to OAuth2.

Context:
- Current implementation: src/auth/oauth1.ts
- 50K active users, zero downtime required
- Must support both flows during 2-week migration window
- Need to preserve all existing session data
- Compliance requirement: audit trail for all auth changes

Success criteria:
- All existing users can continue logging in
- New users use OAuth2 automatically
- Complete migration data report for compliance
- No performance degradation"
```

#### 2. Reference Your Codebase <a href="#id-2-reference-your-codebase" id="id-2-reference-your-codebase"></a>

Point to specific files, patterns, and conventions:

```
"Follow the repository pattern established in src/repositories/
Each repository should:
- Extend BaseRepository
- Use dependency injection for the database client
- Include CRUD operations and custom queries
- Have corresponding tests in tests/repositories/"
```

#### 3. Iterate in Small Steps <a href="#id-3-iterate-in-small-steps" id="id-3-iterate-in-small-steps"></a>

Don't wait until everything is wrong to provide feedback:

```
"Step 1 looks good - please proceed with that.
For Step 2, let's discuss the database schema changes before implementation."
```

#### 4. Provide Examples from Your Codebase <a href="#id-4-provide-examples-from-your-codebase" id="id-4-provide-examples-from-your-codebase"></a>

```
"The error handling should match our pattern in src/services/PaymentService.ts:
- Custom error classes for different failure modes
- Structured logging with correlation IDs
- Retry logic with exponential backoff
- Circuit breaker for external services"
```

#### 5. Set Expectations for Testing <a href="#id-5-set-expectations-for-testing" id="id-5-set-expectations-for-testing"></a>

```
"After executing step 3:
1. Run the full test suite: npm test
2. Check for TypeScript errors: npm run type-check
3. Test manually in staging environment
4. Verify API response format with Postman collection in /docs/api
5. Run the migration script in a database dump

I'll review the PR and provide feedback on the implementation."
```

#### 6. Use Step Editing for Precision <a href="#id-6-use-step-editing-for-precision" id="id-6-use-step-editing-for-precision"></a>

When you need exact changes, edit the step directly rather than describing changes in chat. This ensures:

* No ambiguity in requirements
* Exact specifications are captured
* Changes are tracked in the runbook version

#### 7. Provide Feedback Continuously <a href="#id-7-provide-feedback-continuously" id="id-7-provide-feedback-continuously"></a>

Don't wait for all steps to execute. Review and comment as you go:

* After runbook generation: Review the overall plan
* During execution: Check each PR as it's created
* After testing: Report results and issues immediately

#### 8. Share Test Results <a href="#id-8-share-test-results" id="id-8-share-test-results"></a>

```
"Executed Step 4.1 - test results:

✅ Unit tests: All 45 tests passing
✅ Integration tests: Passing (3 min runtime)
❌ E2E tests: 2 failures in authentication flow
  - LoginTest.tsx: Expected redirect to /dashboard, got /login
  - SessionTest.tsx: Session cookie not being set

Logs show that the JWT token is generated but not included in the response headers.
Can you update Step 4.1 to include the token in the Authorization header?"
```

#### 9. Request Explanations When Needed <a href="#id-9-request-explanations-when-needed" id="id-9-request-explanations-when-needed"></a>

```
"Can you explain the approach in Step 3.2? I'm not sure why we're using
a WeakMap instead of a regular Map. What's the benefit for our use case?"
```

#### 10. Collaborate on Complex Decisions <a href="#id-10-collaborate-on-complex-decisions" id="id-10-collaborate-on-complex-decisions"></a>

```
"For Step 6 (database schema migration), I see three options:

1. Add new columns, migrate data, drop old columns (zero downtime)
2. Create new table, migrate data, swap tables (requires brief downtime)
3. Use database views for backward compatibility (complex but safe)

Our constraints:
- 24/7 uptime requirement
- 500GB database
- Must complete within maintenance window

Which approach do you recommend and why?"
```

