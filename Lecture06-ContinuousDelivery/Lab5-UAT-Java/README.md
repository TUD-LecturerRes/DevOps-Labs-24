# Lab 5: BDD Testing with Cucumber-JVM

## Introduction

### What is BDD?
Behavior-Driven Development (BDD) is a collaborative approach that helps teams create better software by:
- Using natural language to describe application behavior
- Bridging communication between business and technical teams
- Creating tests that serve as living documentation
- Focusing on business value and user requirements

### Tools We'll Use
1. **Cucumber**
   - BDD framework that runs automated acceptance tests
   - Uses plain English descriptions (Gherkin syntax)
   - Converts English into executable tests
   - Popular in both Java and other programming languages

2. **Gherkin**
   - Language used to write test scenarios
   - Uses keywords like `Feature`, `Scenario`, `Given`, `When`, `Then`
   - Example:
   ```gherkin
   Feature: Calculator Addition
   
   Scenario: Add two numbers
     Given I have entered 5 into the calculator
     And I have entered 7 into the calculator
     When I press add
     Then the result should be 12
   ```

3. **JUnit**
   - Testing framework for Java
   - Works with Cucumber to run tests
   - Provides assertions and test infrastructure

### How This Fits Into UAT
1. **Traditional UAT**
   - Manual testing by users
   - Time-consuming
   - Hard to repeat consistently
   - Limited by human availability

2. **BDD-Based UAT**
   - Automated acceptance testing
   - Tests written in business language
   - Easy to maintain and repeat
   - Can be part of CI/CD pipeline

## Prerequisites
✅ Required before starting:
* Java JDK 17 installed
* Basic understanding of Java and testing concepts
* IDE (VS Code or similar)
* Terminal/Command Prompt access

## Part 1: Project Setup with Gradle

### Understanding Project Structure
We'll use Gradle to create a standardized project structure. Gradle is a build automation tool that:
- Manages project dependencies (libraries we need)
- Compiles our code
- Runs our tests
- Generates reports

### Step 1: Create and Initialize Project
```bash
# Create project directory
mkdir calculator-uat
cd calculator-uat

# Initialize Gradle project
gradle init
```

Select these options when prompted (explanation of each choice included):
```bash
1. Type of project: application
   (Creates a complete application structure)

2. Implementation language: Java
   (Sets up Java compilation and testing)

3. Target Java version: 17
   (Latest LTS version with good feature support)

4. Project name: [press Enter for default]
   (Name of your project, calculator-uat is fine)

5. Build script DSL: Groovy
   (More common and readable than Kotlin DSL)

6. Test framework: JUnit Jupiter
   (Modern version of JUnit, works well with Cucumber)

7. No need for new APIs and behavior
   (Keeps the setup simple)
```

### Step 2: Understanding Generated Structure
Gradle creates this structure:
```
calculator-uat/
└── app/
    └── src/
        ├── main/           # Your application code goes here
        │   └── java/
        └── test/           # Your test code goes here
            ├── java/       # Test implementations
            └── resources/  # Test resources (like feature files)
```

## Part 2: Configuration and Dependencies

### Update build.gradle
This file tells Gradle what libraries we need and how to run our tests:

```gradle
plugins {
    id 'application'
    id 'java'
}

repositories {
    mavenCentral()  // Where to download dependencies from
}

dependencies {
    // Testing frameworks
    testImplementation 'org.junit.jupiter:junit-jupiter:5.10.1'
    
    // Cucumber - BDD testing framework
    testImplementation 'io.cucumber:cucumber-java:7.15.0'
    testImplementation 'io.cucumber:cucumber-junit:7.15.0'
    testImplementation 'io.cucumber:cucumber-junit-platform-engine:7.15.0'
    testImplementation 'org.junit.platform:junit-platform-suite:1.10.1'
}

application {
    mainClass = 'org.example.App'  // Our calculator application
}

tasks.named('test') {
    useJUnitPlatform()
    systemProperty "cucumber.junit-platform.naming-strategy", "long"
    testLogging {
        events "passed", "skipped", "failed"
        showStandardStreams = true
    }
}
```

## Part 3: Implementing the Calculator

### Understanding the Implementation
We'll create a simple calculator that will:
1. Perform basic arithmetic operations
2. Handle errors (like division by zero)
3. Provide a command-line interface for manual testing

### Update App.java
Location: `app/src/main/java/org/example/App.java`

```java
package org.example;

public class App {
    // Basic arithmetic operations
    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public int divide(int a, int b) {
        // Check for division by zero
        if (b == 0) {
            throw new ArithmeticException("Division by zero");
        }
        return a / b;
    }

    // Interactive menu for manual testing
    public static void main(String[] args) {
        App calc = new App();
        java.util.Scanner scanner = new java.util.Scanner(System.in);

        while (true) {
            System.out.println("\nSimple Calculator");
            System.out.println("1. Add");
            System.out.println("2. Subtract");
            System.out.println("3. Multiply");
            System.out.println("4. Divide");
            System.out.println("5. Exit");
            
            System.out.print("Choose operation: ");
            int choice = scanner.nextInt();
            
            if (choice == 5) break;
            
            System.out.print("Enter first number: ");
            int a = scanner.nextInt();
            System.out.print("Enter second number: ");
            int b = scanner.nextInt();
            
            try {
                switch (choice) {
                    case 1:
                        System.out.println("Result: " + calc.add(a, b));
                        break;
                    case 2:
                        System.out.println("Result: " + calc.subtract(a, b));
                        break;
                    case 3:
                        System.out.println("Result: " + calc.multiply(a, b));
                        break;
                    case 4:
                        System.out.println("Result: " + calc.divide(a, b));
                        break;
                    default:
                        System.out.println("Invalid choice");
                }
            } catch (ArithmeticException e) {
                System.out.println("Error: " + e.getMessage());
            }
        }
        
        scanner.close();
    }
}
```

## Part 4: Writing BDD Tests

### Understanding Gherkin Syntax
Gherkin is a simple language that:
- Uses natural language patterns
- Follows a specific structure
- Helps define test scenarios

Key Gherkin Keywords:
- `Feature`: Describes what you're testing
- `Scenario`: A specific test case
- `Given`: Setup conditions
- `When`: Action being tested
- `Then`: Expected results
- `And`: Additional steps

### Create Feature File
Create: `app/src/test/resources/features/calculator.feature`

```gherkin
# This is a feature file - it describes the behavior we want to test
Feature: Calculator Operations
  As a user
  I want to perform basic calculations
  So that I can get accurate results

  # Each scenario is a test case
  Scenario: Adding two numbers
    Given I have a calculator
    When I add 5 and 3
    Then the result should be 8

  Scenario: Subtracting numbers
    Given I have a calculator
    When I subtract 8 from 15
    Then the result should be 7

  Scenario: Multiplying numbers
    Given I have a calculator
    When I multiply 4 and 3
    Then the result should be 12

  Scenario: Dividing numbers
    Given I have a calculator
    When I divide 15 by 3
    Then the result should be 5

  # Testing error conditions
  Scenario: Division by zero
    Given I have a calculator
    When I divide 10 by 0
    Then it should throw an arithmetic exception
```

## Part 5: Implementing the Tests

### Understanding Test Components
Our BDD test implementation consists of two main parts:
1. **Test Runner**: Configures and runs the Cucumber tests
2. **Step Definitions**: Connects Gherkin steps to actual Java code

### 1. Create Test Runner
Create file: `app/src/test/java/org/example/RunCucumberTest.java`

```java
package org.example;

import org.junit.platform.suite.api.ConfigurationParameter;
import org.junit.platform.suite.api.IncludeEngines;
import org.junit.platform.suite.api.SelectClasspathResource;
import org.junit.platform.suite.api.Suite;

import static io.cucumber.junit.platform.engine.Constants.PLUGIN_PROPERTY_NAME;
import static io.cucumber.junit.platform.engine.Constants.GLUE_PROPERTY_NAME;

@Suite                                              // Marks this as a test suite
@IncludeEngines("cucumber")                        // Use Cucumber for testing
@SelectClasspathResource("features")               // Where to find feature files
@ConfigurationParameter(key = PLUGIN_PROPERTY_NAME, value = "pretty")  // Nice formatting
@ConfigurationParameter(key = GLUE_PROPERTY_NAME, value = "org.example")  // Where to find step definitions
public class RunCucumberTest {
    // Empty class - configuration is in the annotations
}
```

### 2. Create Step Definitions
Create file: `app/src/test/java/org/example/CalculatorSteps.java`

```java
package org.example;

import io.cucumber.java.en.Given;
import io.cucumber.java.en.Then;
import io.cucumber.java.en.When;
import static org.junit.jupiter.api.Assertions.*;

public class CalculatorSteps {
    // Test context - holds objects we need during the test
    private App calculator;
    private int result;
    private Exception thrownException;

    // Step definitions - map to steps in feature file
    @Given("I have a calculator")
    public void i_have_a_calculator() {
        calculator = new App();  // Create new calculator for each scenario
    }

    @When("I add {int} and {int}")
    public void i_add_and(Integer a, Integer b) {
        result = calculator.add(a, b);  // Perform addition
    }

    @When("I subtract {int} from {int}")
    public void i_subtract_from(Integer b, Integer a) {
        result = calculator.subtract(a, b);  // Perform subtraction
    }

    @When("I multiply {int} and {int}")
    public void i_multiply_and(Integer a, Integer b) {
        result = calculator.multiply(a, b);  // Perform multiplication
    }

    @When("I divide {int} by {int}")
    public void i_divide_by(Integer a, Integer b) {
        try {
            result = calculator.divide(a, b);  // Try to perform division
        } catch (ArithmeticException e) {
            thrownException = e;  // Capture any arithmetic exceptions
        }
    }

    @Then("the result should be {int}")
    public void the_result_should_be(Integer expected) {
        assertEquals(expected, result);  // Verify the result
    }

    @Then("it should throw an arithmetic exception")
    public void it_should_throw_an_arithmetic_exception() {
        assertNotNull(thrownException);  // Verify exception was thrown
        assertTrue(thrownException instanceof ArithmeticException);  // Verify correct exception type
    }
}
```

### Understanding Step Definitions

1. **Annotations**
   - `@Given`: Sets up test prerequisites
   - `@When`: Performs the action being tested
   - `@Then`: Verifies the results
   - Parameters in curly braces `{int}` match numbers in the feature file

2. **Test Context**
   - Class variables maintain state between steps
   - New instance created for each scenario
   - Helps isolate tests from each other

3. **Assertions**
   - `assertEquals`: Verifies expected values
   - `assertNotNull`: Checks for non-null values
   - `assertTrue`: Validates conditions

## Part 6: Running the Tests

### 1. Command Line
```bash
./gradlew test
```

### 2. Expected Output
```
Feature: Calculator Operations
  ✓ Scenario: Adding two numbers
  ✓ Scenario: Subtracting numbers
  ✓ Scenario: Multiplying numbers
  ✓ Scenario: Dividing numbers
  ✓ Scenario: Division by zero

5 Scenarios (5 passed)
15 steps (15 passed)
```

### 3. Test Reports
Find detailed reports in:
```
app/build/reports/tests/test/index.html
```

## Common Issues and Solutions

### 1. Tests Not Found
- Check feature file location
- Verify package names match
- Ensure test runner annotations are correct

### 2. Steps Not Matching
- Compare step definitions with feature file exactly
- Check for typos
- Verify parameter types match

### 3. Test Failures
- Review assertion failures in test report
- Check calculator implementation
- Verify test data in feature file

