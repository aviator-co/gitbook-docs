# Improving code coverage

Code coverage improvement with Runbooks involves using AI to analyze your existing test suite, identify coverage gaps, and automatically generate comprehensive tests to improve overall code coverage. Unlike manual test writing that can miss edge cases and be time-consuming, Runbooks can systematically ensure all code paths are tested with meaningful, maintainable test cases.

### When to use

#### Ideal scenarios

* Low test coverage
* Legacy code testing
* Critical path validation
* Regression prevention
* Ci/cd quality gates

#### Perfect for teams that

* Need to meet specific code coverage targets for compliance or quality
* Have legacy codebases with minimal test coverage
* Want to improve confidence in deployments and releases
* Need to add tests quickly without sacrificing quality
* Struggle with writing comprehensive edge case tests

### Code coverage improvement strategies

#### Available coverage enhancement templates

**Unit test generation**

* Generates comprehensive unit tests for untested functions and methods
* Creates tests for edge cases and boundary conditions
* Implements proper mocking and isolation patterns
* Adds parameterized tests for multiple input scenarios

**Integration test creation**

* Builds integration tests for component interactions
* Tests API endpoints with various input combinations
* Validates database operations and data flow
* Creates end-to-end workflow testing

**Edge case and error handling tests**

* Identifies and tests error conditions and exception paths
* Creates tests for boundary values and invalid inputs
* Validates error messages and recovery mechanisms
* Tests timeout and resource constraint scenarios

**Regression test suite development**

* Converts bug reports into permanent regression tests
* Creates tests that prevent specific issues from reoccurring
* Builds comprehensive test suites for critical business logic
* Implements property-based testing for complex scenarios

### Without using templates

#### 1. Coverage analysis and gap identification

Start by describing your coverage improvement goals:

```
"Our application has only 45% test coverage and we need to reach 80% before our next release. Key areas that need coverage include:
- Payment processing logic (currently 20% covered)
- User authentication and authorization (currently 60% covered)
- Data validation and sanitization functions (currently 30% covered)
- Error handling and recovery mechanisms (currently 15% covered)
- API endpoints and their various response scenarios"
```

**What Runbooks does:**

* Analyzes your current test suite and coverage reports
* Identifies specific functions, classes, and code paths lacking coverage
* Maps critical business logic that requires comprehensive testing
* Creates a prioritized plan focusing on high-risk, low-coverage areas

#### 2. Coverage gap analysis

Runbooks performs comprehensive analysis:

* **Line coverage**
* **Branch coverage**
* **Function coverage**
* **Edge case coverage**
* **Integration points**

#### 3. Test generation strategy

The AI creates a systematic test generation plan:

1. **Critical path coverage**
2. **Edge case generation**
3. **Integration testing**
4. **Regression prevention**
5. **Maintenance strategy**

#### 4. Implementation and validation

Runbooks implements coverage improvements systematically:

* Generates tests that follow existing project patterns and conventions
* Creates meaningful test names and clear assertions
* Implements proper setup and teardown procedures
* Validates that new tests actually improve meaningful coverage

### Real-world code coverage examples

#### Example 1: Payment processing coverage

**Uncovered payment function:**

```javascript
// Original function with no test coverage
function processPayment(amount, paymentMethod, userAccount) {
  if (amount <= 0) {
    throw new Error('Amount must be positive');
  }

  if (!paymentMethod || !paymentMethod.isValid) {
    throw new Error('Invalid payment method');
  }

  if (userAccount.balance < amount && paymentMethod.type !== 'credit') {
    throw new Error('Insufficient funds');
  }

  const transaction = {
    id: generateTransactionId(),
    amount: amount,
    method: paymentMethod.type,
    timestamp: new Date(),
    status: 'pending'
  };

  if (amount > 10000) {
    transaction.requiresApproval = true;
    notifyAdministrators(transaction);
  }

  const result = chargePaymentMethod(paymentMethod, amount);

  if (result.success) {
    transaction.status = 'completed';
    updateUserBalance(userAccount, amount, paymentMethod.type);
  } else {
    transaction.status = 'failed';
    transaction.errorCode = result.errorCode;
  }

  saveTransaction(transaction);
  return transaction;
}
```

**Generated comprehensive test suite:**

```javascript
describe('processPayment', () => {
  let mockPaymentMethod;
  let mockUserAccount;

  beforeEach(() => {
    mockPaymentMethod = {
      type: 'credit',
      isValid: true
    };

    mockUserAccount = {
      id: 'user123',
      balance: 1000
    };

    // Mock external dependencies
    jest.spyOn(global, 'generateTransactionId').mockReturnValue('tx123');
    jest.spyOn(global, 'chargePaymentMethod').mockResolvedValue({ success: true });
    jest.spyOn(global, 'updateUserBalance').mockImplementation();
    jest.spyOn(global, 'saveTransaction').mockImplementation();
    jest.spyOn(global, 'notifyAdministrators').mockImplementation();
  });

  afterEach(() => {
    jest.restoreAllMocks();
  });

  describe('input validation', () => {
    test('throws error for zero amount', () => {
      expect(() => processPayment(0, mockPaymentMethod, mockUserAccount))
        .toThrow('Amount must be positive');
    });

    test('throws error for negative amount', () => {
      expect(() => processPayment(-100, mockPaymentMethod, mockUserAccount))
        .toThrow('Amount must be positive');
    });

    test('throws error for null payment method', () => {
      expect(() => processPayment(100, null, mockUserAccount))
        .toThrow('Invalid payment method');
    });

    test('throws error for invalid payment method', () => {
      mockPaymentMethod.isValid = false;
      expect(() => processPayment(100, mockPaymentMethod, mockUserAccount))
        .toThrow('Invalid payment method');
    });
  });

  describe('balance validation', () => {
    test('throws error for insufficient debit card funds', () => {
      mockPaymentMethod.type = 'debit';
      mockUserAccount.balance = 50;

      expect(() => processPayment(100, mockPaymentMethod, mockUserAccount))
        .toThrow('Insufficient funds');
    });

    test('allows credit card payment regardless of balance', () => {
      mockPaymentMethod.type = 'credit';
      mockUserAccount.balance = 50;

      expect(() => processPayment(100, mockPaymentMethod, mockUserAccount))
        .not.toThrow();
    });

    test('allows debit payment when balance is sufficient', () => {
      mockPaymentMethod.type = 'debit';
      mockUserAccount.balance = 150;

      expect(() => processPayment(100, mockPaymentMethod, mockUserAccount))
        .not.toThrow();
    });
  });

  describe('high-value transaction handling', () => {
    test('requires approval for transactions over $10,000', () => {
      const result = processPayment(15000, mockPaymentMethod, mockUserAccount);

      expect(result.requiresApproval).toBe(true);
      expect(notifyAdministrators).toHaveBeenCalledWith(
        expect.objectContaining({
          amount: 15000,
          requiresApproval: true
        })
      );
    });

    test('does not require approval for transactions under $10,000', () => {
      const result = processPayment(5000, mockPaymentMethod, mockUserAccount);

      expect(result.requiresApproval).toBeUndefined();
      expect(notifyAdministrators).not.toHaveBeenCalled();
    });

    test('requires approval for exactly $10,001', () => {
      const result = processPayment(10001, mockPaymentMethod, mockUserAccount);

      expect(result.requiresApproval).toBe(true);
      expect(notifyAdministrators).toHaveBeenCalled();
    });
  });

  describe('payment processing', () => {
    test('creates completed transaction for successful payment', () => {
      chargePaymentMethod.mockResolvedValue({ success: true });

      const result = processPayment(100, mockPaymentMethod, mockUserAccount);

      expect(result).toEqual({
        id: 'tx123',
        amount: 100,
        method: 'credit',
        timestamp: expect.any(Date),
        status: 'completed'
      });

      expect(updateUserBalance).toHaveBeenCalledWith(
        mockUserAccount, 100, 'credit'
      );
      expect(saveTransaction).toHaveBeenCalledWith(result);
    });

    test('creates failed transaction for unsuccessful payment', () => {
      chargePaymentMethod.mockResolvedValue({
        success: false,
        errorCode: 'CARD_DECLINED'
      });

      const result = processPayment(100, mockPaymentMethod, mockUserAccount);

      expect(result).toEqual({
        id: 'tx123',
        amount: 100,
        method: 'credit',
        timestamp: expect.any(Date),
        status: 'failed',
        errorCode: 'CARD_DECLINED'
      });

      expect(updateUserBalance).not.toHaveBeenCalled();
      expect(saveTransaction).toHaveBeenCalledWith(result);
    });
  });

  describe('edge cases', () => {
    test('handles very small amounts correctly', () => {
      const result = processPayment(0.01, mockPaymentMethod, mockUserAccount);

      expect(result.amount).toBe(0.01);
      expect(result.status).toBe('completed');
    });

    test('handles maximum safe integer amount', () => {
      const maxAmount = Number.MAX_SAFE_INTEGER;
      const result = processPayment(maxAmount, mockPaymentMethod, mockUserAccount);

      expect(result.amount).toBe(maxAmount);
      expect(result.requiresApproval).toBe(true);
    });

    test('preserves payment method type in transaction', () => {
      mockPaymentMethod.type = 'paypal';
      const result = processPayment(100, mockPaymentMethod, mockUserAccount);

      expect(result.method).toBe('paypal');
    });
  });
});
```

#### Example 2: Error handling coverage

**Uncovered error handling logic:**

```python
# API endpoint with poor error handling coverage
def create_user_account(username, email, password):
    try:
        # Validate input
        if not username or len(username) < 3:
            return {'error': 'Username must be at least 3 characters'}, 400

        if not email or '@' not in email:
            return {'error': 'Invalid email address'}, 400

        if not password or len(password) < 8:
            return {'error': 'Password must be at least 8 characters'}, 400

        # Check if user exists
        existing_user = database.find_user(email=email)
        if existing_user:
            return {'error': 'Email already registered'}, 409

        # Create user
        user_data = {
            'username': username,
            'email': email,
            'password': hash_password(password),
            'created_at': datetime.utcnow(),
            'active': True
        }

        user_id = database.create_user(user_data)
        send_welcome_email(email, username)

        return {'user_id': user_id, 'message': 'Account created successfully'}, 201

    except DatabaseConnectionError:
        logger.error('Database connection failed during user creation')
        return {'error': 'Service temporarily unavailable'}, 503

    except EmailServiceError:
        # User was created but email failed - that's ok
        logger.warning(f'Welcome email failed for user {username}')
        return {'user_id': user_id, 'message': 'Account created successfully'}, 201

    except Exception as e:
        logger.error(f'Unexpected error in create_user_account: {str(e)}')
        return {'error': 'Internal server error'}, 500
```

**Generated error handling test suite:**

```python
import pytest
from unittest.mock import patch, MagicMock
from datetime import datetime

class TestCreateUserAccount:

    def setup_method(self):
        """Setup test fixtures before each test."""
        self.valid_username = "testuser"
        self.valid_email = "test@example.com"
        self.valid_password = "password123"

    def test_successful_user_creation(self):
        """Test successful user creation with all valid inputs."""
        with patch('database.find_user', return_value=None), \
             patch('database.create_user', return_value='user123'), \
             patch('send_welcome_email') as mock_email:

            result, status = create_user_account(
                self.valid_username, self.valid_email, self.valid_password
            )

            assert status == 201
            assert result['user_id'] == 'user123'
            assert result['message'] == 'Account created successfully'
            mock_email.assert_called_once_with(self.valid_email, self.valid_username)

    # Username validation tests
    def test_empty_username_returns_error(self):
        """Test that empty username returns validation error."""
        result, status = create_user_account("", self.valid_email, self.valid_password)

        assert status == 400
        assert result['error'] == 'Username must be at least 3 characters'

    def test_short_username_returns_error(self):
        """Test that username shorter than 3 characters returns error."""
        result, status = create_user_account("ab", self.valid_email, self.valid_password)

        assert status == 400
        assert result['error'] == 'Username must be at least 3 characters'

    def test_minimum_valid_username_length(self):
        """Test that 3-character username is accepted."""
        with patch('database.find_user', return_value=None), \
             patch('database.create_user', return_value='user123'), \
             patch('send_welcome_email'):

            result, status = create_user_account("abc", self.valid_email, self.valid_password)
            assert status == 201

    # Email validation tests
    def test_empty_email_returns_error(self):
        """Test that empty email returns validation error."""
        result, status = create_user_account(self.valid_username, "", self.valid_password)

        assert status == 400
        assert result['error'] == 'Invalid email address'

    def test_email_without_at_symbol_returns_error(self):
        """Test that email without @ symbol returns validation error."""
        result, status = create_user_account(
            self.valid_username, "invalid-email", self.valid_password
        )

        assert status == 400
        assert result['error'] == 'Invalid email address'

    def test_none_email_returns_error(self):
        """Test that None email returns validation error."""
        result, status = create_user_account(self.valid_username, None, self.valid_password)

        assert status == 400
        assert result['error'] == 'Invalid email address'

    # Password validation tests
    def test_empty_password_returns_error(self):
        """Test that empty password returns validation error."""
        result, status = create_user_account(self.valid_username, self.valid_email, "")

        assert status == 400
        assert result['error'] == 'Password must be at least 8 characters'

    def test_short_password_returns_error(self):
        """Test that password shorter than 8 characters returns error."""
        result, status = create_user_account(self.valid_username, self.valid_email, "short")

        assert status == 400
        assert result['error'] == 'Password must be at least 8 characters'

    def test_minimum_valid_password_length(self):
        """Test that 8-character password is accepted."""
        with patch('database.find_user', return_value=None), \
             patch('database.create_user', return_value='user123'), \
             patch('send_welcome_email'):

            result, status = create_user_account(
                self.valid_username, self.valid_email, "12345678"
            )
            assert status == 201

    # Duplicate email tests
    def test_existing_email_returns_conflict_error(self):
        """Test that existing email returns 409 conflict error."""
        existing_user = {'id': 'existing123', 'email': self.valid_email}

        with patch('database.find_user', return_value=existing_user):
            result, status = create_user_account(
                self.valid_username, self.valid_email, self.valid_password
            )

            assert status == 409
            assert result['error'] == 'Email already registered'

    # Database error handling tests
    def test_database_connection_error_returns_503(self):
        """Test that database connection error returns 503 service unavailable."""
        with patch('database.find_user', side_effect=DatabaseConnectionError()):
            result, status = create_user_account(
                self.valid_username, self.valid_email, self.valid_password
            )

            assert status == 503
            assert result['error'] == 'Service temporarily unavailable'

    def test_database_error_during_creation_returns_503(self):
        """Test database error during user creation returns 503."""
        with patch('database.find_user', return_value=None), \
             patch('database.create_user', side_effect=DatabaseConnectionError()):

            result, status = create_user_account(
                self.valid_username, self.valid_email, self.valid_password
            )

            assert status == 503
            assert result['error'] == 'Service temporarily unavailable'

    # Email service error handling tests
    def test_email_service_error_still_creates_user(self):
        """Test that email service error doesn't prevent user creation."""
        with patch('database.find_user', return_value=None), \
             patch('database.create_user', return_value='user123'), \
             patch('send_welcome_email', side_effect=EmailServiceError()):

            result, status = create_user_account(
                self.valid_username, self.valid_email, self.valid_password
            )

            assert status == 201
            assert result['user_id'] == 'user123'
            assert result['message'] == 'Account created successfully'

    # General exception handling tests
    def test_unexpected_exception_returns_500(self):
        """Test that unexpected exceptions return 500 internal server error."""
        with patch('database.find_user', side_effect=ValueError("Unexpected error")):
            result, status = create_user_account(
                self.valid_username, self.valid_email, self.valid_password
            )

            assert status == 500
            assert result['error'] == 'Internal server error'

    # Edge case tests
    def test_unicode_username_handling(self):
        """Test that unicode characters in username are handled correctly."""
        unicode_username = "tëst_üser"

        with patch('database.find_user', return_value=None), \
             patch('database.create_user', return_value='user123'), \
             patch('send_welcome_email'):

            result, status = create_user_account(
                unicode_username, self.valid_email, self.valid_password
            )
            assert status == 201

    def test_very_long_inputs_handling(self):
        """Test handling of very long input strings."""
        long_username = "a" * 1000
        long_password = "b" * 1000

        with patch('database.find_user', return_value=None), \
             patch('database.create_user', return_value='user123'), \
             patch('send_welcome_email'):

            result, status = create_user_account(
                long_username, self.valid_email, long_password
            )
            assert status == 201
```

***

### See also

* [improve-build-times.md](improve-build-times.md "mention")
* [bug-fixes.md](bug-fixes.md "mention")
