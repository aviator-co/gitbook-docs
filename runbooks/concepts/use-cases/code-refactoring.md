# Code refactoring

Code refactoring with Runbooks involves automatically analyzing your codebase, identifying improvement opportunities, and systematically updating code to modern patterns and best practices. Unlike manual refactoring that happens piecemeal, Runbooks can refactor entire codebases consistently and safely.

### When to use

#### Ideal scenarios

* **Legacy Code Modernization**: Converting old patterns to modern best practices
* **Framework Upgrades**: Refactoring code to use new framework features
* **Performance Optimization**: Replacing inefficient patterns with optimized ones
* **Consistency Improvements**: Standardizing code patterns across the codebase
* **Architecture Evolution**: Moving from one architectural pattern to another

#### Perfect for teams that

* Have large codebases with inconsistent patterns
* Need to modernize legacy applications
* Want to improve code maintainability and readability
* Need to standardize development practices across teams

### Available refactoring templates

#### React modernization templates

**React class components to hooks**

* Converts class-based components to functional components with hooks
* Migrates lifecycle methods to useEffect hooks
* Transforms state management to useState hooks
* Updates prop types and component patterns

**Legacy Layout to Responsive Design**

* Converts fixed layouts to responsive, mobile-first designs
* Updates CSS from table-based to flexbox/grid layouts
* Implements modern responsive breakpoints
* Adds accessibility improvements

#### JavaScript modernization templates

**jQuery to modern JavaScript**

* Replaces jQuery selectors with modern DOM methods
* Converts jQuery AJAX to fetch API
* Updates event handling to modern patterns
* Removes jQuery dependency and improves performance

**Legacy JavaScript to ES6+**

* Converts var to let/const
* Updates function declarations to arrow functions
* Implements destructuring and template literals
* Adds modern module imports/exports

### Without using templates

#### 1. Codebase analysis

Start by describing what you want to refactor:

```
"Refactor our React application to use modern patterns:
- Convert all class components to functional components with hooks
- Replace lifecycle methods with appropriate hooks
- Update state management from this.setState to useState
- Maintain all existing functionality and prop interfaces"
```

**What runbooks does:**

* Scans your entire codebase for refactoring opportunities
* Identifies all class components and their complexity
* Analyzes lifecycle method usage and state patterns
* Creates a comprehensive refactoring plan

#### 2. Safety analysis

Before refactoring, Runbooks performs safety checks:

* **Dependency Analysis**: Identifies which components depend on others
* **Test Coverage Review**: Ensures adequate testing for safe refactoring
* **Breaking Change Detection**: Identifies potential breaking changes
* **Risk Assessment**: Evaluates complexity and potential issues

#### 3. Incremental refactoring plan

Runbooks creates a step-by-step plan:

1. **Start with Leaf Components**: Begin with components that have no dependencies
2. **Work Up the Tree**: Gradually refactor parent components
3. **Preserve Interfaces**: Maintain existing prop and callback interfaces
4. **Validate Each Step**: Run tests after each component refactoring

#### 4. Automated implementation

The AI executes refactoring systematically:

* Updates one component at a time
* Maintains git history for easy rollback
* Runs tests after each change
* Creates detailed pull requests with explanations

### Real-world refactoring examples

#### Example 1: Component modernization

**Before refactoring:**

```javascript
class UserProfile extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      loading: true,
      user: null,
      error: null
    };
  }

  componentDidMount() {
    this.fetchUser();
  }

  componentDidUpdate(prevProps) {
    if (prevProps.userId !== this.props.userId) {
      this.fetchUser();
    }
  }

  fetchUser = async () => {
    try {
      this.setState({ loading: true });
      const user = await api.getUser(this.props.userId);
      this.setState({ user, loading: false });
    } catch (error) {
      this.setState({ error, loading: false });
    }
  }

  render() {
    const { loading, user, error } = this.state;
    // render logic...
  }
}
```

**After runbooks refactoring:**

```javascript
const UserProfile = ({ userId }) => {
  const [loading, setLoading] = useState(true);
  const [user, setUser] = useState(null);
  const [error, setError] = useState(null);

  const fetchUser = useCallback(async () => {
    try {
      setLoading(true);
      const userData = await api.getUser(userId);
      setUser(userData);
    } catch (err) {
      setError(err);
    } finally {
      setLoading(false);
    }
  }, [userId]);

  useEffect(() => {
    fetchUser();
  }, [fetchUser]);

  // render logic...
};
```

#### Example 2: State management refactoring

**Legacy Redux Pattern:**

```
"Refactor our Redux implementation to use Redux Toolkit:
- Convert action creators to createSlice
- Update reducers to use Immer
- Implement RTK Query for API calls
- Maintain existing component interfaces"
```

**Runbook Implementation:**

1. **Slice Creation**: Convert traditional Redux files to RTK slices
2. **Store Migration**: Update store configuration to use RTK
3. **Component Updates**: Update components to use new hooks
4. **API Integration**: Replace manual API calls with RTK Query
5. **Type Safety**: Add TypeScript types for better developer experience

#### Example 3: CSS architecture refactoring

**From global CSS to CSS modules:**

```
"Refactor our CSS architecture:
- Convert global styles to CSS modules
- Implement consistent naming conventions
- Remove unused CSS rules
- Add CSS custom properties for theming"
```

**Implementation steps:**

1. **Analysis**: Identify all CSS files and their usage patterns
2. **Modularization**: Convert global styles to component-specific modules
3. **Cleanup**: Remove unused styles and consolidate duplicates
4. **Theming**: Extract colors and spacing to CSS custom properties
5. **Documentation**: Update style guides and component documentation

***

### See also

* [improving-readability.md](improving-readability.md "mention")
* [code-migrations.md](code-migrations.md "mention")
