# Lab 2: Implementing Branch Protections and CI Checks (Azure Version)

## Objective
Set up branch protection rules in GitHub and configure CI checks to enforce code quality before merging, using Azure DevOps services for CI/CD.

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

c. Under "Branch protection rules", click "Add classic branch protection rule"

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

### 5. Test the CI Pipeline

a. Create a pull request from `development` to `main` in GitHub:
   - Go to your GitHub repository
   - Click on the "Pull requests" tab
   - Click "New pull request"
   - Set "base" to "main" and "compare" to "development"
   - Review the changes, then click "Create pull request"
   - Add a title and description, then click "Create pull request" again

b. Observe the pipeline run in Azure DevOps and check results

c. If there are linting errors, fix them in your local development branch, commit, and push again

d. Once all checks pass, complete the pull request in GitHub:
   - Click on "Merge pull request"
   - Then click "Confirm merge"

e. After merging, observe that a new pipeline run is triggered in Azure DevOps:
   - This run will be for the main branch, incorporating the newly merged changes
   - Wait for this pipeline to complete successfully

Note: The second pipeline run (on the main branch) occurs because we set up the trigger for both 'main' and 'development' branches in our azure-pipelines.yml file. This ensures that our code is tested again after merging, which is a good practice to catch any potential issues that might arise from the merge process.b

## Note on Branch Protection in Azure DevOps

While this lab focuses on GitHub for repository management and branch protection, it's worth noting that Azure DevOps also offers branch protection policies for Azure Repos. In a project that primarily uses Azure DevOps for both source control and CI/CD, you would configure these policies in the Azure DevOps project settings. However, for this lab, we're using the GitHub branch protection features to keep our source control management consistent.

## Conclusion

You have now set up branch protection rules in GitHub and implemented basic code quality checks in your CI pipeline using Azure DevOps services. This ensures that all code merged into the main branch passes automated checks and has been reviewed, improving overall code quality and collaboration in your development process.
