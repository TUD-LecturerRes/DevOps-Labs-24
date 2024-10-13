# Lab 4: Git Workflow and Best Practices

## Prerequisites

- Visual Studio with Python support installed
- Git for Windows installed
- Python project from previous labs

## 1. Using .gitignore

a. Create a .gitignore file:
   - In Visual Studio, right-click on your project in Solution Explorer
   - Select "Add" > "New Item"
   - Choose "Text File" and name it ".gitignore"

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

     # Visual Studio files
     .vs/
     *.suo
     *.ntvs*
     *.njsproj
     *.sln
     ```

c. Demonstrate how it prevents tracking:
   - Create a new Python file named "temp.py" with some content
   - Run the Python file to generate a .pyc file
   - In the Visual Studio terminal, run `git status`
   - Observe that temp.py is listed as untracked, but .pyc files are not visible

## 2. Working with Tags

a. Create a lightweight tag:
   - In the Visual Studio terminal, run:
     ```
     git tag v1.0
     ```

b. Create an annotated tag:
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

a. Make several small commits:
   - Make three small changes to your Python files, committing each separately

b. Start an interactive rebase:
   - In the Visual Studio terminal, run:
     ```
     git rebase -i HEAD~3
     ```

c. In the interactive mode:
   - Your default text editor will open. Change "pick" to "squash" for the second and third commits
   - Save and close the editor
   - In the next editor window, update the commit message for the squashed commit

d. Force push the rebased history:
   - Run:
     ```
     git push --force
     ```

## 4. Git Hooks

a. Install flake8:
   - In the Visual Studio terminal, run:
     ```
     pip install flake8
     ```

b. Create a pre-commit hook:
   - Navigate to your project's .git\hooks directory in File Explorer
   - Create a new file named "pre-commit" (no extension)
   - Open it in a text editor and add the following content:
     ```batch
     @echo off
     flake8 .
     if %errorlevel% neq 0 exit /b %errorlevel%
     ```

c. Make the hook executable:
   - Windows doesn't require changing file permissions like Unix systems

d. Demonstrate the hook:
   - Introduce a style error in one of your Python files (e.g., add an unnecessary semicolon)
   - Try to commit the change
   - Observe that the commit is prevented due to the flake8 error
   - Fix the error and try committing again
