# Lab 1: Setting up a Basic CI Pipeline with GitHub and AWS

## Objective
Create a simple Python application, set up a GitHub repository, and configure a basic CI pipeline that syncs changes from GitHub to AWS CodeCommit and performs a simple build operation.

## Prerequisites
- GitHub account
- AWS account
- AWS CLI installed and configured
- Git installed locally

## Note for Azure Lab Completers
If you have already completed the Azure version of this lab, you can skip steps 1 and 2. Your GitHub repository is already set up with the initial Python application. You can proceed directly to step 3 to set up the AWS CodeCommit repository and continue from there. Note that you'll be using the same GitHub repository for both Azure and AWS integrations.

## Steps

### 1. Create a Simple Python Application

a. Create a new directory on your local machine:
```bash
mkdir hello-world-python
```

b. Navigate to the directory:
```bash
cd hello-world-python
```

c. Create a file named `app.py` with the following content:
```python
def hello():
    return "Hello, World!"

if __name__ == "__main__":
    print(hello())
```

d. Create a file named `requirements.txt` (leave it empty for now)

### 2. Set up a GitHub Repository

a. Go to GitHub and create a new repository named "hello-world-python"

b. Initialize the local repository:
```bash
git init
git add .
git commit -m "Initial commit"
```

c. Connect your local repo to GitHub:
```bash
git remote add origin https://github.com/your-username/hello-world-python.git
git branch -M main
git push -u origin main
```

### 3. Set up AWS CodeCommit Repository

a. Open the AWS Console and navigate to CodeCommit
b. Click "Create repository"
c. Name it "hello-world-python" and create
d. Note down the HTTPS clone URL

### 4. Configure AWS CodeBuild
Note: We will discuss the build process in more details during the next lecture

a. In the AWS Console, navigate to CodeBuild
b. Click "Create build project"
c. Set the following options:
   - Project name: "hello-world-python-build"
   - Source provider: GitHub
   - Connect to your GitHub account and select the repository
   - Environment: Managed image, Amazon Linux 2, Standard runtime
   - Service role: Create a new service role
   - Buildspec: Insert build commands
d. In the build commands, enter:
```yaml
version: 0.2
phases:
  install:
    runtime-versions:
      python: 3.8
  build:
    commands:
      - echo "Building the Python application"
      - python -m compileall .
```
e. Create the build project

### 5. Set up AWS CodePipeline

a. In the AWS Console, navigate to CodePipeline
b. Click "Create pipeline"
c. Set the following options:
   - Pipeline name: "hello-world-python-pipeline"
   - Service role: New service role
   - Source provider: GitHub (Version 2)
   - Connect to your GitHub account and select the repository and branch
   - Detection option: GitHub webhooks
   - Build provider: AWS CodeBuild
   - Select the build project you created earlier
   - Skip the deploy stage for now
d. Review and create the pipeline

### 6. Test the CI Pipeline

a. Make a change to your `app.py` file locally, e.g.:
```python
def hello():
    return "Hello, CI World!"
```

b. Commit and push the change:
```bash
git add app.py
git commit -m "Update hello message"
git push origin main
```

c. Go to AWS CodePipeline and watch your pipeline run automatically
d. Check the CodeBuild logs to see the build process

### 7. Verify CodeCommit Sync

a. Go to AWS CodeCommit and check your repository
b. Verify that the changes you pushed to GitHub are now in CodeCommit

## Conclusion

You have now set up a basic CI pipeline that automatically syncs code from GitHub to AWS CodeCommit and runs a simple build process whenever changes are pushed to the main branch. This forms the foundation for more complex CI/CD workflows.
