# Code migrations

Code migrations can be of a few flavors - upgrading a library (e.g. SQLAlchemy 1.4 to 2.0 in python), upgrading a language framework (Java 8 to Java 17) or migrating to a completely new framework (Ember to React).

Typically large migrations can require multiple teams collaboration and may be spread over a huge amount of codebase. Using Runbooks these migrations can be planned out with all teams involved and executed step by step while ensuring compatibility.

Remote Agents framework also comes pre-bundled with a library of Runbook templates that can be used as a starting point while adding more context of the nuances associated with your own codebase.

**Example: Java 8 to Java 17 Migration**

<details>

<summary>Java 8 to Java 17 migration template</summary>

````
# Java 8 to Java 17 Migration Runbook

## Metadata
- **Runbook ID**: JAVA-MIG-8-17-001
- **Version**: 1.0
- **Created**: 2024-01-15
- **Author**: Platform Team
- **Estimated Duration**: 40-80 hours (varies by codebase size)
- **Risk Level**: Medium-High
- **Rollback Strategy**: Git revert to tagged release

## Objective
Migrate Java 8 codebase to Java 17, leveraging new language features, improving performance, and ensuring compatibility with modern Java ecosystem while maintaining backward compatibility where required.

## Prerequisites
- Comprehensive test suite with >80% code coverage
- CI/CD pipeline with automated testing
- Current Java 8 codebase building successfully
- Backup of current production deployment

---

## 1. Pre-Migration Analysis

### 1.1 Dependency Audit
Analyze all project dependencies for Java 17 compatibility.

#### 1.1.1 Maven Dependencies Analysis
```xml
<!-- Check current dependency versions -->
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>versions-maven-plugin</artifactId>
    <version>2.15.0</version>
</plugin>
```

#### 1.1.2 Gradle Dependencies Analysis
```gradle
// Apply versions plugin
plugins {
    id 'com.github.ben-manes.versions' version '0.47.0'
}
```

#### 1.1.3 Identify Incompatible Libraries
- Check for libraries using removed APIs
- Verify JAXB, JAX-WS dependencies
- Validate reflection-heavy frameworks
- Document required library upgrades

### 1.2 Code Analysis

#### 1.2.1 Deprecated API Usage
```bash
# Scan for deprecated APIs
jdeprscan --release 17 --list --for-removal target/classes
```

#### 1.2.2 Removed API Detection
- Scan for `sun.*` package usage
- Identify `com.sun.*` internal APIs
- Check for JavaEE modules usage
- Document all occurrences

#### 1.2.3 Reflection and Unsafe Operations
- Identify `setAccessible()` usage
- Find `Unsafe` class usage
- Check for illegal reflective access
- Plan module access requirements

### 1.3 Build System Preparation

#### 1.3.1 Maven Configuration
```xml
<properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <maven.compiler.release>17</maven.compiler.release>
</properties>
```

#### 1.3.2 Gradle Configuration
```gradle
java {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}
```

---

## 2. Environment Setup

### 2.1 Development Environment

#### 2.1.1 Install JDK 17
- Download OpenJDK 17 or preferred distribution
- Configure `JAVA_HOME` environment variable
- Update IDE to support Java 17
- Configure IDE compiler settings

#### 2.1.2 Build Tool Updates
- Maven: Minimum version 3.8.1
- Gradle: Minimum version 7.3
- Update wrapper scripts
- Verify plugin compatibility

### 2.2 CI/CD Pipeline Configuration

#### 2.2.1 Update Build Images
```yaml
# Example: Jenkins Pipeline
pipeline {
    agent {
        docker {
            image 'maven:3.8.6-openjdk-17'
        }
    }
}
```

#### 2.2.2 Parallel Build Configuration
- Maintain Java 8 build for comparison
- Set up Java 17 build pipeline
- Configure test result comparison
- Implement gradual rollout strategy

---

## 3. Core Language Migration

### 3.1 Module System Adoption (Optional)

#### 3.1.1 Create module-info.java
```java
module com.company.application {
    requires java.base;
    requires java.sql;
    requires java.logging;
    
    exports com.company.api;
    exports com.company.model;
    
    opens com.company.entity to hibernate.core;
}
```

#### 3.1.2 Handle Split Packages
- Identify split package conflicts
- Refactor package structures
- Update imports across codebase
- Test module boundaries

### 3.2 Removed APIs Replacement

#### 3.2.1 Replace JavaEE Dependencies
```xml
<!-- Remove -->
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
</dependency>

<!-- Add -->
<dependency>
    <groupId>jakarta.xml.bind</groupId>
    <artifactId>jakarta.xml.bind-api</artifactId>
    <version>4.0.0</version>
</dependency>
```

#### 3.2.2 Replace Internal APIs
```java
// Before: Using sun.misc.BASE64Encoder
sun.misc.BASE64Encoder encoder = new sun.misc.BASE64Encoder();
String encoded = encoder.encode(data);

// After: Using java.util.Base64
String encoded = Base64.getEncoder().encodeToString(data);
```

### 3.3 Language Feature Updates

#### 3.3.1 Local Variable Type Inference
```java
// Before
Map<String, List<Customer>> customerMap = new HashMap<>();
for (Map.Entry<String, List<Customer>> entry : customerMap.entrySet()) {
    List<Customer> customers = entry.getValue();
}

// After
var customerMap = new HashMap<String, List<Customer>>();
for (var entry : customerMap.entrySet()) {
    var customers = entry.getValue();
}
```

#### 3.3.2 Switch Expressions
```java
// Before
String result;
switch (day) {
    case MONDAY:
    case FRIDAY:
    case SUNDAY:
        result = "Short day";
        break;
    case TUESDAY:
        result = "Long day";
        break;
    default:
        result = "Regular day";
}

// After
String result = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> "Short day";
    case TUESDAY -> "Long day";
    default -> "Regular day";
};
```

#### 3.3.3 Text Blocks
```java
// Before
String json = "{\n" +
              "  \"name\": \"John\",\n" +
              "  \"age\": 30\n" +
              "}";

// After
String json = """
    {
      "name": "John",
      "age": 30
    }
    """;
```

---

## 4. Library and Framework Updates

### 4.1 Spring Framework Migration

#### 4.1.1 Update Spring Boot Version
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.1.5</version>
</parent>
```

#### 4.1.2 Update Annotations
```java
// Replace @Autowired with constructor injection
@Component
public class UserService {
    private final UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

### 4.2 Testing Framework Updates

#### 4.2.1 JUnit 5 Migration
```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>
</dependency>
```

#### 4.2.2 Update Test Annotations
```java
// Before: JUnit 4
@RunWith(SpringRunner.class)
@Test(expected = IllegalArgumentException.class)
public void testMethod() {}

// After: JUnit 5
@ExtendWith(SpringExtension.class)
@Test
void testMethod() {
    assertThrows(IllegalArgumentException.class, () -> {
        // test code
    });
}
```

### 4.3 Logging Framework Updates

#### 4.3.1 SLF4J and Logback Updates
```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.4.11</version>
</dependency>
```

#### 4.3.2 Log4j2 Migration (if applicable)
```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.21.1</version>
</dependency>
```

---

## 5. Performance Optimizations

### 5.1 JVM Flags Update

#### 5.1.1 Remove Deprecated Flags
```bash
# Remove these flags
-XX:+UseConcMarkSweepGC
-XX:+CMSIncrementalMode
-XX:MaxPermSize=256m

# Add modern alternatives
-XX:+UseG1GC
-XX:MaxRAMPercentage=75.0
```

#### 5.1.2 Add Performance Flags
```bash
-XX:+UseStringDeduplication
-XX:+UseShenandoahGC  # Alternative GC
-XX:+EnableDynamicAgentLoading
```

### 5.2 Collection Factory Methods

#### 5.2.1 Immutable Collections
```java
// Before
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list = Collections.unmodifiableList(list);

// After
List<String> list = List.of("a", "b");
```

#### 5.2.2 Map Factory Methods
```java
// Before
Map<String, Integer> map = new HashMap<>();
map.put("a", 1);
map.put("b", 2);

// After
Map<String, Integer> map = Map.of(
    "a", 1,
    "b", 2
);
```

---

## 6. Security Updates

### 6.1 TLS Configuration

#### 6.1.1 Update Security Properties
```properties
# Disable older TLS versions
jdk.tls.disabledAlgorithms=SSLv3, TLSv1, TLSv1.1
```

#### 6.1.2 Strong Encryption Defaults
```java
// Configure HTTPS connections
System.setProperty("https.protocols", "TLSv1.3,TLSv1.2");
System.setProperty("jdk.tls.client.protocols", "TLSv1.3,TLSv1.2");
```

### 6.2 Security Manager Deprecation

#### 6.2.1 Remove Security Manager Usage
```java
// Identify and remove
System.setSecurityManager(new SecurityManager());

// Replace with alternative security measures
// - Use container security
// - Implement application-level security
```

---

## 7. Testing and Validation

### 7.1 Comprehensive Testing

#### 7.1.1 Unit Test Execution
```bash
# Run all unit tests
mvn clean test -Dtest=**/*Test

# With coverage
mvn clean test jacoco:report
```

#### 7.1.2 Integration Testing
```bash
# Run integration tests
mvn clean verify -Dskip.unit.tests=true
```

#### 7.1.3 Performance Testing
- Benchmark critical paths
- Compare with Java 8 baseline
- Memory usage analysis
- GC performance comparison

### 7.2 Compatibility Testing

#### 7.2.1 API Compatibility
```bash
# Use japicmp for API comparison
mvn japicmp:cmp -DoldVersion=1.0.0-java8
```

#### 7.2.2 Binary Compatibility
- Test with existing clients
- Verify serialization compatibility
- Check reflection-based frameworks

---

## 8. Documentation Updates

### 8.1 Code Documentation

#### 8.1.1 Update JavaDoc
```java
/**
 * Process user data using modern Java 17 features.
 * @since Java 17
 * @implNote Uses records and pattern matching
 */
public void processUserData(Object data) {
    switch (data) {
        case User(var name, var age) -> processUser(name, age);
        case null -> handleNull();
        default -> handleUnknown(data);
    }
}
```

#### 8.1.2 README Updates
- Document Java 17 requirement
- Update build instructions
- List new features utilized
- Migration guide for developers

### 8.2 Deployment Documentation

#### 8.2.1 Update Deployment Scripts
```bash
#!/bin/bash
# Check Java version
java_version=$(java -version 2>&1 | awk -F '"' '/version/ {print $2}')
if [[ ! "$java_version" =~ ^17\. ]]; then
    echo "Error: Java 17 required"

````

</details>

