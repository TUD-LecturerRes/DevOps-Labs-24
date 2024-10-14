# Lab 1: Git Basics with Python

## Introduction
This lab is designed to introduce you to the basics of Git version control system using Python. You'll learn how to set up Git, create and manage a local repository, and work with changes in your code. These skills are essential for any developer, as version control is a crucial part of modern software development practices.

## Prerequisites

- VS Code installed with Python extension
- Git for Windows installed

## Step 1: Git Setup and Configuration

In this step, you'll configure Git with your personal information. This is important because Git uses this information to identify who made each commit.

1. Open Command Prompt (cmd) as an administrator

2. Configure your Git username:
   ```
   git config --global user.name "Your Name"
   ```

3. Configure your Git email:
   ```
   git config --global user.email "youremail@example.com"
   ```

These settings are global, meaning they'll apply to all Git repositories on your computer unless overridden locally.

## Step 2: Creating and Managing a Local Repository

Now you'll create a new project and initialize a Git repository. This is typically the first step when starting a new project that you want to version control.

1. Open VS Code

2. Click on "File" > "Open Folder" and create a new folder named "GitBasicsLab"

3. Once the folder is open in VS Code, open the integrated terminal by going to "View" > "Terminal"

4. In the terminal, initialize a new Git repository:
   ```
   git init
   ```
   This creates a new Git repository in your project folder.

5. Create a new file named "README.md" in your project root:
   ```
   cmd /c "echo # Git Basics Lab > README.md"
   ```
   README files are important in projects as they provide information about the project.

6. Stage the new file:
   ```
   git add README.md
   ```
   This command tells Git to track the README.md file.

7. Commit the changes:
   ```
   git commit -m "Initial commit: Add README.md"
   ```
   This creates your first commit, saving the current state of your project.

8. View the commit history:
   ```
   git log
   ```
   This shows you a log of all commits in the repository.

## Step 3: Working with Changes

In this step, you'll learn how to make changes to your project, view those changes, and commit them.

1. In VS Code, create a new file named "GitBasicsLab.py"

2. Add the following Python code to the file:
   ```python
   print("Hello, Git!")
   ```

3. Save the file

4. In the terminal, check the status of your repository:
   ```
   git status
   ```
   This command shows you which files have been changed or are untracked.

5. Stage the new file:
   ```
   git add GitBasicsLab.py
   ```

6. Now, modify the "GitBasicsLab.py" file, changing the content to:
   ```python
   print("Hello, Git!")
   print("Learning about git diff.")
   ```

7. Save the file

8. Now use git diff to see the changes:
   ```
   git diff
   ```
   You should see the new line you added. This command shows you the differences between the working directory and the staging area.

9. Stage the changes:
   ```
   git add GitBasicsLab.py
   ```

10. Commit the changes:
    ```
    git commit -m "Create GitBasicsLab.py with initial content"
    ```

11. Make another change to "GitBasicsLab.py", adding a new line to print the current date:
    ```python
    import datetime

    print("Hello, Git!")
    print("Learning about git diff.")
    print(f"Today is {datetime.date.today()}")
    ```

12. Save the file

13. Use git diff again to see the new changes:
    ```
    git diff
    ```
    You should see the import statement and the new print line.

14. To practice reverting changes, use:
    ```
    git checkout -- GitBasicsLab.py
    ```
    This command discards the changes you just made, reverting the file to its last committed state.

15. Verify that the last change was reverted by opening "GitBasicsLab.py" in VS Code and by using git diff (which should now show no output as there are no unstaged changes)

By completing this lab, you've learned the basic Git workflow: making changes, staging them, committing them, and even reverting changes. These are fundamental skills that you'll use regularly in software development.
