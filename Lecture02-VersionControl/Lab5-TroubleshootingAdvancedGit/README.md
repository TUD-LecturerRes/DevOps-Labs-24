# Lab 5: Troubleshooting and Advanced Git Operations

## Prerequisites

- Visual Studio with Python support installed
- Git for Windows installed
- Python project from previous labs

## 1. Reverting Commits

a. Make a commit with a change:
   - Open your main Python file in Visual Studio
   - Add a new function or modify an existing one
   - Commit the change:
     ```
     git add .
     git commit -m "Added new feature X"
     ```

b. Revert that commit:
   - In the Visual Studio terminal, run:
     ```
     git revert HEAD
     ```
   - This will open your default editor. Save and close to complete the revert.

c. Demonstrate the difference between revert and reset:
   - Make another commit with a different change
   - Use reset to undo it:
     ```
     git reset --hard HEAD^
     ```
   - Note: This removes the commit from history, unlike revert which adds a new commit to undo changes.

## 2. Cherry-picking

a. Create a new branch and make some commits:
   ```
   git checkout -b feature-branch
   ```
   - Make a few changes and commits on this branch

b. Switch back to master:
   ```
   git checkout master
   ```

c. Cherry-pick a specific commit:
   - Find the hash of a commit you want to cherry-pick
   ```
   git cherry-pick <commit-hash>
   ```

## 3. Stashing Changes

a. Make some changes in your working directory:
   - Modify a Python file but don't commit

b. Stash the changes:
   ```
   git stash
   ```

c. Switch to another branch and back:
   ```
   git checkout -b temp-branch
   git checkout master
   ```

d. Apply the stash:
   ```
   git stash pop
   ```

## 4. Bisecting

a. Introduce a bug in the calculator function:
   - Open your calculator.py file
   - Introduce a subtle bug in one of the functions
   - Make several more commits with other changes

b. Start the bisect process:
   ```
   git bisect start
   git bisect bad  # current commit is bad
   git bisect good <known-good-commit>
   ```

c. Follow the bisect process:
   - Git will checkout different commits
   - Test your code at each step
   - Mark each commit as 'good' or 'bad' using:
     ```
     git bisect good
     ```
     or
     ```
     git bisect bad
     ```
   - Continue until Git identifies the bug-introducing commit

d. End the bisect process:
   ```
   git bisect reset
   ```

## 5. Submodules

a. Create a new repository on GitHub for a submodule

b. Add the submodule to your main project:
   - In the Visual Studio terminal, run:
     ```
     git submodule add <submodule-repo-url>
     ```

c. Commit the changes:
   ```
   git add .
   git commit -m "Added submodule"
   ```

d. Clone the main repository and initialize submodules:
   - Open a new instance of Visual Studio
   - Clone your main repository
   - In the terminal of the new instance:
     ```
     git submodule init
     git submodule update
     ```

e. Update the submodule:
   - Navigate to the submodule directory in File Explorer
   - Make a change in the submodule
   - Commit and push the change in the submodule
   - In your main project's Visual Studio terminal:
     ```
     cd <submodule-directory>
     git pull origin master
     cd ..
     git add <submodule-directory>
     git commit -m "Update submodule"
     ```
