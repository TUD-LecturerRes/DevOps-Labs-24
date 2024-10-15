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

Tags in Git are used to mark specific points in history, typically for release versions. Let's create two different commits and tag them separately, then demonstrate how to pull code based on a tag.

a. Make two separate commits:
   1. Open your GitBasicsLab.py file
   2. Add a new function:
      ```python
      def feature_a():
          return "This is feature A"
      ```
   3. Save the file and commit:
      ```
      git add GitBasicsLab.py
      git commit -m "Add feature A"
      ```
   4. Now, add another function:
      ```python
      def feature_b():
          return "This is feature B"
      ```
   5. Save and commit again:
      ```
      git add GitBasicsLab.py
      git commit -m "Add feature B"
      ```

b. Create tags for each commit:
   1. Tag the first commit:
      ```
      git tag -a v1.0 HEAD^ -m "Version 1.0 with feature A"
      ```
   2. Tag the second commit:
      ```
      git tag -a v1.1 HEAD -m "Version 1.1 with feature B"
      ```

c. Push the tags to the remote repository:
   ```
   git push origin --tags
   ```

d. Pulling code based on a tag:
   1. First, let's simulate working on a different machine by creating a new directory:
      ```
      mkdir test_tag_pull
      cd test_tag_pull
      ```
   2. Clone your repository:
      ```
      git clone <your-repo-url>
      ```
   3. Now, let's checkout the code at v1.0:
      ```
      git checkout v1.0
      ```
   4. You can verify that only feature A is present in the code at this point.

e. Switching between tagged versions:
   - To switch to v1.1:
     ```
     git checkout v1.1
     ```
   - You should now see both feature A and feature B in the code.

## 3. Interactive Rebasing

Let's go through the process of making three small changes and then using interactive rebase to clean up the commit history.

a. Switch back to the main repository:
   If you're still in the test_tag_pull directory from the previous exercise, navigate back to your main project directory:
   ```
   cd ../
   ```
   Ensure you're on the main branch:
   ```
   git checkout main
   ```

b. Handle the test_tag_pull directory:
   You have two options:

   Option 1: Add to .gitignore
   - Open your .gitignore file and add:
     ```
     test_tag_pull/
     ```
   - Commit this change:
     ```
     git add .gitignore
     git commit -m "Add test_tag_pull to .gitignore"
     ```

   Option 2: Delete the directory

c. Make three small changes to your Python files:
   1. Open GitBasicsLab.py and add a new function:
      ```python
      def helper_function_1():
          return "I'm helping!"
      ```
      Commit this change:
      ```
      git add GitBasicsLab.py
      git commit -m "Add helper function 1"
      ```

   2. Add another function:
      ```python
      def helper_function_2():
          return "I'm also helping!"
      ```
      Commit this change:
      ```
      git add GitBasicsLab.py
      git commit -m "Add helper function 2"
      ```

   3. Finally, modify an existing function (e.g., `feature_a`):
      ```python
      def feature_a():
          return "This is the improved feature A"
      ```
      Commit this change:
      ```
      git add GitBasicsLab.py
      git commit -m "Improve feature A"
      ```

d. Start an interactive rebase:
   ```
   git rebase -i HEAD~3
   ```

e. In the interactive mode:
   1. VS Code will open the rebase file. You'll see something like this:
      ```
      pick abc1234 Add helper function 1
      pick def5678 Add helper function 2
      pick ghi9012 Improve feature A
      ```
   2. Change it to:
      ```
      pick abc1234 Add helper function 1
      squash def5678 Add helper function 2
      squash ghi9012 Improve feature A
      ```
   3. Save and close the file.
   4. In the next file that opens, update the commit message for the squashed commit. For example:
      ```
      Add helper functions and improve feature A

      - Added two helper functions
      - Improved the implementation of feature A
      ```

f. After the rebase is complete, force push the rebased history:
   ```
   git push --force
   ```

Remember, force pushing rewrites history, so be cautious when doing this on shared branches. It's generally safe on your own feature branches that haven't been merged yet.
