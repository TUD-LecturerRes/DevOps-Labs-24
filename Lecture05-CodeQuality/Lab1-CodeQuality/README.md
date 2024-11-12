# Lab 1: Code Quality & Testing Fundamentals

## Introduction
In this lab, we'll explore fundamental concepts of code quality and testing in Java using JUnit 5 and various code quality tools. We'll work with a simple Calculator application to understand basic testing concepts, test coverage, and code quality metrics.

### Learning Objectives
- Set up a basic Java testing environment
- Understand fundamental unit testing concepts
- Learn about code coverage and quality metrics
- Practice writing basic unit tests
- Implement code coverage reporting

### Prerequisites
- VS Code installed with Java support
- Java JDK 11 or later installed
- Git installed and configured
- Basic understanding of Java programming

## Part 1: Environment Setup and Initial Analysis

### 1. Project Setup
First, we'll clone a sample project that contains a basic calculator implementation:
```bash
# Clone the repository
git clone https://github.com/junit-team/junit5-samples
cd junit5-samples/junit5-jupiter-starter-gradle
```

### 2. Development Environment Configuration
We need to install several VS Code extensions to support our development:

1. Open VS Code Extensions (Ctrl+Shift+X)
2. Install the following extensions:
   - "Extension Pack for Java" - Provides Java development essentials
   - "SonarLint" - For real-time code quality checking
   - "Test Runner for Java" - Enables test execution within VS Code
   - "Gradle for Java" - Supports Gradle project management

### 3. Understanding the Project Structure
Let's examine the key components of our project:

#### Calculator.java
Location: `src/main/java/com/example/project/Calculator.java`
```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}
```

This is our main class that we'll be testing. It contains:
- A simple addition method
- Two integer parameters
- Returns the sum as an integer
- Public access for testing purposes

#### CalculatorTests.java
Location: `src/test/java/com/example/project/CalculatorTests.java`
```java
class CalculatorTests {
    @Test
    @DisplayName("1 + 1 = 2")
    void addsTwoNumbers() {
        Calculator calculator = new Calculator();
        assertEquals(2, calculator.add(1, 1), "1 + 1 should equal 2");
    }

    @ParameterizedTest(name = "{0} + {1} = {2}")
    @CsvSource({
        "0,    1,   1",
        "1,    2,   3",
        "49,  51, 100",
        "1,  100, 101"
    })
    void add(int first, int second, int expectedResult) {
        Calculator calculator = new Calculator();
        assertEquals(expectedResult, calculator.add(first, second),
                () -> first + " + " + second + " should equal " + expectedResult);
    }
}
```

This test class demonstrates several important testing concepts:
1. Basic unit test with `@Test` annotation
2. Descriptive test naming using `@DisplayName`
3. Parameterized testing using `@ParameterizedTest` and `@CsvSource`
4. Assertion messages for clear failure reporting
5. Multiple test cases to verify different scenarios

### 4. Implementing Code Coverage
To ensure our tests are comprehensive, we'll add code coverage reporting using JaCoCo:

1. Update your `build.gradle` with the following configuration:
```gradle
plugins {
    id 'java'
    id 'eclipse' // optional (to generate Eclipse project files)
    id 'idea' // optional (to generate IntelliJ IDEA project files)
    id 'jacoco'  // ADD THIS LINE for code coverage
}

repositories {
    mavenCentral()
}

dependencies {
    testImplementation(platform('org.junit:junit-bom:5.11.3'))
    testImplementation('org.junit.jupiter:junit-jupiter')
    testRuntimeOnly('org.junit.platform:junit-platform-launcher')
}

test {
    useJUnitPlatform()
    testLogging {
        events "passed", "skipped", "failed"
        showStandardStreams = true  // ADD THIS LINE for more detailed output
    }
    finalizedBy jacocoTestReport  // ADD THIS LINE to auto-generate report after tests
}

// ADD THIS BLOCK for JaCoCo configuration
jacocoTestReport {
    dependsOn test
    reports {
        xml.required = false
        csv.required = false
        html.outputLocation = layout.buildDirectory.dir('jacocoHtml')
    }
}
```

#### Running Tests and Viewing Coverage
Execute the following commands:
```bash
./gradlew clean test
```

This will:
1. Clean previous build artifacts
2. Execute all test cases
3. Generate a coverage report
4. Display test results in the console

To analyze the coverage report:
- Open `build/jacocoHtml/index.html` in your browser
- Review the coverage metrics:
  - Line coverage
  - Branch coverage
  - Method coverage

## Next Steps
After completing this lab, you should:
- Understand basic unit testing concepts
- Be able to write simple unit tests
- Know how to run tests and view coverage reports
- Be familiar with code quality tools in VS Code
  
The next lab will build upon these fundamentals to explore more advanced testing concepts and patterns.

## Part 2: Code Quality Analysis and Test Enhancement

### Introduction
In this section, we'll explore common code quality issues and learn how static analysis tools can help identify and fix them. We'll also practice expanding our test suite to handle edge cases and improve code coverage.

### Learning Objectives
- Understand common code quality issues in Java
- Learn to use SonarLint for static code analysis
- Practice implementing robust error handling
- Develop comprehensive test cases
- Handle edge cases and error conditions

### 1. Understanding Code Quality Issues

Let's extend our Calculator class with additional functions that contain some basic code quality issues:

```java
/*
 * Copyright 2015-2024 the original author or authors.
 *
 * All rights reserved. This program and the accompanying materials are
 * made available under the terms of the Eclipse Public License v2.0 which
 * accompanies this distribution and is available at
 *
 * http://www.eclipse.org/legal/epl-v20.html
 */

package com.example.project;

public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        double temp;      // Issue: unused variable
        return a - b;
    }

    public int multiply(int a, int b) {
        int Result = a * b;  // Issues: 
                            // 1. non-standard variable naming (S117)
                            // 2. unnecessary temporary variable (S1488)
        return Result;
    }
}
```

### 2. Using SonarLint for Code Analysis

Let's examine each issue that SonarLint detects:

##### Issue 1: Unused Variable
```java
public int subtract(int a, int b) {
    double temp;      // Unused variable
    return a - b;
}
```

**SonarLint Warning:**
- java:S1481 - Remove this unused "temp" local variable

<details>
<summary>❓ Why is this a problem?</summary>

- Creates unnecessary memory allocation
- Makes code harder to read
- Could indicate incomplete implementation
- Creates confusion about the variable's purpose

</details>

<details>
<summary>✅ Solution</summary>

```java
public int subtract(int a, int b) {
    return a - b;
}
```
</details>

##### Issues 2 & 3: Variable Naming and Temporary Variable
```java
public int multiply(int a, int b) {
    int Result = a * b;
    return Result;
}
```

**SonarLint Warnings:**
1. java:S117 - Rename this local variable to match the regular expression '^[a-z][a-zA-Z0-9]*$'
2. java:S1488 - Immediately return this expression instead of assigning it to the temporary variable "Result"

<details>
<summary>❓ Why are these problems?</summary>

For S117 (Variable Naming):
- Violates Java naming conventions (variables should start with lowercase)
- Makes code less readable and maintainable
- Creates inconsistency in the codebase

For S1488 (Temporary Variable):
- Creates unnecessary variable allocation
- Adds complexity without adding value
- Makes the code more verbose than necessary

</details>

<details>
<summary>✅ Solution</summary>

```java
public int multiply(int a, int b) {
    return a * b;
}
```

**Explanation:**
- Removed temporary variable and returned expression directly
- Eliminated the naming issue by removing the variable altogether
- Made the code more concise and clear

</details>

### Final Corrected Code
```java
/*
 * Copyright 2015-2024 the original author or authors.
 *
 * All rights reserved. This program and the accompanying materials are
 * made available under the terms of the Eclipse Public License v2.0 which
 * accompanies this distribution and is available at
 *
 * http://www.eclipse.org/legal/epl-v20.html
 */

package com.example.project;

public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }
}
```

### 3. Using Amazon Q to Generate Unit Tests

In this section, we'll learn how to use Amazon Q to automatically generate unit tests for our Calculator class.

#### Prerequisites
- VS Code with Amazon Q extension installed
- Valid Amazon Q account
- Gradle project with JUnit 5

#### Exercise: Generate Tests for Subtract Method

1. Open VS Code and ensure you're logged into Amazon Q
2. Have both `Calculator.java` and `CalculatorTests.java` open
3. Use Amazon Q to generate tests for the subtract method

<details>
<summary>💡 How to Generate Tests with Amazon Q</summary>

1. Open Amazon Q chat panel
2. Enter the prompt: "Write a unit test for subtract method and integrate with my CalculatorTests.java"
3. Amazon Q will analyze your code and suggest appropriate tests

</details>

<details>
<summary>📝 Generated Test Code</summary>

_Based on your open CalculatorTests.java file, I'll show you how to add the subtract tests in the same style as your existing tests. I notice you're using JUnit Jupiter (JUnit 5) and you have some nice patterns with @DisplayName and @ParameterizedTest that we can follow:_
   
```java
@Test
@DisplayName("5 - 3 = 2")
void subtractsTwoNumbers() {
    Calculator calculator = new Calculator();
    assertEquals(2, calculator.subtract(5, 3), "5 - 3 should equal 2");
}

@ParameterizedTest(name = "{0} - {1} = {2}")
@CsvSource({
    "10,  5,   5",
    "10,  10,  0",
    "0,   5,  -5",
    "-10, -5, -5",
    "100, 99,  1"
})
void subtract(int first, int second, int expectedResult) {
    Calculator calculator = new Calculator();
    assertEquals(expectedResult, calculator.subtract(first, second),
            () -> first + " - " + second + " should equal " + expectedResult);
}
```

This follows the same pattern as your existing tests:
1. A simple test case with @DisplayName for basic subtraction
2. A parameterized test that covers multiple scenarios including:
- Regular positive number subtraction
- Subtracting same numbers (result is zero)
- Subtracting from zero (negative result)
- Subtracting negative numbers
- Subtracting numbers close to each other

The tests use the same style of assertion and lambda expression for the error message that you're using in your add tests. Just paste these methods into your existing CalculatorTests class alongside your add tests.
</details>

#### Running the Tests with Gradle

To run the tests using Gradle:

```bash
./gradlew test
```

#### Running the Tests with VS Code Test Explorer

1. Open Test Explorer in VS Code (flask/beaker icon in sidebar)
2. Find the "Run Test" button with coverage icon (play button with percentage symbol)
3. Click to run all tests with coverage

The report shows we're missing coverage for the `multiply()` method. We will address this in next section.


### 4. Using VS Code's Built-in Test Generator

Let's compare two approaches to generating unit tests: VS Code's built-in generator and Amazon Q's suggestions.

#### Using VS Code Test Generator
1. In the `Calculator.java` file, click on the Bulb (💡) icon next to multiply method
2. Select "Source Actions" → "Generate Tests"
3. Accept default prompt for target class name (`com.example.project.CalculatorTests`)
4. Select multiply method

VS Code adds empty test placeholder in our code:
```java
@Test
void testMultiply() {
    
}
```

#### Using Amazon Q's Intelligent Suggestions
When you open `CalculatorTests.java`, Amazon Q automatically analyzes your code context and suggests complete tests:

```java
@Test
@DisplayName("5 * 3 = 15")
void multipliesTwoNumbers() {
    Calculator calculator = new Calculator();
    assertEquals(15, calculator.multiply(5, 3), "5 * 3 should equal 15");
}

@ParameterizedTest(name = "{0} * {1} = {2}")
@CsvSource({
    "0,    1,   0",
    "1,    2,   2",
    "49,  51, 2499",
    "1,  100, 100"
})
void multiply(int first, int second, int expectedResult) {
    Calculator calculator = new Calculator();
    assertEquals(expectedResult, calculator.multiply(first, second),
            () -> first + " * " + second + " should equal " + expectedResult);
}
```

#### Comparison
<details>
<summary>🔍 Key Differences</summary>

- VS Code:
  - Provides basic structure only
  - Requires manual implementation
  - Quick way to create test placeholder

- Amazon Q:
  - Provides complete, ready-to-use tests
  - Includes parameterized tests
  - Matches existing code style
  - Covers multiple test scenarios
  - Adds meaningful display names
</details>

This example clearly demonstrates the advantage of using AI-powered tools like Amazon Q for test generation, saving time and providing more comprehensive test coverage out of the box.

## Part 3: Integrating Continuous Code Quality Checks into Azure DevOps Pipelines

### Objective
Implement continuous code quality checks by setting up an automated pipeline in Azure DevOps that includes SonarCloud analysis and code coverage reports.

### Prerequisites
- Azure DevOps account
- GitHub account (for creating a public repository)
- Access to SonarCloud (https://sonarcloud.io/)

### Section 1: Setting Up the Project and Basic Pipeline

#### 1. Create a Personal Access Token (PAT) in Azure DevOps

1. Log in to your Azure DevOps account
2. Go to User Settings (top right corner) > Personal Access Tokens
3. Click "New Token"
4. Name your token (e.g., "SonarCloudIntegration")
5. Set the expiration to at least 90 days
6. Under Scopes, select "Custom defined" and check "Code (Read & Write)"
7. Click "Create" and copy the generated token (you won't be able to see it again)

#### 2. Set Up SonarCloud Organization

1. Go to https://sonarcloud.io/ and log in with your Azure DevOps account
2. If it's your first time, you'll see 'Welcome to SonarCloud, how do you want to get started?'
3. Select 'Import an organization'
4. Create an organization:
   - Enter your Azure DevOps organization name
   - Paste your Personal Access Token
5. Note down `your-sonarcloud-organization-key` and `your-project-key` (you'll need this later)
6. Choose the free plan and click "Create Organization"

#### 3. Create a Public GitHub Repository

1. Log in to your GitHub account
2. Click the "+" icon in the top right and select "New repository"
3. Name your repository (e.g., "calculator-sonarcloud-demo")
4. Add a README file
5. Make sure it's set to "Private"
6. Click "Create repository"

#### 4. Create a New Pipeline in Azure DevOps

1. Log in to your Azure DevOps account
2. Create new PUBLIC project and open it: calculator-sonarcloud-demo

Note: You might have to enable the Public Projects option in security settings: https://learn.microsoft.com/en-us/azure/devops/organizations/projects/make-project-public?view=azure-devops
    
3. Go to Pipelines > New Pipeline
4. Choose GitHub as your code repository
5. Select your "calculator-sonarcloud-demo" repository
6. Choose "Starter pipeline" to begin with a basic YAML file
7. press 'Save and run'

#### 5. Update Pipeline Configuration

1. Clone your new GitHub repository locally:
   ```
   git clone https://github.com/yourusername/calculator-sonarcloud-demo.git
   cd calculator-sonarcloud-demo
   ```
2. Copy the following files and directories from your `junit5-jupiter-starter-gradle` project to your new repository:
   - `src/` directory (contains your Java source files and tests)
   - `build.gradle` (Gradle build configuration)
   - `gradlew` and `gradlew.bat` (Gradle wrapper scripts)
   - `gradle/` directory (contains Gradle wrapper jar and properties)
   - `.gitignore`

3. In your local repository, open the `azure-pipelines.yml` file
4. Replace the content with the following:

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: Gradle@3
  inputs:
    workingDirectory: ''
    gradleWrapperFile: 'gradlew'
    gradleOptions: '-Xmx3072m'
    javaHomeOption: 'JDKVersion'
    jdkVersionOption: '1.11'
    jdkArchitectureOption: 'x64'
    publishJUnitResults: true
    testResultsFiles: '**/TEST-*.xml'
    tasks: 'clean build test jacocoTestReport'
```

This pipeline will:
- Build your Java project using Gradle
- Run tests
- Generate JaCoCo coverage reports

#### 6. Push Calculator Code to GitHub

1. Commit these files to your new repository:
   ```
   git add .
   git commit -m "Initial commit: Add Calculator project files"
   git push origin main
   ```

With these steps completed, you have set up a basic pipeline for your Calculator project in Azure DevOps, integrated with your GitHub repository. The pipeline configuration is now in your repository, and any future changes can be made locally and pushed to GitHub.


### Section 2: Integrating SonarCloud Analysis

#### 1. Set Up SonarCloud Project

1. Go to https://sonarcloud.io/ and log in
2. Navigate to "Analyze new project"
3. Select your "calculator-sonarcloud-demo" repository
4. Choose "With Azure DevOps Pipelines" as the analysis method

### Section 2: Integrating SonarCloud Analysis

#### 1. Update build.gradle

First, update your `build.gradle` file to include the SonarQube plugin:

```gradle
plugins {
    id 'java'
    id 'eclipse' // optional (to generate Eclipse project files)
    id 'idea' // optional (to generate IntelliJ IDEA project files)
    id 'jacoco'  // for code coverage
    id "org.sonarqube" version "5.1.0.4882" // SonarQube plugin
}

// ... rest of your build.gradle file
```

#### 2. Update Azure Pipeline Configuration

Update your `azure-pipelines.yml` file with the following configuration:

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- checkout: self
  fetchDepth: 0

- task: SonarCloudPrepare@3
  inputs:
    SonarCloud: 'SonarConnection'
    organization: 'your-sonarcloud-organization'
    scannerMode: 'other'
    extraProperties: |
      # Additional properties that will be passed to the scanner,
      # Put one key=value per line, example:
      # sonar.exclusions=**/*.bin
      sonar.projectKey=your-project-key
      sonar.projectName=your-project-name

- task: Gradle@3
  inputs:
    workingDirectory: ''
    gradleWrapperFile: 'gradlew'
    gradleOptions: '-Xmx3072m'
    javaHomeOption: 'JDKVersion'
    jdkVersionOption: '17'
    jdkArchitectureOption: 'x64'
    publishJUnitResults: true
    testResultsFiles: '**/TEST-*.xml'
    tasks: 'clean build test jacocoTestReport sonar'

- task: SonarCloudPublish@3
  inputs:
    pollingTimeoutSec: '300'
```

Make sure to replace `your-sonarcloud-organization`, `your-project-key`, and `your-project-name` with your specific SonarCloud details.

#### 3. Explanation of Changes

1. **build.gradle update**: 
   - We've added the SonarQube plugin (version 5.1.0.4882) to the plugins section.
   - This allows us to run SonarQube analysis as part of our Gradle build.

2. **Azure Pipelines YAML changes**:
   - The `SonarCloudPrepare@3` task is configured with your SonarCloud connection and project details.
   - The Gradle task now includes `sonar` in its list of tasks to run.
   - We've updated the JDK version to 17 for compatibility with the latest SonarQube plugin.
   - The separate `SonarCloudAnalyze` task has been removed as the analysis is now part of the Gradle build.

#### 4. Run the Pipeline and Review Results

1. Commit and push your changes to both `build.gradle` and `azure-pipelines.yml` files.
2. In Azure DevOps, go to Pipelines and run your updated pipeline.
3. Once the pipeline completes, go to your project in SonarCloud.
4. Review the analysis results, including:
   - Code quality issues
   - Test coverage
   - Duplications
   - Maintainability, reliability, and security ratings

#### 5. Interpret SonarCloud Results

- **Quality Gate**: This is a set of boolean conditions that gives you instant feedback on your project. If all conditions are met, the quality gate is considered passed.
- **Issues**: These are potential bugs, code smells, or vulnerabilities in your code.
- **Coverage**: This shows how much of your code is covered by unit tests.
- **Duplications**: This indicates repeated code that could potentially be refactored.

By completing these steps, you've successfully integrated SonarCloud analysis into your Gradle build process and Azure DevOps pipeline. This setup will provide continuous code quality feedback every time you push changes to your repository.

For more detailed information on SonarQube Gradle plugin configuration, visit the [SonarQube Gradle Plugin documentation](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-gradle/).

### Section 3: Enhancing Code Coverage Reporting

To ensure our code coverage results are properly generated and can be used by both Azure DevOps and SonarCloud, we need to make some adjustments to our Gradle configuration and pipeline.

#### Task 1: Update Gradle Configuration for JaCoCo

Open your `build.gradle` file and add a configuration block for JaCoCo. This will ensure that the XML report is generated, which is required for integration with Azure DevOps and SonarCloud.

Try to add the necessary configuration to generate the XML report. 

<details>
<summary>Click here to see the solution</summary>

Add the following block to your `build.gradle` file:

```gradle
jacocoTestReport {
    dependsOn test
    reports {
        xml.required = true
        csv.required = false
        html.outputLocation = layout.buildDirectory.dir('jacocoHtml')
    }
}
```

This configuration does the following:
- Ensures the JaCoCo report task runs after the test task
- Sets the XML report to be required (this is crucial for integration with Azure DevOps and SonarCloud)
- Disables the CSV report (optional)
- Specifies the output location for the HTML report
</details>

#### Task 2: Update Azure Pipeline Configuration

Now that we've configured Gradle to generate the necessary reports, we need to update our Azure pipeline to publish these results. 

Update your `azure-pipelines.yml` file to include a task for publishing code coverage results.

<details>
<summary>Click here to see the solution</summary>

Add the following task to your `azure-pipelines.yml` file, after the Gradle task:

```yaml
# Optional: Publish coverage results to Azure DevOps
- task: PublishCodeCoverageResults@2
  inputs:
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/build/reports/jacoco/test/jacocoTestReport.xml'
    pathToSources: '$(System.DefaultWorkingDirectory)/src/main/java/'
```

This task does the following:
- Publishes the code coverage results to Azure DevOps
- Specifies the location of the JaCoCo XML report
- Indicates the path to the source code, allowing Azure DevOps to generate detailed coverage reports
</details>

#### Final Pipeline Configuration

After completing both tasks, your `azure-pipelines.yml` file should look similar to this:

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- checkout: self
  fetchDepth: 0

- task: SonarCloudPrepare@3
  inputs:
    SonarCloud: 'SonarConnection'
    organization: 'your-sonarcloud-organization'
    scannerMode: 'other'
    extraProperties: |
      # Additional properties that will be passed to the scanner,
      # Put one key=value per line, example:
      # sonar.exclusions=**/*.bin
      sonar.projectKey=your-project-key
      sonar.projectName=your-project-name

- task: Gradle@3
  inputs:
    workingDirectory: ''
    gradleWrapperFile: 'gradlew'
    gradleOptions: '-Xmx3072m'
    javaHomeOption: 'JDKVersion'
    jdkVersionOption: '17'
    jdkArchitectureOption: 'x64'
    publishJUnitResults: true
    testResultsFiles: '**/TEST-*.xml'
    tasks: 'clean build test jacocoTestReport sonar'
    
# Optional: Publish coverage results to Azure DevOps
- task: PublishCodeCoverageResults@2
  inputs:
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/build/reports/jacoco/test/jacocoTestReport.xml'
    pathToSources: '$(System.DefaultWorkingDirectory)/src/main/java/'

- task: SonarCloudPublish@3
  inputs:
    pollingTimeoutSec: '300'
```

With these changes, your pipeline will now generate detailed code coverage reports, publish them to Azure DevOps, and ensure that SonarCloud has access to this information for its analysis.

### Section 4: Introducing and Fixing Code Quality Issues

In this lab, we'll create a new Java file with intentional code quality issues, analyze it with SonarCloud, and then fix some of the issues to see how the code quality improves.

#### Step 1: Create a new Java file with problematic code

Create a new file named `BadCodeExamples.java` in the `src/main/java/com/example/project` directory of your project with the following content:

```java
package com.example.project;

import java.util.ArrayList;
import java.util.List;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Base64;

public class BadCodeExamples {

    public static String PASSWORD = "mySecretPassword123"; // Security hotspot: Hardcoded credentials

    public void unusedMethod() {
        // Code smell: Unused method
    }

    public int divideNumbers(int a, int b) {
        return a / b; // Bug: Potential division by zero
    }

    public void nullPointerExample(String str) {
        if (str.length() > 5) { // Bug: Potential null pointer dereference
            System.out.println(str);
        }
    }

    public List<Integer> createList() {
        ArrayList<Integer> list = new ArrayList<Integer>(); // Code smell: Diamond operator missing
        return list;
    }

    public void infiniteLoop() {
        while (true) { // Bug: Infinite loop
            System.out.println("This will run forever");
        }
    }

    public void duplicateCode() {
        // Code smell: Duplicate code
        for (int i = 0; i < 10; i++) {
            System.out.println("Duplicate code: " + i);
        }

        for (int i = 0; i < 10; i++) {
            System.out.println("Duplicate code: " + i);
        }
    }

    public void unusedVariable() {
        int unused = 42; // Code smell: Unused variable
    }

    public void emptyTryCatchBlock() {
        try {
            // Some code that might throw an exception
        } catch (Exception e) {
            // Bug: Empty catch block
        }
    }

    public String concatenateStrings(List<String> strings) {
        String result = "";
        for (String s : strings) {
            result += s; // Code smell: Inefficient string concatenation
        }
        return result;
    }

    public void sqlInjectionVulnerability(String userInput) {
        try {
            Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/test", "user", "password");
            String query = "SELECT * FROM users WHERE username = '" + userInput + "'"; // Security: SQL Injection vulnerability
            conn.createStatement().executeQuery(query);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void weakEncryption(String password) {
        String encoded = Base64.getEncoder().encodeToString(password.getBytes()); // Security: Weak encryption
        System.out.println("Encoded: " + encoded);
    }

    public void unsafeDeserialization(byte[] data) {
        try {
            java.io.ObjectInputStream ois = new java.io.ObjectInputStream(new java.io.ByteArrayInputStream(data));
            Object obj = ois.readObject(); // Security: Unsafe deserialization
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### Step 2: Push changes to the calculator-sonarcloud-demo repository

1. Stage the new file for commit:
   ```
   git add src/main/java/com/example/project/BadCodeExamples.java
   ```

2. Commit the changes:
   ```
   git commit -m "Add BadCodeExamples.java with intentional issues for SonarCloud analysis"
   ```

3. Push the changes to your remote repository:
   ```
   git push origin main
   ```

#### Step 3: Analyze findings in SonarCloud

1. Wait for the Azure DevOps pipeline to complete. This will trigger the SonarCloud analysis.
2. Once the build is finished, go to your project in SonarCloud (https://sonarcloud.io).
3. Review the analysis results, paying attention to:
   - New issues introduced by the `BadCodeExamples.java` file
   - The types of issues detected (bugs, vulnerabilities, code smells, security hotspots)
   - The severity levels of the detected issues

#### Step 4: Fix some issues

1. Choose at least 5 issues reported by SonarCloud to fix. Try to include a mix of bugs, vulnerabilities, and code smells.
2. Make the necessary changes to the `BadCodeExamples.java` file to address these issues.
3. Here are some suggestions for fixes you could implement:
   - Remove the hardcoded password
   - Add a null check in the `nullPointerExample` method
   - Use the diamond operator in the `createList` method
   - Remove the infinite loop
   - Use a StringBuilder in the `concatenateStrings` method
   - Use parameterized queries to prevent SQL injection in the `sqlInjectionVulnerability` method

4. After making your changes, commit and push your updates:
   ```
   git add src/main/java/com/example/project/BadCodeExamples.java
   git commit -m "Fix several issues reported by SonarCloud"
   git push origin main
   ```

#### Step 5: Analyze improvements in SonarCloud

1. Wait for the Azure DevOps pipeline to complete again.
2. Go back to your project in SonarCloud and review the new analysis results.
3. Compare the new results with the previous analysis:
   - How many issues were resolved?
   - Did fixing some issues reveal any new ones?
   - How did the overall project metrics change?

#### Step 6: Reflection

Answer the following questions:

1. How many issues did SonarCloud initially detect in the `BadCodeExamples.java` file?
2. How do tools like SonarCloud help in maintaining code quality and security in a development process?
3. What types of issues were most common (bugs, vulnerabilities, code smells, or security hotspots)?
4. Which issues do you think are the most critical to address, and why?

By completing this lab, you'll gain hands-on experience with introducing, identifying, and fixing common code quality issues and security vulnerabilities, reinforcing the importance of static code analysis and continuous improvement in the development process.
