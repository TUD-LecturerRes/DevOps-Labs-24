# Lab 3: Branching and Merging

## Prerequisites

- Visual Studio with Python support installed
- Git for Windows installed
- GitHub account
- Completion of Lab 1 and Lab 2

## Step 1: Creating and Switching Branches

1. Open your "PythonGitLab" project in Visual Studio
2. Open the integrated terminal (View > Terminal)
3. Create a new branch named "feature-calculator":
   ```
   git checkout -b feature-calculator
   ```
4. Verify you're on the new branch:
   ```
   git branch
   ```
5. In Visual Studio, create a new Python file named "calculator.py"
6. Add the following code to calculator.py:
   ```python
   def add(a, b):
       return a + b

   def subtract(a, b):
       return a - b
   ```
7. Stage and commit the new file:
   ```
   git add calculator.py
   git commit -m "Add basic calculator functions"
   ```
8. Switch back to the master branch:
   ```
   git checkout master
   ```
9. Note that calculator.py is no longer visible in Visual Studio's Solution Explorer

## Step 2: Merging Branches

1. Merge the feature-calculator branch into master:
   ```
   git merge feature-calculator
   ```
2. Push the changes to GitHub:
   ```
   git push
   ```

## Step 3: Creating a Pull Request on GitHub

1. Create a new branch for another feature:
   ```
   git checkout -b feature-multiply
   ```
2. In Visual Studio, open calculator.py and add a multiply function:
   ```python
   def multiply(a, b):
       return a * b
   ```
3. Stage, commit, and push this new branch to GitHub:
   ```
   git add calculator.py
   git commit -m "Add multiply function"
   git push -u origin feature-multiply
   ```
4. Go to your GitHub repository in a web browser
5. You should see a prompt to create a pull request for your recently pushed branch. Click on "Compare & pull request"
6. Add a description for your pull request and click "Create pull request"

## Step 4: Reviewing and Merging a Pull Request

1. On the pull request page, click on the "Files changed" tab to review the changes
2. Add a comment to the pull request (e.g., "Looks good, but let's add a divide function too")
3. Go back to Visual Studio
4. Make sure you're on the feature-multiply branch:
   ```
   git checkout feature-multiply
   ```
5. Add a divide function to calculator.py:
   ```python
   def divide(a, b):
       if b != 0:
           return a / b
       else:
           return "Error: Division by zero"
   ```
6. Stage, commit, and push these changes:
   ```
   git add calculator.py
   git commit -m "Add divide function"
   git push
   ```
7. Go back to the pull request on GitHub and refresh the page
8. You should see the new commit with the divide function added
9. Click "Merge pull request" and then "Confirm merge"

## Step 5: Updating Local Master Branch

1. In Visual Studio's terminal, switch to the master branch:
   ```
   git checkout master
   ```
2. Pull the latest changes:
   ```
   git pull
   ```
3. Verify that calculator.py now includes all functions (add, subtract, multiply, divide)
