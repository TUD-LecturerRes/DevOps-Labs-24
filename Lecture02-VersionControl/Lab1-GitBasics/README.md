# Lab 1: Git Basics with Python

## Prerequisites

- VS Code installed with Python extension
- Git for Windows installed

## Step 1: Git Setup and Configuration

1. Open Command Prompt (cmd) as an administrator

2. Configure your Git username:
   ```
   git config --global user.name "Your Name"
   ```

3. Configure your Git email:
   ```
   git config --global user.email "youremail@example.com"
   ```

## Step 2: Creating and Managing a Local Repository

1. Open VS Code

2. Click on "File" > "Open Folder" and create a new folder named "GitBasicsLab"

3. Once the folder is open in VS Code, open the integrated terminal by going to "View" > "Terminal"

4. In the terminal, initialize a new Git repository:
   ```
   git init
   ```

5. Create a new file named "README.md" in your project root:
   ```
   cmd /c "echo # Git Basics Lab > README.md"
   ```

6. Stage the new file:
   ```
   git add README.md
   ```

7. Commit the changes:
   ```
   git commit -m "Initial commit: Add README.md"
   ```

8. View the commit history:
   ```
   git log
   ```

## Step 3: Working with Changes

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
   You should see the new line you added.

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

15. Verify that the last change was reverted by opening "GitBasicsLab.py" in VS Code and by using git diff (which should now show no output as there are no unstaged changes)
