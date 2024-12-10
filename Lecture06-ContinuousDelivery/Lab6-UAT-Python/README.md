# Lab 5b: BDD Testing with Behave (Python Version)

## Introduction
This lab implements the same BDD testing concepts as the Java/Cucumber lab, but using Python and Behave. The fundamental concepts remain the same:
- Behavior-Driven Development (BDD)
- Natural language test specifications
- Automated acceptance testing

### Java vs Python Comparison
```
Cucumber (Java)         | Behave (Python)
---------------------- | ----------------------
build.gradle           | requirements.txt
src/test/resources     | features/
@Given, @When, @Then   | @given, @when, @then
JUnit assertions       | Python assertions
```

## Prerequisites
✅ Required before starting:
* Python 3.8 or higher installed
* pip (Python package manager)
* VS Code or similar IDE
* Basic understanding of Python

## Part 1: Project Setup (Windows)

### Create Project Structure
```powershell
# Create project directory
mkdir python-calculator-uat
cd python-calculator-uat

# Create directories
mkdir calculator
mkdir features
mkdir features\steps

# Create empty files (Windows method)
echo. > calculator\__init__.py
echo. > calculator\calc.py
echo. > features\calculator.feature
echo. > features\steps\calculator_steps.py
echo. > requirements.txt
```

### Project Structure
After creation, you should see:
```
python-calculator-uat\
├── calculator\              # Our application code
│   ├── __init__.py         # Makes it a Python package
│   └── calc.py             # Calculator implementation
├── features\               # Behave test directory
│   ├── calculator.feature  # Feature definitions
│   └── steps\             # Step implementations
│       └── calculator_steps.py
└── requirements.txt        # Project dependencies
```

### Setup Dependencies
Create `requirements.txt` with this content:
```
behave==1.2.6
```

Install dependencies using:
```powershell
pip install -r requirements.txt
```

## Part 2: Implementing the Calculator

### Create Calculator Class
In `calculator/calc.py`, add this code:

```python
class Calculator:
    def add(self, a, b):
        return a + b

    def subtract(self, a, b):
        return a - b

    def multiply(self, a, b):
        return a * b

    def divide(self, a, b):
        if b == 0:
            raise ValueError("Division by zero")
        return a / b

    def run(self):
        """Interactive calculator menu"""
        while True:
            print("\nSimple Calculator")
            print("1. Add")
            print("2. Subtract")
            print("3. Multiply")
            print("4. Divide")
            print("5. Exit")
            
            try:
                choice = int(input("Choose operation: "))
                
                if choice == 5:
                    break
                
                if choice < 1 or choice > 4:
                    print("Invalid choice")
                    continue
                
                a = float(input("Enter first number: "))
                b = float(input("Enter second number: "))
                
                if choice == 1:
                    print("Result:", self.add(a, b))
                elif choice == 2:
                    print("Result:", self.subtract(a, b))
                elif choice == 3:
                    print("Result:", self.multiply(a, b))
                elif choice == 4:
                    try:
                        print("Result:", self.divide(a, b))
                    except ValueError as e:
                        print("Error:", str(e))
                
            except ValueError:
                print("Please enter valid numbers")

# Allow running calculator directly
if __name__ == "__main__":
    calc = Calculator()
    calc.run()
```

### Test the Calculator
You can test the calculator manually:

```powershell
# From the python-calculator-uat directory
python -m calculator.calc
```

## Part 3: Writing BDD Tests

### Create Feature File
In `features/calculator.feature`, add:

```gherkin
Feature: Calculator Operations
    As a user
    I want to perform basic calculations
    So that I can get accurate results

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

    Scenario: Division by zero
        Given I have a calculator
        When I divide 10 by 0
        Then it should raise a ValueError
```

### Create Step Definitions
In `features/steps/calculator_steps.py`, add:

```python
from behave import given, when, then
from calculator.calc import Calculator

@given('I have a calculator')
def step_impl(context):
    context.calculator = Calculator()
    context.result = None
    context.error = None

@when('I add {x:d} and {y:d}')
def step_impl(context, x, y):
    context.result = context.calculator.add(x, y)

@when('I subtract {x:d} from {y:d}')
def step_impl(context, x, y):
    context.result = context.calculator.subtract(y, x)

@when('I multiply {x:d} and {y:d}')
def step_impl(context, x, y):
    context.result = context.calculator.multiply(x, y)

@when('I divide {x:d} by {y:d}')
def step_impl(context, x, y):
    try:
        context.result = context.calculator.divide(x, y)
    except ValueError as e:
        context.error = e

@then('the result should be {expected:d}')
def step_impl(context, expected):
    assert context.result == expected, f"Expected {expected}, but got {context.result}"

@then('it should raise a ValueError')
def step_impl(context):
    assert isinstance(context.error, ValueError), "Expected ValueError was not raised"
```

### Running the Tests

```powershell
# From the python-calculator-uat directory
python -m behave
```

You should see output like:
```
Feature: Calculator Operations # features/calculator.feature:1
  Scenario: Adding two numbers               # features/calculator.feature:6
    Given I have a calculator               # features/steps/calculator_steps.py:4
    When I add 5 and 3                      # features/steps/calculator_steps.py:10
    Then the result should be 8             # features/steps/calculator_steps.py:30
  ...
5 scenarios passed, 0 failed, 0 skipped
15 steps passed, 0 failed, 0 skipped
```
