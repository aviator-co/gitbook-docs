# Improving readability

Code readability improvement with Runbooks involves using AI to analyze your codebase for readability issues and systematically applying improvements that make code easier to understand, maintain, and extend. Unlike manual code cleanup that happens inconsistently, Runbooks can apply readability improvements across your entire codebase following consistent standards and best practices.

### When to use

#### Ideal scenarios

* Legacy code cleanup
* Code standardization
* Onboarding optimization
* Documentation debt
* Refactoring for clarity

#### Perfect for teams that

* Struggle with code maintenance due to poor readability
* Have inconsistent coding standards across the codebase
* Need to onboard new developers quickly
* Want to reduce time spent understanding existing code
* Have accumulated technical debt in code quality

### Available readability improvement templates

#### Code structure and organization templates

**Function and method decomposition**

* Breaks large, complex functions into smaller, focused units
* Implements single responsibility principle
* Adds clear function naming and documentation
* Improves parameter organization and defaults

**Class and module organization**

* Restructures classes for better separation of concerns
* Organizes methods and properties logically
* Implements clear interface definitions
* Adds comprehensive class documentation

**File and directory structure**

* Reorganizes files into logical groupings
* Implements consistent naming conventions
* Creates clear module boundaries
* Adds index files for better imports

#### Naming and documentation templates

**Variable and function naming**

* Converts cryptic variable names to descriptive ones
* Implements consistent naming conventions
* Removes abbreviated and unclear names
* Adds type hints and documentation

**API documentation generation**

* Generates comprehensive API documentation
* Adds docstrings and comments to all public methods
* Creates usage examples and parameter descriptions
* Implements documentation standards (JSDoc, Sphinx, etc.)

**Code comments and explanations**

* Adds explanatory comments for complex logic
* Documents business rules and requirements
* Explains algorithmic choices and trade-offs
* Removes outdated or incorrect comments

#### Formatting and style templates

**Code formatting standardization**

* Applies consistent indentation and spacing
* Implements standard bracket and parentheses styles
* Organizes imports and dependencies consistently
* Removes trailing whitespace and empty lines

**Modern language feature adoption**

* Updates to modern syntax for better readability
* Implements destructuring and template literals
* Uses modern iteration and functional methods
* Adopts cleaner async/await patterns

### Without using templates

#### 1. Readability assessment

Start by describing your readability challenges:

```
"Our codebase has grown organically over 5 years and has become difficult to maintain. Issues include:
- Functions that are 200+ lines long with unclear purposes
- Variable names like 'data', 'item', 'temp' throughout the code
- Missing documentation for complex business logic
- Inconsistent formatting and coding styles across files
- Deep nesting that makes control flow hard to follow"
```

**What Runbooks does:**

* Analyzes your codebase for complexity metrics and readability issues
* Identifies functions and classes that need decomposition
* Maps inconsistent naming patterns and style violations
* Creates a prioritized improvement plan focusing on high-impact areas

#### 2. Complexity analysis

Runbooks performs comprehensive analysis:

* Cyclomatic complexity
* Nesting depth
* Function length
* Naming consistency
* Documentation coverage

#### 3. Systematic improvement strategy

The AI creates a structured improvement plan:

1. Critical path cleanup
2. Naming standardization
3. Function decomposition
4. Documentation addition
5. Style standardization

#### 4. Implementation and validation

Runbooks implements improvements systematically:

* Makes one type of improvement at a time for easy review
* Preserves all functionality while improving readability
* Runs tests to ensure behavior remains unchanged
* Creates detailed documentation of improvements made

### Real-world readability improvement examples

#### Example 1: Function decomposition and naming

**Before improvement:**

```javascript
// Cryptic function with unclear purpose and poor structure
function processData(data) {
  let result = [];
  for (let i = 0; i < data.length; i++) {
    if (data[i].status === 'active' && data[i].balance > 0) {
      let item = {
        id: data[i].id,
        name: data[i].firstName + ' ' + data[i].lastName,
        total: data[i].balance * 1.08,
        category: data[i].balance > 1000 ? 'premium' : 'standard'
      };
      if (data[i].lastLoginDate) {
        let daysSince = (Date.now() - new Date(data[i].lastLoginDate)) / (1000 * 60 * 60 * 24);
        if (daysSince < 30) {
          item.engagement = 'high';
        } else if (daysSince < 90) {
          item.engagement = 'medium';
        } else {
          item.engagement = 'low';
        }
      }
      result.push(item);
    }
  }
  return result;
}
```

**After Runbooks improvement:**

```javascript
/**
 * Transforms raw user data into enriched customer profiles for reporting
 * @param {Array<User>} users - Raw user data from the database
 * @returns {Array<CustomerProfile>} Enriched customer profiles with calculated fields
 */
function createCustomerProfiles(users) {
  const activeUsersWithBalance = filterActiveUsers(users);
  return activeUsersWithBalance.map(user => createCustomerProfile(user));
}

/**
 * Filters users to only include active users with positive balance
 * @param {Array<User>} users - All users from the database
 * @returns {Array<User>} Filtered list of active users with balance
 */
function filterActiveUsers(users) {
  return users.filter(user =>
    user.status === 'active' && user.balance > 0
  );
}

/**
 * Creates an enriched customer profile from a user record
 * @param {User} user - Individual user record
 * @returns {CustomerProfile} Enriched profile with calculated fields
 */
function createCustomerProfile(user) {
  return {
    id: user.id,
    name: formatFullName(user),
    total: calculateTotalWithTax(user.balance),
    category: determineCustomerCategory(user.balance),
    engagement: calculateEngagementLevel(user.lastLoginDate)
  };
}

/**
 * Formats user's first and last name into a full name
 * @param {User} user - User with firstName and lastName
 * @returns {string} Formatted full name
 */
function formatFullName(user) {
  return `${user.firstName} ${user.lastName}`;
}

/**
 * Calculates total amount including 8% tax
 * @param {number} balance - User's current balance
 * @returns {number} Balance with tax included
 */
function calculateTotalWithTax(balance) {
  const TAX_RATE = 0.08;
  return balance * (1 + TAX_RATE);
}

/**
 * Determines customer category based on balance
 * @param {number} balance - User's current balance
 * @returns {string} 'premium' for high-value customers, 'standard' otherwise
 */
function determineCustomerCategory(balance) {
  const PREMIUM_THRESHOLD = 1000;
  return balance > PREMIUM_THRESHOLD ? 'premium' : 'standard';
}

/**
 * Calculates user engagement level based on last login date
 * @param {string|null} lastLoginDate - ISO date string of last login
 * @returns {string} Engagement level: 'high', 'medium', 'low', or 'unknown'
 */
function calculateEngagementLevel(lastLoginDate) {
  if (!lastLoginDate) {
    return 'unknown';
  }

  const daysSinceLogin = calculateDaysSince(lastLoginDate);

  if (daysSinceLogin < 30) return 'high';
  if (daysSinceLogin < 90) return 'medium';
  return 'low';
}

/**
 * Calculates the number of days between a date and now
 * @param {string} dateString - ISO date string
 * @returns {number} Number of days since the given date
 */
function calculateDaysSince(dateString) {
  const date = new Date(dateString);
  const now = Date.now();
  const millisecondsPerDay = 1000 * 60 * 60 * 24;

  return (now - date.getTime()) / millisecondsPerDay;
}
```

#### Example 2: Class structure and documentation

**Before improvement:**

```python
# Poorly organized class with unclear responsibilities
class DataManager:
    def __init__(self, db_conn):
        self.db = db_conn
        self.cache = {}

    def get_data(self, id, type):
        if type == 'user':
            if id in self.cache:
                return self.cache[id]
            result = self.db.execute("SELECT * FROM users WHERE id = ?", (id,))
            self.cache[id] = result
            return result
        elif type == 'order':
            result = self.db.execute("SELECT * FROM orders WHERE id = ?", (id,))
            if result:
                user_data = self.get_data(result['user_id'], 'user')
                result['user_name'] = user_data['name']
            return result

    def save_data(self, data, type):
        if type == 'user':
            self.db.execute("INSERT INTO users VALUES (?, ?, ?)",
                          (data['id'], data['name'], data['email']))
            self.cache[data['id']] = data
        elif type == 'order':
            self.db.execute("INSERT INTO orders VALUES (?, ?, ?)",
                          (data['id'], data['user_id'], data['amount']))
```

**After Runbooks improvement:**

```python
from typing import Dict, Optional, Any
from abc import ABC, abstractmethod

class DatabaseRepository(ABC):
    """Abstract base class for database repositories with caching support."""

    def __init__(self, database_connection):
        """Initialize repository with database connection."""
        self._database = database_connection
        self._cache: Dict[Any, Dict] = {}

    @abstractmethod
    def _get_table_name(self) -> str:
        """Return the database table name for this repository."""
        pass

    @abstractmethod
    def _create_insert_query(self, data: Dict) -> tuple:
        """Create INSERT query and parameters for the given data."""
        pass

class UserRepository(DatabaseRepository):
    """Repository for managing user data with caching support."""

    def _get_table_name(self) -> str:
        return "users"

    def _create_insert_query(self, user_data: Dict) -> tuple:
        """Create INSERT query for user data."""
        query = "INSERT INTO users (id, name, email) VALUES (?, ?, ?)"
        params = (user_data['id'], user_data['name'], user_data['email'])
        return query, params

    def get_user_by_id(self, user_id: int) -> Optional[Dict]:
        """
        Retrieve user by ID with caching support.

        Args:
            user_id: The unique identifier for the user

        Returns:
            User data dictionary or None if not found
        """
        if user_id in self._cache:
            return self._cache[user_id]

        query = "SELECT * FROM users WHERE id = ?"
        user_data = self._database.execute(query, (user_id,))

        if user_data:
            self._cache[user_id] = user_data

        return user_data

    def save_user(self, user_data: Dict) -> None:
        """
        Save user data to database and update cache.

        Args:
            user_data: Dictionary containing user information
                      Must include 'id', 'name', and 'email' keys
        """
        query, params = self._create_insert_query(user_data)
        self._database.execute(query, params)
        self._cache[user_data['id']] = user_data

class OrderRepository(DatabaseRepository):
    """Repository for managing order data with user enrichment."""

    def __init__(self, database_connection, user_repository: UserRepository):
        """Initialize order repository with user repository dependency."""
        super().__init__(database_connection)
        self._user_repository = user_repository

    def _get_table_name(self) -> str:
        return "orders"

    def _create_insert_query(self, order_data: Dict) -> tuple:
        """Create INSERT query for order data."""
        query = "INSERT INTO orders (id, user_id, amount) VALUES (?, ?, ?)"
        params = (order_data['id'], order_data['user_id'], order_data['amount'])
        return query, params

    def get_order_by_id(self, order_id: int) -> Optional[Dict]:
        """
        Retrieve order by ID with user information enrichment.

        Args:
            order_id: The unique identifier for the order

        Returns:
            Order data dictionary with user_name field added, or None if not found
        """
        query = "SELECT * FROM orders WHERE id = ?"
        order_data = self._database.execute(query, (order_id,))

        if not order_data:
            return None

        return self._enrich_order_with_user_data(order_data)

    def _enrich_order_with_user_data(self, order_data: Dict) -> Dict:
        """Add user information to order data."""
        user_data = self._user_repository.get_user_by_id(order_data['user_id'])

        if user_data:
            order_data['user_name'] = user_data['name']

        return order_data

    def save_order(self, order_data: Dict) -> None:
        """
        Save order data to database.

        Args:
            order_data: Dictionary containing order information
                       Must include 'id', 'user_id', and 'amount' keys
        """
        query, params = self._create_insert_query(order_data)
        self._database.execute(query, params)
```

#### Example 3: Complex logic simplification

**Before improvement:**

```javascript
// Complex nested logic that's hard to follow
function calculatePrice(item, user, discount, shipping) {
  let price = item.basePrice;
  if (user.type === 'premium') {
    if (user.yearsActive > 5) {
      price = price * 0.85;
    } else if (user.yearsActive > 2) {
      price = price * 0.9;
    } else {
      price = price * 0.95;
    }
  } else if (user.type === 'regular') {
    if (user.yearsActive > 3) {
      price = price * 0.95;
    }
  }

  if (discount) {
    if (discount.type === 'percentage') {
      price = price * (1 - discount.value / 100);
    } else if (discount.type === 'fixed') {
      price = Math.max(0, price - discount.value);
    }
  }

  if (shipping.expedited && price > 100) {
    price += shipping.cost * 0.5;
  } else if (shipping.expedited) {
    price += shipping.cost;
  } else if (price < 50) {
    price += shipping.cost;
  }

  return Math.round(price * 100) / 100;
}
```

**After Runbooks improvement:**

```javascript
/**
 * Calculates the final price for an item including all discounts and shipping
 * @param {Item} item - Product item with base pricing
 * @param {User} user - Customer information for loyalty discounts
 * @param {Discount|null} discount - Optional discount to apply
 * @param {Shipping} shipping - Shipping options and costs
 * @returns {number} Final price rounded to 2 decimal places
 */
function calculateFinalPrice(item, user, discount, shipping) {
  let price = item.basePrice;

  price = applyLoyaltyDiscount(price, user);
  price = applyPromoDiscount(price, discount);
  price = addShippingCost(price, shipping);

  return roundToTwoDecimals(price);
}

/**
 * Applies loyalty discount based on user type and years active
 * @param {number} price - Current price
 * @param {User} user - User with type and yearsActive properties
 * @returns {number} Price after loyalty discount
 */
function applyLoyaltyDiscount(price, user) {
  const discountRate = getLoyaltyDiscountRate(user);
  return price * (1 - discountRate);
}

/**
 * Determines loyalty discount rate based on user type and tenure
 * @param {User} user - User information
 * @returns {number} Discount rate as decimal (0.15 = 15% discount)
 */
function getLoyaltyDiscountRate(user) {
  if (user.type === 'premium') {
    return getPremiumUserDiscountRate(user.yearsActive);
  }

  if (user.type === 'regular' && user.yearsActive > 3) {
    return 0.05; // 5% discount for long-term regular users
  }

  return 0; // No loyalty discount
}

/**
 * Calculates discount rate for premium users based on tenure
 * @param {number} yearsActive - Number of years user has been active
 * @returns {number} Discount rate as decimal
 */
function getPremiumUserDiscountRate(yearsActive) {
  if (yearsActive > 5) return 0.15;  // 15% discount
  if (yearsActive > 2) return 0.10;  // 10% discount
  return 0.05;                       // 5% discount
}

/**
 * Applies promotional discount if available
 * @param {number} price - Current price
 * @param {Discount|null} discount - Discount object or null
 * @returns {number} Price after promotional discount
 */
function applyPromoDiscount(price, discount) {
  if (!discount) {
    return price;
  }

  if (discount.type === 'percentage') {
    return price * (1 - discount.value / 100);
  }

  if (discount.type === 'fixed') {
    return Math.max(0, price - discount.value);
  }

  return price; // Unknown discount type, no change
}

/**
 * Adds shipping cost based on order value and shipping options
 * @param {number} price - Current order total
 * @param {Shipping} shipping - Shipping configuration
 * @returns {number} Price including shipping
 */
function addShippingCost(price, shipping) {
  const qualifiesForFreeShipping = price >= 50 && !shipping.expedited;

  if (qualifiesForFreeShipping) {
    return price;
  }

  const shippingCost = calculateShippingCost(price, shipping);
  return price + shippingCost;
}

/**
 * Calculates shipping cost based on order value and expedited option
 * @param {number} price - Order total
 * @param {Shipping} shipping - Shipping options
 * @returns {number} Shipping cost
 */
function calculateShippingCost(price, shipping) {
  if (!shipping.expedited) {
    return shipping.cost;
  }

  // Expedited shipping discount for orders over $100
  const expeditedDiscount = price > 100 ? 0.5 : 1.0;
  return shipping.cost * expeditedDiscount;
}

/**
 * Rounds a number to two decimal places
 * @param {number} value - Number to round
 * @returns {number} Number rounded to 2 decimal places
 */
function roundToTwoDecimals(value) {
  return Math.round(value * 100) / 100;
}
```

***

### See also

* [product-backlog.md](product-backlog.md "mention")
* [code-refactoring.md](code-refactoring.md "mention")
