# Lab 2: Remote Repositories with GitHub

## Prerequisites

- Visual Studio with Python support installed
- Git for Windows installed
- GitHub account

## Step 1: Setting up GitHub

1. Log in to your GitHub account (or create one if you haven't already)
2. Click the '+' icon in the top-right corner and select "New repository"
3. Name your repository "PythonGitLab"
4. Choose "Private" for repository visibility
5. Do not initialize the repository with a README, .gitignore, or license
6. Click "Create repository"

## Step 2: Connecting Local and Remote Repositories

1. Open Visual Studio
2. Go to "File" > "Open" > "Project/Solution" and open your "GitBasicsLab" from Lab 1
3. Open the integrated terminal in Visual Studio (View > Terminal)
4. Add the GitHub repository as a remote:
   ```
   git remote add origin https://github.com/yourusername/PythonGitLab.git
   ```
   Replace 'yourusername' with your actual GitHub username
5. Verify the remote was added:
   ```
   git remote -v
   ```
6. Push your local repository to GitHub:
   ```
   git push -u origin master
   ```
   You may be prompted to log in to your GitHub account
7. Refresh your GitHub page to see your code now on GitHub

## Step 3: Making and Pushing Changes

1. In Visual Studio, open "GitBasicsLab.py"
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

## Step 4: Pulling Changes

1. On the GitHub website, navigate to your "PythonGitLab" repository
2. Click on the "GitBasicsLab.py" file
3. Click the edit (pencil) icon
4. Add a new line at the end of the file:
   ```python
   print("This line was added directly on GitHub.")
   ```
5. Scroll down, add a commit message like "Update GitBasicsLab.py via GitHub", and click "Commit changes"
6. In Visual Studio's terminal, pull the changes:
   ```
   git pull
   ```
7. Open "GitBasicsLab.py" in Visual Studio to see the changes you made on GitHub now in your local file

## Step 5: Handling Conflicts (Simulation)

1. In Visual Studio, modify "GitBasicsLab.py" by adding a line:
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
5. In Visual Studio's terminal, try to pull:
   ```
   git pull
   ```
   This should result in a merge conflict
6. Open "GitBasicsLab.py" in Visual Studio, you'll see conflict markers. Resolve the conflict by keeping both lines:
   ```python
   print("This line was added locally.")
   print("Another line added on GitHub.")
   ```
7. Stage the resolved file, commit, and push:
   ```
   git add GitBasicsLab.py
   git commit -m "Resolve merge conflict"
   git push
   ```
