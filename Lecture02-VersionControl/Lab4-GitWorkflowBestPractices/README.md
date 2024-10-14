# Lab 4: Git Workflow and Best Practices

## Introduction
This lab introduces advanced Git concepts and best practices that are crucial for efficient and organized software development. You'll learn about .gitignore files, tagging, interactive rebasing, and Git hooks.

## Prerequisites

- VS Code with Python extension installed
- Git for Windows installed
- Python project from previous labs

Ensure you have these tools set up and have completed the previous labs before starting.

## 1. Using .gitignore

A .gitignore file specifies intentionally untracked files that Git should ignore. This is useful for excluding build artifacts, temporary files, and sensitive information.

a. Create a .gitignore file:
   - In VS Code, right-click on your project in the File Explorer
   - Select "New File" and name it ".gitignore"

b. Add patterns to ignore Python-specific files:
   - Open .gitignore and add the following content:
     ```
     # Python files
     *.pyc
     __pycache__/
     *.pyo
     *.pyd
     .Python
     env/
     venv/
     *.log

     # VS Code files
     .vscode/

     # Other IDE files
     .idea/
     *.swp
     ```

c. Demonstrate how it prevents tracking:
c. Demonstrate how .gitignore prevents tracking:

1. Create a new Python file:
   - In VS Code, create a new file named "temp.py"
   - Add some simple Python code, for example:
     ```python
     print("This is a temporary file")
     
     def example_function():
         return "This function doesn't do much"
     ```
   - Save the file

2. Run the Python file to generate a .pyc file:
   - In the VS Code terminal, run:
     ```
     python temp.py
     ```
   - This will execute the script and also generate a compiled Python file (temp.pyc or a __pycache__ directory containing temp.cpython-XX.pyc, where XX is your Python version)

3. Create some other files that should be ignored:
   - Create a directory named "venv" (simulating a virtual environment)
   - Create an empty file named "logfile.log"

4. Check the Git status:
   - In the VS Code terminal, run:
     ```
     git status
     ```
   - Observe the output carefully

5. Analyze the results:
   - You should see two items listed under "Untracked files":
     1. ".gitignore": This is the new file we just created.
     2. "temp.py": This is the Python file we created, which isn't ignored.
   - The .gitignore file itself is not ignored by default, as it's an important part of the project configuration that should typically be tracked in version control.
   - You should NOT see any .pyc files, the __pycache__ directory, the venv directory, or the logfile.log file. These are all ignored thanks to the patterns in your .gitignore file.

6. Stage and commit the .gitignore file:
   - Run the following commands:
     ```
     git add .gitignore
     git commit -m "Add .gitignore file"
     ```
   - This adds the .gitignore file to your repository, ensuring that these ignore rules are shared with other collaborators.

7. Check the status again:
   - Run:
     ```
     git status
     ```

8. Understanding the benefits of .gitignore:
   - Take a moment to consider why we're ignoring certain files:
   
     a. Why do you think we don't want to track .pyc files?
     
     b. What issues might arise if we tracked log files in our repository?
     
     c. Why is it better to exclude the virtual environment (venv) from version control?

9. Experimenting with force-adding ignored files:
   - Sometimes, you might need to track a file that's usually ignored. Let's try this:
   
     a. Run the following command:
        ```
        git add -f logfile.log
        ```
        
     b. Now check the status with `git status`. What do you observe?
     
     c. Think about scenarios where force-adding might be necessary. Can you come up with an example?
     
     d. Remove the file from staging:
        ```
        git reset logfile.log
        ```
   - Important: Force-adding ignored files should be done cautiously. In your own words, explain why this might be risky in a real project.

## 2. Working with Tags

Tags are references to specific points in Git history. They are typically used to mark release points.

a. Create a lightweight tag:
   - In the VS Code terminal, run:
     ```
     git tag v1.0
     ```

b. Create an annotated tag (includes extra metadata):
   - Run:
     ```
     git tag -a v1.1 -m "Version 1.1 release"
     ```

c. Push tags to GitHub:
   - Run:
     ```
     git push origin --tags
     ```

d. Use tags to mark release points:
   - Go to your GitHub repository
   - Click on "Releases"
   - Click "Create a new release"
   - Choose the v1.1 tag
   - Add release notes and publish the release

## 3. Interactive Rebasing

Interactive rebasing allows you to modify commits in your branch history. This is useful for cleaning up your commit history before merging.

a. Make several small commits:
   - Make three small changes to your Python files, committing each separately

b. Start an interactive rebase:
   - In the VS Code terminal, run:
     ```
     git rebase -i HEAD~3
     ```

c. In the interactive mode:
   - VS Code will open the rebase file. Change "pick" to "squash" for the second and third commits
   - Save and close the file
   - In the next file that opens, update the commit message for the squashed commit

d. Force push the rebased history:
   - Run:
     ```
     git push --force
     ```
   Note: Be cautious with force push, especially on shared branches!

## 4. Git Hooks

Git hooks are scripts that Git executes before or after events such as commit, push, and receive. They're useful for enforcing standards in your project.

a. Install flake8 (a Python linter):
   - In the VS Code terminal, run:
     ```
     pip install flake8
     ```

b. Create a pre-commit hook:
   - Navigate to your project's .git/hooks directory
   - Create a new file named "pre-commit" (no extension)
   - Open it in VS Code and add the following content:
     ```bash
     #!/bin/sh
     flake8 .
     ```

c. Make the hook executable (for Windows):
   - In PowerShell, run:
     ```
     icacls .git\hooks\pre-commit /grant Everyone:RX
     ```

d. Demonstrate the hook:
   - Introduce a style error in one of your Python files (e.g., add an unnecessary semicolon)
   - Try to commit the change
   - Observe that the commit is prevented due to the flake8 error
   - Fix the error and try committing again

By completing this lab, you've learned about important Git features and best practices that will help you maintain a clean and efficient workflow in your projects. Understanding these concepts is crucial for professional software development and collaboration.
