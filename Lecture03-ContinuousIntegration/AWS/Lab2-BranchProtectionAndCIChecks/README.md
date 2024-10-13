# Lab 2: Implementing Branch Protections and CI Checks

## Objective
Set up branch protection rules in GitHub and configure CI checks to enforce code quality before merging, using AWS services.

## Prerequisites
- Completed Lab 1 (AWS version)
- GitHub account with owner or admin rights to the repository
- AWS account and configured CLI

## Note for Azure Lab Completers
If you have already completed the Azure version of this lab, you can skip steps 1 and 2. Your GitHub repository is already set up with the branch protection rules and the modified application. You can proceed directly to step 3 to update the AWS CodeBuild configuration.

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

### 4. Update AWS CodeBuild Configuration

a. Go to AWS CodeBuild and select your build project

b. Edit the buildspec to include pylint:
```yaml
version: 0.2
phases:
  install:
    runtime-versions:
      python: 3.8
  pre_build:
    commands:
      - pip install pylint
  build:
    commands:
      - echo "Running pylint"
      - pylint **/*.py || exit 1
      - echo "Building the Python application"
      - python -m compileall .
```

### 5. Update AWS CodePipeline

a. Go to AWS CodePipeline and edit your pipeline

b. Add a new stage after the Source stage

c. Add an action group to this new stage:
   - Action name: "CodeQuality"
   - Action provider: AWS CodeBuild
   - Input artifacts: SourceArtifact
   - Select your existing CodeBuild project

### 6. Test the CI Pipeline

a. Create a pull request from `development` to `main` in GitHub

b. Observe the CodePipeline run and check results

c. If there are linting errors, fix them and push again

d. Once all checks pass, merge the pull request

## Conclusion

You have now set up branch protection rules and implemented basic code quality checks in your CI pipeline using AWS services. This ensures that all code merged into the main branch passes automated checks and has been reviewed, improving overall code quality and collaboration in your development process.
