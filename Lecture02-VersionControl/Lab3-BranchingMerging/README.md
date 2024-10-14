# Lab 3: Branching and Merging

## Introduction
This lab will introduce you to branching and merging in Git, as well as creating and managing pull requests on GitHub. These are crucial skills for collaborative development and maintaining clean, organized codebases.

## Prerequisites

- VS Code with Python extension installed
- Git for Windows installed
- GitHub account
- Completion of Lab 1 and Lab 2

Ensure you have these tools set up and have completed the previous labs before starting.

## Step 1: Creating and Switching Branches

Branches allow you to develop features isolated from each other. This is useful for working on different aspects of a project simultaneously.

1. Open your "PythonGitLab" project in VS Code
2. Open the integrated terminal (View > Terminal)
3. Create a new branch named "feature-calculator":
   ```
   git checkout -b feature-calculator
   ```
   This command creates a new branch and switches to it.
4. Verify you're on the new branch:
   ```
   git branch
   ```
   The current branch will be marked with an asterisk (*).
5. In VS Code, create a new Python file named "calculator.py"
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
8. Switch back to the main branch:
   ```
   git checkout main
   ```
9. Note that calculator.py is no longer visible in VS Code's file explorer. This is because the file only exists in the feature-calculator branch.

## Step 2: Merging Branches

Merging allows you to incorporate changes from one branch into another.

1. Merge the feature-calculator branch into main:
   ```
   git merge feature-calculator
   ```
2. Push the changes to GitHub:
   ```
   git push
   ```

## Step 3: Creating a Pull Request on GitHub

Pull requests are a way to propose changes to a repository. They're especially useful for code review and managing contributions from multiple developers.

1. Create a new branch for another feature:
   ```
   git checkout -b feature-multiply
   ```
2. In VS Code, open calculator.py and add a multiply function:
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

Code review is a crucial part of collaborative development. Pull requests facilitate this process.

1. On the pull request page, click on the "Files changed" tab to review the changes
2. Add a comment to the pull request (e.g., "Looks good, but let's add a divide function too")
3. Go back to VS Code
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

## Step 5: Updating Local Main Branch

After merging a pull request on GitHub, you need to update your local repository.

1. In VS Code's terminal, switch to the main branch:
   ```
   git checkout main
   ```
2. Pull the latest changes:
   ```
   git pull
   ```
3. Verify that calculator.py now includes all functions (add, subtract, multiply, divide)

You can also use VS Code's Source Control view to visualize branches, stage changes, and commit. The Git Graph extension for VS Code can provide a visual representation of your branch structure and merge history.

By completing this lab, you've learned how to create and manage branches, merge changes, create and review pull requests, and keep your local repository in sync with the remote. These are fundamental skills for collaborative development using Git and GitHub.
