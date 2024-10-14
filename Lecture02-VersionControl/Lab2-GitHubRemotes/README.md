# Lab 2: Remote Repositories with GitHub

## Introduction
This lab will introduce you to working with remote repositories using GitHub. You'll learn how to connect your local Git repository to GitHub, push and pull changes, and handle merge conflicts. These skills are crucial for collaborating on projects and maintaining code in a distributed environment.

## Prerequisites

- VS Code with Python extension installed
- Git for Windows installed
- GitHub account

Ensure you have these tools and accounts set up before starting the lab.

## Step 1: Setting up GitHub

GitHub is a popular platform for hosting Git repositories. In this step, you'll create a new repository on GitHub.

1. Log in to your GitHub account (or create one if you haven't already)
2. Click the '+' icon in the top-right corner and select "New repository"
3. Name your repository "PythonGitLab"
4. Choose "Private" for repository visibility (you can change this later if needed)
5. Do not initialize the repository with a README, .gitignore, or license (we'll push our existing local repository)
6. Click "Create repository"

This creates an empty repository on GitHub that we'll connect to our local repository.

## Step 2: Connecting Local and Remote Repositories

Now you'll link your local repository to the GitHub repository you just created.

1. Open VS Code
2. Open your "GitBasicsLab" folder from Lab 1 (File > Open Folder)
3. Open the integrated terminal in VS Code (View > Terminal)
4. Add the GitHub repository as a remote:
   ```
   git remote add origin https://github.com/yourusername/PythonGitLab.git
   ```
   Replace 'yourusername' with your actual GitHub username. This command links your local repository to the GitHub repository.
5. Verify the remote was added:
   ```
   git remote -v
   ```
   This shows you the remote repositories connected to your local repository.
6. Push your local repository to GitHub:
   ```
   git push -u origin main
   ```
   This uploads your local commits to GitHub. The `-u` flag sets up tracking, so in future you can simply use `git push`.
   
   Note: If you encounter a credentials error, it's likely due to authentication issues

8. Refresh your GitHub page to see your code now on GitHub

## Step 3: Making and Pushing Changes

In this step, you'll make changes locally and push them to GitHub.

1. In VS Code, open "GitBasicsLab.py"
2. Add a new function to the file:
   ```python
   def greet(name):
       return f"Hello, {name}! Welcome to Git and GitHub."

   print(greet("Student"))
   ```
3. Save the file
4. Stage the changes:
   ```
   git add GitBasicsLab.py
   ```
5. Commit the changes:
   ```
   git commit -m "Add greet function"
   ```
6. Push the changes to GitHub:
   ```
   git push
   ```
7. Refresh your GitHub repository page to see the updated code

This workflow - edit, stage, commit, push - is the core of working with Git and GitHub.

## Step 4: Pulling Changes

Now you'll learn how to check for changes in the remote repository and pull them to your local repository.

1. On the GitHub website, navigate to your "PythonGitLab" repository
2. Click on the "GitBasicsLab.py" file
3. Click the edit (pencil) icon
4. Add a new line at the end of the file:
   ```python
   print("This line was added directly on GitHub.")
   ```
5. Scroll down, add a commit message like "Update GitBasicsLab.py via GitHub", and click "Commit changes"

6. In VS Code's terminal, fetch the latest changes from the remote repository without merging them:
   ```
   git fetch
   ```
   This command downloads the latest information from the remote repository but doesn't change your local files yet.

7. Check the difference between your local `main` branch and the remote `origin/main` branch:
   ```
   git diff main origin/main
   ```
   This will show you the changes that exist in the remote repository but not in your local repository. You should see the line you added on GitHub.

8. If you want to see a summary of what files have changed, you can use:
   ```
   git diff --stat main origin/main
   ```
   This gives you a more concise overview of the changes.

9. Now that you've reviewed the changes, pull them into your local repository:
   ```
   git pull
   ```
   This downloads and merges the changes from GitHub into your local repository.

10. Open "GitBasicsLab.py" in VS Code to verify that the changes you made on GitHub are now in your local file.

## Step 5: Handling Conflicts (Simulation)

Merge conflicts occur when Git can't automatically merge changes. This step simulates a conflict and shows you how to resolve it using both the command line and VS Code's visual interface.

1. In VS Code, modify "GitBasicsLab.py" by adding a line:
   ```python
   print("This line was added locally.")
   ```
2. Save the file, stage, and commit:
   ```
   git add GitBasicsLab.py
   git commit -m "Add local change"
   ```
3. On GitHub, edit the same file again, adding a different line at the end:
   ```python
   print("Another line added on GitHub.")
   ```
4. Commit this change on GitHub
5. In VS Code's terminal, try to pull:
   ```
   git pull
   ```
   This should result in a merge conflict

6. VS Code will detect the conflict and show it in the Source Control view:
   - Open the Source Control view by clicking on the branching icon in the left sidebar
   - You'll see the conflicting file (GitBasicsLab.py) marked with a '!' icon

7. Open "GitBasicsLab.py". VS Code will show the conflicts with special markers:
   ```
   <<<<<<< HEAD
   print("This line was added locally.")
   =======
   print("Another line added on GitHub.")
   >>>>>>> [commit hash]
   ```

8. VS Code provides several options to resolve the conflict:
   - You'll see "Accept Current Change", "Accept Incoming Change", "Accept Both Changes", and "Compare Changes" options above the conflict
   - Click on "Accept Both Changes" to keep both lines

9. After resolving the conflict, the file will look like this:
   ```python
   print("This line was added locally.")
   print("Another line added on GitHub.")
   ```
10. Save the file

11. Return to the command line to complete the process. In VS Code's integrated terminal:

    Stage the resolved file:
    ```
    git add GitBasicsLab.py
    ```

    Commit the changes:
    ```
    git commit -m "Resolve merge conflict"
    ```

    Push the changes to GitHub:
    ```
    git push
    ```

12. Refresh your GitHub repository page to confirm that the changes have been successfully pushed.

By completing this lab, you've learned how to work with remote repositories, push and pull changes, and handle basic merge conflicts. These skills form the foundation of collaborative development using Git and GitHub.
