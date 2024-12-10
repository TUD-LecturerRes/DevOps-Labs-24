# Lab 4: Introduction to UI Testing with Selenium WebDriver

## Introduction

In this lab, you'll learn about Selenium WebDriver, a popular tool for automated web testing. Selenium WebDriver allows you to write code that controls web browsers automatically, making it ideal for testing web applications.

### Why Selenium?
* It's the industry standard for web UI testing
* Supports all major browsers (Chrome, Firefox, Safari, Edge)
* Works with multiple programming languages (we'll use Java)
* Free and open source
* Large community and extensive documentation

For more details about Selenium and its capabilities, visit the [official documentation](https://www.selenium.dev/documentation/webdriver/getting_started/).

## Learning Objectives
By completing this lab, you will:
* Set up Selenium WebDriver in your Spring Boot project
* Write basic automated tests for your web application
* Learn how to locate and interact with web elements
* Implement automated login testing

## Prerequisites
✅ Required before starting:
* Java Development Kit (JDK) 17 installed
* Chrome browser installed
* Your Spring Boot application from previous labs
* Basic understanding of HTML and web elements
* 
### Important Note
This lab builds on the Spring Boot secure web application from previous labs. Make sure your application is working correctly before proceeding.

## Part 1: Setting Up Your Environment

### 1. Verify Spring Boot Application

First, ensure your Spring Boot application works:

```bash
# Run the application
./gradlew bootRun

# In a browser, verify:
http://localhost:8080
```

You should see the welcome page. Keep this running in a separate terminal.

### 2. Update Build Configuration

Add Selenium dependencies to your `build.gradle`:

```gradle
dependencies {
    // Keep your existing dependencies
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity6:3.1.2.RELEASE'
    
    // Test dependencies
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.security:spring-security-test'
    
    // Add Selenium dependencies
    testImplementation 'org.seleniumhq.selenium:selenium-java:4.27.0'
}

test {
    useJUnitPlatform()
    testLogging {
        events "passed", "skipped", "failed"
        showExceptions true
        exceptionFormat "full"
        showCauses true
        showStackTraces true
    }
}
```

### 3. Refresh Dependencies

```bash
./gradlew clean
./gradlew --refresh-dependencies
```

## First Script: Testing the Home Page

Now that we have Selenium set up, let's create our first test script. We'll test the home page of our Spring Boot application, which contains:
* A welcome message
* A link to the greeting page

### Create Test Directory Structure

First, create this test directory in your project:
```bash
src/
└── test/
    └── java/
        └── com/
            └── example/
                └── securingweb/
                    └── selenium/
                        └── HomePageTest.java
```

### Create Your First Test

Create `HomePageTest.java` with this content:

```java
package com.example.securingweb.selenium;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;

public class HomePageTest {
    private WebDriver driver;

    @BeforeEach
    public void setup() {
        // 1. Start the session
        driver = new ChromeDriver();
    }

    @Test
    public void testHomePage() {
        // 2. Take action on browser - navigate to home page
        driver.get("http://localhost:8080");

        // 3. Request browser information
        String pageTitle = driver.getTitle();
        assertEquals("Spring Security Example", pageTitle);

        // 4. Find elements
        WebElement heading = driver.findElement(By.tagName("h1"));
        WebElement paragraph = driver.findElement(By.tagName("p"));
        WebElement link = driver.findElement(By.cssSelector("a[href='/hello']"));

        // 5. Request element information
        assertEquals("Welcome!", heading.getText());
        assertTrue(paragraph.getText().contains("Click"));
        assertEquals("here", link.getText());
    }

    @AfterEach
    public void teardown() {
        // 6. End the session
        if (driver != null) {
            driver.quit();
        }
    }
}
```

### Understanding the Code

Let's break down the main components of our Selenium test:

1. **Start the session**
   ```java
   driver = new ChromeDriver();
   ```
   Creates a new Chrome browser instance.

2. **Navigate to page**
   ```java
   driver.get("http://localhost:8080");
   ```
   Opens our application's home page.

3. **Verify page title**
   ```java
   String pageTitle = driver.getTitle();
   assertEquals("Spring Security Example", pageTitle);
   ```
   Checks if we're on the right page.

4. **Find and verify elements**
   ```java
   WebElement heading = driver.findElement(By.tagName("h1"));
   assertEquals("Welcome!", heading.getText());
   ```
   Locates and verifies the welcome message.

5. **Check link presence**
   ```java
   WebElement link = driver.findElement(By.cssSelector("a[href='/hello']"));
   assertEquals("here", link.getText());
   ```
   Verifies the greeting link exists.

6. **Clean up**
   ```java
   driver.quit();
   ```
   Closes the browser.

### Running the Test

1. Start your Spring Boot application
2. Run the test:
```bash
# Make sure your Spring Boot app is running in another terminal
./gradlew test --tests HomePageTest
```

### Expected Results
When you run the test:
* Chrome browser will open
* Navigate to home page
* Verify page title and content
* Close the browser


# Part 2: Testing User Workflows with Selenium

## Introduction
In real-world applications, User Acceptance Testing (UAT) focuses on verifying that software meets business requirements and user expectations. Let's simulate a real UAT scenario for our authentication system.

## Feature: User Authentication System
As a website user,
I want to securely access protected content,
So that I can view my personalized information.

### Acceptance Criteria
* Users can log in within 3 seconds
* Invalid credentials show clear error messages
* Protected pages redirect to login
* Login page is accessible from any protected page
* Successful login redirects to originally requested page
* Logout clears session and redirects to home
* 'Remember me' function persists between sessions

### Pass Threshold
* 100% success rate for critical paths (login, logout, security redirects)
* Maximum response time: 3 seconds for any operation
* All error messages must be user-friendly and visible
* No security bypasses possible

## Test Scenarios

### 1. Happy Path (Critical)
```gherkin
Scenario: User successfully accesses protected content
Given user is on the home page
When user clicks on protected content link
And user is redirected to login page
And user enters valid credentials
Then user should see protected content within 3 seconds
And user name should be displayed correctly
```

### 2. Authentication Failures (Critical) - Optional
```gherkin
Scenario: User enters invalid credentials
Given user is on the login page
When user enters invalid credentials
Then error message should be displayed
And user should remain on login page
```

### 3. Session Management (Important) - Optional
```gherkin
Scenario: User logs out successfully
Given user is logged in
When user clicks logout
Then user should be redirected to home page
And protected content should not be accessible
```

### 4. Security Validations (Critical) - Optional
```gherkin
Scenario: Direct access to protected pages
Given user is not authenticated
When user tries to access protected URL directly
Then user should be redirected to login page
```

## Part 2.1: Implementing Happy Path Test

First, let's implement our critical happy path scenario:
```gherkin
Scenario: User successfully accesses protected content
Given user is on the home page
When user clicks on protected content link
And user is redirected to login page
And user enters valid credentials
Then user should see protected content within 3 seconds
And user name should be displayed correctly
```

### 1. Create Test Structure

Create new files:
```bash
src/
└── test/
    └── java/
        └── com/
            └── example/
                └── securingweb/
                    └── selenium/
                        ├── tests/
                        │   └── AuthenticationWorkflowTest.java
                        └── pages/
                            ├── HomePage.java
                            ├── LoginPage.java
                            └── HelloPage.java
```

### 2. Create Page Objects

First, let's create our page objects. These represent the web pages we're testing:

Note: The Page Object Pattern is a design pattern that creates an object repository for storing all web elements. It helps reduce code duplication and improves test maintenance.

`HomePage.java`:
```java
package com.example.securingweb.selenium.pages;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;

public class HomePage {
    private WebDriver driver;

    @FindBy(css = "a[href='/hello']")
    private WebElement helloLink;

    public HomePage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    public void navigateTo() {
        driver.get("http://localhost:8080");
    }

    public void clickHelloLink() {
        helloLink.click();
    }
}
```

`LoginPage.java`:
```java
package com.example.securingweb.selenium.pages;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;

public class LoginPage {
    private WebDriver driver;
    private WebDriverWait wait;

    @FindBy(name = "username")
    private WebElement usernameInput;

    @FindBy(name = "password")
    private WebElement passwordInput;

    @FindBy(css = "input[type='submit']")
    private WebElement signInButton;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(3));
        PageFactory.initElements(driver, this);
    }

    public void login(String username, String password) {
        wait.until(ExpectedConditions.visibilityOf(usernameInput));
        usernameInput.sendKeys(username);
        passwordInput.sendKeys(password);
        signInButton.click();
    }

    public boolean isAtLoginPage() {
        return driver.getCurrentUrl().contains("/login");
    }
}
```

`HelloPage.java`:
```java
package com.example.securingweb.selenium.pages;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;

public class HelloPage {
    private WebDriver driver;
    private WebDriverWait wait;

    @FindBy(tagName = "h1")
    private WebElement greeting;

    @FindBy(css = "input[value='Sign Out']")
    private WebElement signOutButton;

    public HelloPage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(3));
        PageFactory.initElements(driver, this);
    }

    public String getGreeting() {
        wait.until(ExpectedConditions.visibilityOf(greeting));
        return greeting.getText();
    }

    public boolean isAtHelloPage() {
        return driver.getCurrentUrl().contains("/hello");
    }
}
```

### 3. Implement Happy Path Test

`AuthenticationWorkflowTest.java`:
```java
package com.example.securingweb.selenium.tests;

import com.example.securingweb.selenium.pages.*;
import org.junit.jupiter.api.*;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import java.time.Duration;

import static org.junit.jupiter.api.Assertions.*;

public class AuthenticationWorkflowTest {
    private WebDriver driver;
    private HomePage homePage;
    private LoginPage loginPage;
    private HelloPage helloPage;

    @BeforeEach
    public void setup() {
        driver = new ChromeDriver();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(3));
        
        homePage = new HomePage(driver);
        loginPage = new LoginPage(driver);
        helloPage = new HelloPage(driver);
    }

    @Test
    @DisplayName("User can access protected content after login")
    public void testSuccessfulLogin() {
        // Given: user is on the home page
        homePage.navigateTo();

        // When: user clicks on protected content link
        homePage.clickHelloLink();

        // Then: user is redirected to login page
        assertTrue(loginPage.isAtLoginPage(), "Should be redirected to login page");

        // When: user enters valid credentials
        loginPage.login("user", "password");

        // Then: user should see protected content
        assertTrue(helloPage.isAtHelloPage(), "Should be on hello page");
        
        // And: user name should be displayed correctly
        String greeting = helloPage.getGreeting();
        assertTrue(greeting.contains("Hello user!"), 
            "Greeting should contain username");
    }

    @AfterEach
    public void teardown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

### 4. Run the Test

1. Start your Spring Boot application:
```bash
./gradlew bootRun
```

2. In a new terminal, run the test:
```bash
./gradlew test --tests AuthenticationWorkflowTest
```

### Expected Results
* Browser opens automatically
* Navigates through the authentication workflow
* Verifies correct page transitions
* Validates user content
* Closes browser

### Understanding the Implementation

1. **Page Objects Pattern**
   * Each page has its own class
   * Encapsulates page elements and behaviors
   * Makes tests more maintainable

2. **Wait Strategies**
   * Implicit wait of 3 seconds (meets performance requirement)
   * Explicit waits for critical elements

3. **Assertions**
   * Verifies correct page navigation
   * Validates user content
   * Checks response times
