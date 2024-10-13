# Lab 1: Setting up a Basic CI Pipeline with GitHub and Azure DevOps

## Objective
Create a simple Python application, set up a GitHub repository, and configure a basic CI pipeline that syncs changes from GitHub to Azure Repos and performs a simple build operation.

## Prerequisites
- GitHub account
- Azure DevOps account
- Git installed locally

## Note for AWS Lab Completers
If you have already completed the AWS version of this lab, you can skip steps 1 and 2. Your GitHub repository is already set up with the initial Python application. You can proceed directly to step 3 to set up the Azure DevOps project and continue from there.

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

### 3. Set up Azure DevOps Project

a. Go to dev.azure.com and sign in
b. Create a new project named "hello-world-python"
c. Navigate to Repos and note down the clone URL for the Azure Repo

### 4. Configure Azure Pipelines

a. In your Azure DevOps project, go to Pipelines
b. Click "Create Pipeline"
c. Choose "GitHub" as the location of your code
d. Select your "hello-world-python" repository
e. Choose "Starter pipeline"
f. Replace the content of the azure-pipelines.yml with:

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: UsePythonVersion@0
  inputs:
    versionSpec: '3.8'
    addToPath: true

- script: |
    echo "Building the Python application"
    python -m compileall .
  displayName: 'Build Python App'

- task: CopyFiles@2
  inputs:
    contents: '**/*.py'
    targetFolder: '$(build.artifactStagingDirectory)'

- task: PublishBuildArtifacts@1
  inputs:
    pathToPublish: '$(Build.ArtifactStagingDirectory)'
    artifactName: 'drop'
```

g. Click "Save and run"

### 5. Set up Azure Repos Sync

a. In your Azure DevOps project, go to Project Settings
b. Navigate to Repos > Repositories
c. Click on "Import a repository"
d. Choose "Import" and enter your GitHub repository URL
e. Click "Import" to start the sync

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

c. Go to Azure Pipelines and watch your pipeline run automatically
d. Check the build logs to see the build process

### 7. Verify Azure Repos Sync

a. Go to Azure Repos in your project
b. Verify that the changes you pushed to GitHub are now in Azure Repos

## Conclusion

You have now set up a basic CI pipeline that automatically syncs code from GitHub to Azure Repos and runs a simple build process whenever changes are pushed to the main branch. This forms the foundation for more complex CI/CD workflows in Azure DevOps.
