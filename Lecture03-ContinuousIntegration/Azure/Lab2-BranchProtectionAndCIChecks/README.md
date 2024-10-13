# Lab 2: Implementing Branch Protections and CI Checks (Azure Version)

## Objective
Set up branch protection rules in GitHub and configure CI checks to enforce code quality before merging, using Azure DevOps services.

## Prerequisites
- Completed Lab 1 (Azure version)
- GitHub account with owner or admin rights to the repository
- Azure DevOps account

## Note for AWS Lab Completers
If you have already completed the AWS version of this lab, you can skip steps 1, 2, and 3. Your GitHub repository is already set up with the branch protection rules and the modified application. You can proceed directly to step 4 to update the Azure Pipelines configuration.

## Steps

### 1. Create a Development Branch

a. In your local repository, create and switch to a new branch:
```bash
git checkout -b development
```

b. Push the new branch to GitHub:
```bash
git push -u origin development
```

### 2. Set Up Branch Protection Rules in GitHub

a. Go to your GitHub repository

b. Click on "Settings" > "Branches"

c. Under "Branch protection rules", click "Add rule"

d. For "Branch name pattern", enter `main`

e. Check the following options:
   - "Require pull request reviews before merging"
   - "Require status checks to pass before merging"

f. Click "Create" to save the rule

### 3. Modify the Application for Testing

a. In your local `development` branch, modify `app.py`:
```python
def hello():
    return "Hello, CI World!"

def add(a, b):
    return a + b

if __name__ == "__main__":
    print(hello())
    print(f"1 + 2 = {add(1, 2)}")
```

b. Commit and push the changes:
```bash
git add app.py
git commit -m "Add add function for testing"
git push origin development
```

### 4. Update Azure Pipelines Configuration

a. In your Azure DevOps project, go to Pipelines

b. Edit your existing pipeline

c. Update the `azure-pipelines.yml` file:
```yaml
trigger:
- main
- development

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: UsePythonVersion@0
  inputs:
    versionSpec: '3.8'
    addToPath: true

- script: |
    pip install pylint
    pylint **/*.py
  displayName: 'Run pylint'

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

### 5. Configure Branch Policies in Azure Repos

a. Go to Azure Repos in your project

b. Click on "Branches"

c. Find the `main` branch and click on the three dots (...), then "Branch policies"

d. Enable "Require a minimum number of reviewers"

e. Under "Build validation", add your pipeline and select "Require"

### 6. Test the CI Pipeline

a. Create a pull request from `development` to `main` in Azure Repos

b. Observe the pipeline run and check results

c. If there are linting errors, fix them and push again

d. Once all checks pass, complete the pull request

## Conclusion

You have now set up branch protection rules and implemented basic code quality checks in your CI pipeline using Azure DevOps services. This ensures that all code merged into the main branch passes automated checks and has been reviewed, improving overall code quality and collaboration in your development process.
