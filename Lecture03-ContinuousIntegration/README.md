# Lecture 03: Continuous Integration

This directory contains materials for Lecture 3, focusing on Continuous Integration (CI). There are 2 labs in this section, each with versions for both AWS and Azure:

1. [Basic CI Pipeline](./aws/Lab1-BasicCIPipeline) (AWS) / [Basic CI Pipeline](./azure/Lab1-BasicCIPipeline) (Azure)
2. [Branch Protection and CI Checks](./aws/Lab2-BranchProtectionAndCIChecks) (AWS) / [Branch Protection and CI Checks](./azure/Lab2-BranchProtectionAndCIChecks) (Azure)

Each lab directory contains a README.md file with instructions specific to the chosen cloud platform (AWS or Azure).

## Lab Descriptions

### Lab 1: Basic CI Pipeline
Setting up a basic Continuous Integration pipeline using either AWS services (CodeCommit, CodeBuild, CodePipeline) or Azure services (Azure Repos, Azure Pipelines). This lab covers creating a simple Python application, setting up a GitHub repository, and configuring a CI pipeline to automatically build and test the code on every commit.

### Lab 2: Branch Protection and CI Checks
Implementing branch protection rules and enhancing the CI pipeline to include code quality checks. This lab covers creating development branches, setting up branch protection rules in GitHub, configuring linting checks in the CI pipeline, and managing pull requests with automated checks.

## Prerequisites

- Completed Lecture 2 labs on Version Control and Git
- GitHub account with owner or admin rights to a repository
- For AWS labs:
  - AWS account
  - AWS CLI installed and configured
- For Azure labs:
  - Azure DevOps account
- Python installed locally
- Git installed locally

## Note on Lab Versions

Each lab is available in both AWS and Azure versions. You may choose to complete either or both versions based on your preferred cloud platform. If you complete one version (e.g., AWS) and then want to do the other (e.g., Azure), you can skip some initial steps in the second version as noted in the lab instructions.

Please complete Lab 1 before moving on to Lab 2, as the second lab builds upon the CI pipeline created in the first lab.
