# Lab 2: Implementing Unit Testing in a Gradle Project

## Introduction

In this lab, we'll explore unit testing in a Gradle project. Gradle is a build automation tool that manages dependencies and the build process for your project. By default, Gradle uses JUnit for unit testing in Java projects. JUnit is a popular testing framework for Java applications.

### Prerequisites
- Completed Lab 1: Build Automation with Gradle and Azure Pipelines
- Java Development Kit (JDK) installed
- Gradle installed
- An IDE or text editor (preferably Visual Studio Code with Java extensions)

### Initial Setup

1. **Sync your repository from Lab 1**
   Open a terminal and navigate to your project directory. Then run:
   ```bash
   git fetch origin
   git checkout main
   git pull origin main
   ```
   This will ensure you have the latest version of your project, including the `azure-pipelines.yml` file added in Lab 1.

2. **Open the project in your IDE**
   Open your Gradle project from Lab 1 in your preferred IDE or text editor.

## Part 1: Understanding and Expanding Unit Tests

### Steps

1. **Review existing files**
   Open your Gradle project from Lab 1 and examine the following files:
   
   `app/src/main/java/org/example/App.java`:
   ```java
   package org.example;

   public class App {
       public String getGreeting() {
           return "Hello World!";
       }

       public static void main(String[] args) {
           System.out.println(new App().getGreeting());
       }
   }
   ```
   This is the main application class. It has a `getGreeting()` method that returns a string and a `main()` method that prints the greeting.

   `app/src/test/java/org/example/AppTest.java`:
   ```java
   package org.example;

   import org.junit.jupiter.api.Test;
   import static org.junit.jupiter.api.Assertions.*;

   class AppTest {
       @Test void appHasAGreeting() {
           App classUnderTest = new App();
           assertNotNull(classUnderTest.getGreeting(), "app should have a greeting");
       }
   }
   ```
   This is the test class for `App`. It uses JUnit 5 (also known as JUnit Jupiter) for testing. The `@Test` annotation marks a method as a test method.

2. **Understand the existing test**
   The existing test `appHasAGreeting()` verifies that the `getGreeting()` method returns a non-null value. It uses the `assertNotNull()` method from JUnit's Assertions class.

   ```java
   @Test void appHasAGreeting() {
       App classUnderTest = new App();
       assertNotNull(classUnderTest.getGreeting(), "app should have a greeting");
   }
   ```

   This test:
   1. Creates a new instance of the `App` class.
   2. Calls the `getGreeting()` method on this instance.
   3. Asserts that the returned value is not null.
   4. If the assertion fails, it will display the message "app should have a greeting".

3. **Add a new test**
   Let's add a more specific test. In `AppTest.java`, add a new test method inside the `AppTest` class:

   ```java
   @Test
   void greetingIsHelloWorld() {
       App classUnderTest = new App();
       assertEquals("Hello World!", classUnderTest.getGreeting(), "greeting should be 'Hello World!'");
   }
   ```

   This new test:
   1. Creates a new instance of the `App` class.
   2. Calls the `getGreeting()` method.
   3. Uses `assertEquals()` to check if the returned greeting exactly matches "Hello World!".
   4. If the assertion fails, it will display the message "greeting should be 'Hello World!'".

4. **Run the tests locally**
   In your terminal, navigate to your project root directory and run:
   ```
   ./gradlew test
   ```
   
   This command tells Gradle to run all tests in the project. Gradle will compile your code, run the tests, and provide a summary of the results.

5. **View the test results**
   After running the tests, Gradle generates a test report. You can find it at:
   `app/build/reports/tests/test/index.html`
   
   Open this file in a web browser to view detailed results of your test run.

6. **Modify the App class to cause a test failure**
   To understand how tests catch changes in behavior, let's modify the `App` class. Open `app/src/main/java/org/example/App.java` and change the `getGreeting()` method:

   ```java
   public String getGreeting() {
       return "Hello, DevOps!";
   }
   ```

7. **Run the tests again**
   Run the Gradle test command again:
   ```
   ./gradlew test
   ```

8. **Examine the failed test results**
   Open the new test report and analyze the failure message. You should see that the `greetingIsHelloWorld` test has failed, while the `appHasAGreeting` test still passes.

### Expected Outcomes
- Initially, both tests should pass.
- After modifying the `App` class, the `greetingIsHelloWorld` test should fail, but `appHasAGreeting` should still pass.
- The test report should clearly indicate which test failed and why.

### Reflection Questions
1. Why did changing the greeting cause one test to fail but not the other?
2. How does this demonstrate the importance of specific test assertions?
3. In a real-world scenario, how might this kind of test failure be useful?
4. How would you modify the test to make it pass with the new greeting?

### Conclusion
In this part of the lab, you've explored the basics of unit testing in a Gradle project using JUnit 5. You've seen how existing tests work, added your own test, and experienced how changing code can lead to test failures. This process demonstrates the value of unit tests in catching unintended changes in behavior.

Remember, unit tests are a crucial part of the DevOps pipeline. They provide quick feedback to developers, catch bugs early in the development process, and serve as documentation for how code should behave. As you continue through this course, you'll see how these tests fit into the larger picture of continuous integration and delivery.

## Part 2: Test-Driven Development (TDD)

### Introduction to TDD
Test-Driven Development (TDD) is a software development process that relies on the repetition of a very short development cycle. It aims to improve software quality and reduce the time spent on debugging. The TDD cycle, often called "Red-Green-Refactor", consists of three steps:

1. Red: Write a test that fails. This step defines the desired improvement or new function.
2. Green: Write the minimum amount of code to pass the test.
3. Refactor: Improve the code without changing its functionality.

This approach can lead to more modular, flexible, and extensible code. It also serves as living documentation of the system's intended behavior.

### Objective
Implement a new feature using the Test-Driven Development (TDD) approach. We'll add a method to reverse the greeting string.

### Steps

1. **Update the existing test**
   Before we start with TDD for our new feature, we need to address the fact that we're changing the existing functionality. In `AppTest.java`, modify the existing test:

   ```java
   @Test
   void greetingContainsHelloWorld() {
       App classUnderTest = new App();
       assertTrue(classUnderTest.getGreeting().contains("Hello World!"), "greeting should contain 'Hello World!'");
   }
   ```

   This change makes our test more flexible, allowing for additional text in the greeting. We use `assertTrue()` and the `contains()` method to check if the greeting includes "Hello World!" anywhere in the string.

2. **Update the App class**
   In `App.java`, modify the `getGreeting()` method:

   ```java
   public String getGreeting() {
       return "Hello World! Welcome to TDD.";
   }
   ```

   This change adds more text to our greeting, demonstrating how requirements might evolve in a real project.

3. **Run the tests**
   ```
   ./gradlew test
   ```
   Verify that the updated test passes with the new greeting. This ensures our changes haven't broken existing functionality.

4. **Write the test for the new feature**
   Now we can start the TDD process for our new reverse greeting feature. In `AppTest.java`, add:

   ```java
   @Test
   void reverseGreetingTest() {
       App classUnderTest = new App();
       assertEquals(".DDT ot emocleW !dlroW olleH", classUnderTest.getReverseGreeting(), "reverse greeting should be '.DDT ot emocleW !dlroW olleH'");
   }
   ```

   This test expects a method `getReverseGreeting()` to exist and return the reversed string of our greeting. Note that we're writing this test before implementing the feature - this is the key principle of TDD.

5. **Run the tests**
   ```
   ./gradlew test
   ```
   Observe that the new test fails. This is the "Red" phase of TDD. The test fails because we haven't implemented the `getReverseGreeting()` method yet.

6. **Implement the feature**
   Now we move to the "Green" phase. We'll write the minimum code necessary to make the test pass. In `App.java`, add the new method:

   ```java
   public String getReverseGreeting() {
       return new StringBuilder(getGreeting()).reverse().toString();
   }
   ```

   This method uses Java's StringBuilder to reverse the string returned by `getGreeting()`.

7. **Run the tests again**
   ```
   ./gradlew test
   ```
   All tests should now pass. We've reached the "Green" phase of TDD.

8. **Refactor (if necessary)**
   The "Refactor" phase is about improving the code without changing its functionality. In this case, our implementation is simple and doesn't require refactoring. In real-world scenarios, you might refactor your code at this stage to improve its structure or performance while keeping the tests passing.

### Expected Outcomes
- Initially, the new test should fail (Red phase)
- After implementing the `getReverseGreeting()` method, all tests should pass (Green phase)

### Reflection Questions
1. How does TDD differ from the approach we took in Part 1?
2. What are the advantages of writing the test before implementing the feature?
3. Can you think of any challenges that might arise when using TDD in a real-world project?

### Conclusion
In this part of the lab, you've not only experienced the TDD workflow but also learned how to handle changing requirements. You've seen how existing tests might need to be updated as the software evolves, and how TDD can guide the implementation of new features. This reflects real-world scenarios where requirements often change, and tests need to be maintained alongside the code they're testing.

## Part 3: Mocking with Mockito

### Introduction to Mocking and Mockito
Mocking is a technique used in unit testing to create objects that simulate the behavior of real objects. Mockito is a popular mocking framework for Java applications. While we often use mocking for external dependencies, Mockito is versatile and can be used to mock various types of objects:

- External dependencies (e.g., web services, databases)
- Internal classes and interfaces within your application
- Concrete classes
- Static methods (with Mockito 3.4.0 and above)
- Final classes and methods (with the inline mock maker enabled)
- Classes within specific packages

Mocking allows you to:
- Test your code in isolation
- Control the behavior of dependencies
- Test edge cases and error scenarios easily
- Simulate entire subsystems of your application

### Objective
Learn how to use Mockito to mock dependencies in unit tests. We'll create a new class that depends on an external service and write tests for it using Mockito.

### Steps

1. **Add Mockito dependency**
   Open your `app/build.gradle` file and add the following to the `dependencies` block:

   ```groovy
   testImplementation 'org.mockito:mockito-core:3.12.4'
   testImplementation 'org.mockito:mockito-junit-jupiter:3.12.4'
   ```

   This adds Mockito Core and the Mockito-JUnit integration to your project.

2. **Create a new interface for an external service**
   Create a new file `WeatherService.java` in the `app/src/main/java/org/example` directory:

   ```java
   package org.example;

   public interface WeatherService {
       String getCurrentWeather(String city);
   }
   ```

   This interface represents an external weather service. In a real application, this might make HTTP requests or database queries.

3. **Create a new class that uses the WeatherService**
   Create a new file `WeatherApp.java` in the same directory:

   ```java
   package org.example;

   public class WeatherApp {
       private final WeatherService weatherService;

       public WeatherApp(WeatherService weatherService) {
           this.weatherService = weatherService;
       }

       public String getWeatherGreeting(String city) {
           String weather = weatherService.getCurrentWeather(city);
           return "Hello! The current weather in " + city + " is " + weather;
       }
   }
   ```

   This class depends on the `WeatherService` to get the current weather for a city. By using dependency injection (passing `WeatherService` in the constructor), we make this class easier to test.

4. **Write a test class using Mockito**
   Create a new file `WeatherAppTest.java` in the `app/src/test/java/org/example` directory:

   ```java
   package org.example;

   import org.junit.jupiter.api.Test;
   import org.junit.jupiter.api.extension.ExtendWith;
   import org.mockito.Mock;
   import org.mockito.junit.jupiter.MockitoExtension;

   import static org.junit.jupiter.api.Assertions.assertEquals;
   import static org.mockito.Mockito.when;

   @ExtendWith(MockitoExtension.class)
   class WeatherAppTest {

       @Mock
       private WeatherService weatherService;

       @Test
       void testGetWeatherGreeting() {
           // Arrange
           WeatherApp weatherApp = new WeatherApp(weatherService);
           when(weatherService.getCurrentWeather("New York")).thenReturn("sunny");

           // Act
           String greeting = weatherApp.getWeatherGreeting("New York");

           // Assert
           assertEquals("Hello! The current weather in New York is sunny", greeting);
       }
   }
   ```

   This test class uses the "Arrange-Act-Assert" pattern, which is a common structure for organizing unit tests:

   1. Arrange:
      This is where you set up the conditions for your test. You prepare the objects, create mocks, and set up any necessary data or state.
      In our test:
      ```java
      WeatherApp weatherApp = new WeatherApp(weatherService);
      when(weatherService.getCurrentWeather("New York")).thenReturn("sunny");
      ```
      We create the `WeatherApp` object and set up the mock `weatherService` to return "sunny" when asked about New York's weather.

   2. Act:
      This is where you perform the action that you're testing. Typically, this is a call to the method you're testing.
      In our test:
      ```java
      String greeting = weatherApp.getWeatherGreeting("New York");
      ```
      We call the method we're testing, `getWeatherGreeting("New York")`.

   3. Assert:
      This is where you verify that the action produced the expected result. You use assertions to check if the output or resulting state matches what you expect.
      In our test:
      ```java
      assertEquals("Hello! The current weather in New York is sunny", greeting);
      ```
      We check if the returned greeting matches what we expect.

   This pattern helps make tests more readable and maintainable by clearly separating the three phases of a test. It allows other developers (including your future self) to quickly understand what each test is doing, what it's testing, and what the expected outcome is.

5. **Run the tests**
   ```
   ./gradlew test
   ```

   The test should pass, demonstrating that our `WeatherApp` class works correctly with the mocked `WeatherService`.

6. **Add another test case**
   Add another test method to `WeatherAppTest.java`:

   ```java
   @Test
   void testGetWeatherGreetingForDifferentCity() {
       // Arrange
       WeatherApp weatherApp = new WeatherApp(weatherService);
       when(weatherService.getCurrentWeather("Dublin")).thenReturn("rainy");

       // Act
       String greeting = weatherApp.getWeatherGreeting("Dublin");

       // Assert
       assertEquals("Hello! The current weather in Dublin is rainy", greeting);
   }
   ```

   This test case checks if our `WeatherApp` works correctly for a different city, demonstrating how we can easily test different scenarios using mocks.

7. **Run the tests again**
   ```
   ./gradlew test
   ```

   Both tests should pass, showing that our `WeatherApp` works as expected for different inputs.

### Expected Outcomes
- All tests should pass
- You should be able to test the `WeatherApp` class without needing a real `WeatherService` implementation

### Reflection Questions
1. Why is mocking useful in unit testing?
2. In what situations might you choose to use a mock object instead of a real object in your tests?

### Conclusion
In this part of the lab, you've learned how to use Mockito to create mock objects for your unit tests. Mocking is a powerful technique that allows you to test your code in isolation, control the behavior of dependencies, and easily test different scenarios.

While we used mocking for an external service in this example, remember that Mockito can be used to mock various types of objects within your application. This flexibility makes it a valuable tool for creating comprehensive unit tests.

In a real-world application, you might use mocking to test code that interacts with databases, web services, or other external systems. You could also use it to isolate parts of your own application for testing, such as mocking one subsystem while testing another

## Part 4: Code Coverage with JaCoCo

### Introduction to Code Coverage
Code coverage is a measure used to describe the degree to which the source code of a program is executed when a particular test suite runs. It provides insight into how much of your code is being tested and can help identify areas of your codebase that may need additional testing.

### Steps

1. **Add JaCoCo plugin to Gradle**
   Open your `app/build.gradle` file and add the JaCoCo plugin to your existing plugins block:

   ```groovy
   plugins {
       // Existing plugins...
       id 'application'
       // Add JaCoCo plugin
       id 'jacoco'
   }
   ```

   This adds the JaCoCo plugin to your Gradle project, enabling code coverage functionality.

2. **Configure JaCoCo version**
   Add the following configuration after your existing configurations:

   ```groovy
   jacoco {
       toolVersion = "0.8.7"
   }
   ```

   This sets the version of JaCoCo to use. You can update this version as newer releases become available.

3. **Configure test task to generate JaCoCo report**
   Modify your existing test task configuration:

   ```groovy
   tasks.named('test') {
       // Existing configuration...
       useJUnitPlatform()
       // Add this line to generate JaCoCo report after tests
       finalizedBy jacocoTestReport
   }
   ```

   This ensures that the JaCoCo report is generated automatically after your tests run.

4. **Configure JaCoCo test report**
   Add the following configuration:

   ```groovy
   tasks.named('jacocoTestReport') {
       dependsOn test
       reports {
           xml.required = true
           csv.required = false
           html.outputLocation = layout.buildDirectory.dir('jacocoHtml')
       }
   }
   ```

   This configures the JaCoCo test report task to:
   - Depend on the test task
   - Generate XML and HTML reports (CSV is disabled)
   - Set the output location for the HTML report

5. **Set up coverage verification**
   Add the following configuration:

   ```groovy
   tasks.named('jacocoTestCoverageVerification') {
       violationRules {
           rule {
               limit {
                   minimum = 0.5
               }
           }
       }
   }
   ```

   This sets up a coverage verification rule that requires a minimum of 50% code coverage. You can adjust this threshold as needed.

6. **Include coverage verification in check task**
   Add the following configuration:

   ```groovy
   tasks.named('check') {
       dependsOn jacocoTestCoverageVerification
   }
   ```

   This ensures that coverage verification is run as part of the `check` task, which is commonly used in CI/CD pipelines.

7. **Run tests with coverage**
   Execute the following command:

   ```
   ./gradlew test
   ```

   This will run your tests and generate a coverage report.

8. **View the coverage report**
   Open the generated HTML report in your browser. It should be located at:
   `app/build/jacocoHtml/index.html`

9. **Run coverage verification**
   Execute the following command:

   ```
   ./gradlew check
   ```

   This will run your tests, generate a coverage report, and verify that your code meets the minimum coverage threshold we set (50%).

10. **Experiment with thresholds**
   Try adjusting the minimum coverage threshold in your `build.gradle` file and re-run the verification. For example:

   ```groovy
   jacocoTestCoverageVerification {
       violationRules {
           rule {
               limit {
                   minimum = 0.9
               }
           }
       }
   }
   ```

   This sets the minimum coverage to 90%. Run `./gradlew check` again and observe the results.

### Expected Outcomes
- A JaCoCo HTML report should be generated in the `app/build/jacocoHtml/` directory
- The `check` task should pass if your code meets the coverage threshold, and fail if it doesn't

### Reflection Questions
1. Why might we want to set a minimum coverage threshold?
2. What strategies could you use to improve the code coverage of your tests?

### Conclusion
Code coverage is a valuable metric for assessing the thoroughness of your test suite. However, it's important to remember that high coverage doesn't necessarily equate to high-quality tests or bug-free code. Coverage should be used as one of many tools in your quality assurance toolkit.

## Part 5: Updating Azure Pipelines with Tests and Code Coverage

### Objective
Update the existing Azure Pipeline to run unit tests and generate code coverage reports.

### Steps

1. **Update azure-pipelines.yml**
   Open the `azure-pipelines.yml` file in your project root and replace its content with the following:

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
       jdkVersionOption: '17'
       jdkArchitectureOption: 'x64'
       publishJUnitResults: true
       testResultsFiles: '**/TEST-*.xml'
       tasks: 'build jacocoTestReport'

   - task: PublishCodeCoverageResults@2
     inputs:
       summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/build/reports/jacoco/test/jacocoTestReport.xml'
       pathToSources: '$(System.DefaultWorkingDirectory)/app/src/main/java/'

   - task: Gradle@3
     inputs:
       workingDirectory: ''
       gradleWrapperFile: 'gradlew'
       gradleOptions: '-Xmx3072m'
       javaHomeOption: 'JDKVersion'
       jdkVersionOption: '17'
       jdkArchitectureOption: 'x64'
       tasks: 'showProjectInfo'
   ```

   This updated configuration:
   - Modifies the first Gradle task to include `jacocoTestReport` generation
   - Adds a new task to publish code coverage results
   - Keeps the existing `showProjectInfo` task

2. **Commit and push the changes**
   ```bash
   git add .
   git commit -m "Update project with new tests and code coverage"
   git push origin main
   ```
   This will stage all changes made throughout the lab, commit them, and push to your remote repository.

3. **Run the pipeline**
   - Go to your Azure DevOps project
   - Navigate to Pipelines
   - Select your pipeline
   - Click "Run pipeline"

4. **View test results and code coverage**
   - After the pipeline runs, go to the pipeline summary
   - You should see a "Tests" tab with the results of your unit tests
   - You should also see a "Code Coverage" tab with the JaCoCo coverage report

### Expected Outcomes
- The pipeline should successfully build your project, run tests, and generate code coverage reports
- Test results should be visible in the Azure DevOps interface
- Code coverage reports should be generated and visible in Azure DevOps
- The `showProjectInfo` task should still run and display project information

### Reflection Questions
1. How does integrating tests and code coverage into the CI pipeline benefit the development process?
2. What challenges might arise when running tests in a CI environment that don't occur locally?

### Conclusion
By updating your Azure Pipeline to include test execution and code coverage reporting, you've enhanced your CI/CD process while maintaining the custom project information task. This setup allows you to automatically verify that your code is working as expected, maintain visibility into the test coverage of your codebase, and keep track of important project details with every change. This comprehensive approach supports code quality, early issue detection, and informed decision-making throughout the development cycle.
