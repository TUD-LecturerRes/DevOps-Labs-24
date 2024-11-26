# Lab 1: Secure Web Application with Spring Boot and Azure DevOps

## Introduction

In this lab, we'll be working with a Spring Boot web application that demonstrates basic security concepts. We'll set up the project locally, create an Azure DevOps pipeline to build and test it, and finally deploy it to a web server.

### Learning Objectives
- Set up a Spring Boot web application with basic security
- Build and test the application locally
- Create an Azure DevOps pipeline for continuous integration
- Deploy the application to a web server
- Access and test the deployed application

### Prerequisites
- Java Development Kit (JDK) 17 or later
- Git
- Azure DevOps account
- Basic understanding of Spring Boot and web applications

## Part 1: Project Setup and Local Build

### 1. Clone the Repository and Introduction to Spring Boot

We'll be working with a sample project from the Spring guides. This project demonstrates how to create a secure web application using Spring Boot.

First, let's clone the sample project:

```bash
# Clone the repository
git clone https://github.com/spring-guides/gs-securing-web.git

# Create a new directory for our project
mkdir securing-web-azure
cd securing-web-azure

# Initialize a new git repository
git init

# Copy only the files we need from the completed project
Move the following files and directories from the `gs-securing-web/complete` folder to your new `securing-web-azure` directory:

- `src` directory
- `build.gradle` file
- `gradlew` file
- `gradlew.bat` file
- `gradle` directory
- `settings.gradle` file

# Add all files to git
git add .

# Commit the files
git commit -m "Initial commit of Spring Boot secure web application"
```

This project is based on the guide available at: https://spring.io/guides/gs/securing-web
Note: Spring Boot is a popular framework for building stand-alone, production-grade Spring-based applications with minimal configuration.

### 2. Understanding the Project Structure

This Spring Boot project demonstrates a simple web application with security features. Key components include:

- `MvcConfig.java`: Configures view controllers for the application
- `SecuringWebApplication.java`: The main application class that bootstraps the Spring Boot application
- `WebSecurityConfig.java`: Sets up security configuration
- Thymeleaf templates in `src/main/resources/templates`

Note: Thymeleaf is a modern server-side Java template engine for both web and standalone environments. It's often used with Spring Boot applications to create dynamic web pages. 

### 3. Building and Running Locally

Let's build and run the application locally to ensure everything works:

```bash
./gradlew bootRun
```

Once the application starts, open a web browser and go to `http://localhost:8080`. You should see a login page.

- Use username: "user" and password: "password" to log in
- After logging in, you should be able to access the Hello page

### 4. Running Tests

Run the tests to ensure everything is working correctly:

```bash
./gradlew test
```

This will execute the unit tests for the application.

## Part 2: Setting Up Azure DevOps Pipeline

### 1. Create a New Azure DevOps Project

1. Log in to your Azure DevOps account
2. Create a new project named "SpringSecureWebApp"

### 2. Push Your Code to Azure Repos (UPDATE: USE GITHUB for easier work with furter labs).

While we've been primarily using GitHub, for this lab we'll use Azure Repos. Azure Repos is a set of version control tools that you can use to manage your code. It's part of Azure DevOps and provides Git repositories or Team Foundation Version Control (TFVC) for your projects.

Now that we have our streamlined project set up locally, let's push it to Azure Repos:

1. In your Azure DevOps project, go to Repos and get the clone URL.

2. In your local `securing-web-azure` directory, add the Azure Repo as a remote and push your code:

   ```bash
   # Add the Azure Repo as a remote
   git remote add azure <Your-Azure-Repo-URL>

   # Push your code to Azure Repos
   git push -u azure --all
   ```

   Replace `<Your-Azure-Repo-URL>` with the URL you obtained from Azure Repos.
   Note: you might have to authenticate. 

3. After running these commands, your local Git repository will be connected to Azure Repos, and your code will be pushed to the main branch in Azure Repos.

### 3. Create the Pipeline

1. In Azure DevOps, go to Pipelines and create a new pipeline
2. Choose Azure Repos Git as the source
3. Select your repository
4. Select Gradle
5. Select Save (Do not Run a pipline yet)

### 4. Configure the Pipeline and Environment

First, let's create a Dev environment in Azure DevOps:

1. In your Azure DevOps project, go to Pipelines > Environments
2. Click "New environment"
3. Name it "Dev"
4. For the resource, choose "None" and click "Create"

Explanation of choosing "None" for resources:
- In Azure DevOps, an environment can have associated resources like Virtual Machines, Kubernetes namespaces, or Web Apps where you would typically deploy your application.
- By choosing "None", we're creating an "empty environment". This means:
  a) The environment exists in Azure DevOps as a logical construct.
  b) It doesn't have any specific deployment targets linked to it.
  c) We can use it in our pipeline for organization, approval gates, or deployment simulation.
  
- For this lab, an empty environment allows us to:
  a) Demonstrate the concept of environments in Azure DevOps pipelines.
  b) Set up our pipeline structure correctly without needing actual infrastructure.
  c) Simulate a deployment process, which is useful for learning and testing.
- In a real-world scenario, you would typically add actual resources to this environment, such as VMs or Azure Web Apps, where your application would be deployed.

Now, create a new file named `azure-pipelines.yml` in the root of your repository and add the following content:

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

variables:
  javaVersion: '17'

stages:
- stage: Build
  jobs:
  - job: BuildJob
    steps:
    - task: JavaToolInstaller@0
      inputs:
        versionSpec: '$(javaVersion)'
        jdkArchitectureOption: 'x64'
        jdkSourceOption: 'PreInstalled'
    
    - task: Gradle@2
      inputs:
        workingDirectory: ''
        gradleWrapperFile: 'gradlew'
        gradleOptions: '-Xmx3072m'
        javaHomeOption: 'JDKVersion'
        jdkVersionOption: '$(javaVersion)'
        jdkArchitectureOption: 'x64'
        publishJUnitResults: true
        testResultsFiles: '**/TEST-*.xml'
        tasks: 'bootJar'

    - task: CopyFiles@3
      inputs:
        contents: '**/build/libs/*SNAPSHOT.jar'
        targetFolder: '$(build.artifactStagingDirectory)'

    - task: PublishBuildArtifacts@1
      inputs:
        pathToPublish: '$(Build.ArtifactStagingDirectory)'
        artifactName: 'drop'

- stage: Deploy
  jobs:
  - deployment: DeployJob
    pool:
      vmImage: 'ubuntu-latest'
    environment: 'Dev'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: JavaToolInstaller@0
            inputs:
              versionSpec: '$(javaVersion)'
              jdkArchitectureOption: 'x64'
              jdkSourceOption: 'PreInstalled'
          
          - task: DownloadBuildArtifacts@0
            inputs:
              buildType: 'current'
              downloadType: 'single'
              artifactName: 'drop'
              downloadPath: '$(System.ArtifactsDirectory)'
          
          - script: |
              JAR_FILE=$(find $(System.ArtifactsDirectory) -name *SNAPSHOT.jar)
              if [ -z "$JAR_FILE" ]; then
                echo "No JAR file found"
                exit 1
              fi
              echo "Found JAR file: $JAR_FILE"
              java -jar $JAR_FILE &
            displayName: 'Deploy Spring Boot app'
```

Let's break down the structure of this pipeline:

1. **Trigger**: Specifies that the pipeline should run on any changes to the main branch.

2. **Pool**: Defines the type of agent to run the pipeline. We're using the latest Ubuntu image.

3. **Variables**: Sets the Java version to be used throughout the pipeline.

4. **Stages**: The pipeline is divided into two main stages:

   a. **Build Stage**:
      - Installs the specified Java version.
      - Uses the Gradle task to build the application and run tests.
      - Copies the built JAR file to a staging directory.
      - Publishes the JAR as a pipeline artifact.

   b. **Deploy Stage**:
      - Installs the same Java version as the Build stage.
      - Downloads the artifact produced in the Build stage.
      - Simulates deployment by running the Spring Boot application JAR file.

5. **Environment**: The Deploy stage uses the 'Dev' environment we created earlier.

This pipeline demonstrates a basic CI/CD process:
- It builds and tests the application.
- It creates an artifact (the JAR file).
- It simulates a deployment to a Dev environment.

Important notes:
- The deployment in this pipeline is simplified for demonstration purposes. In a real-world scenario, you would deploy to an actual server or cloud service.
- The application is started in the background (`&` at the end of the java command) and will continue running after the pipeline completes. In a production environment, you'd need proper process management.
- There's no step to stop the application or clean up resources. In a real deployment, you'd need to manage the application lifecycle more carefully.

In future labs, we'll expand on this pipeline to include more realistic deployment scenarios, additional testing stages, and other best practices for CI/CD.

### 5. Run the Pipeline

Commit and push your `azure-pipelines.yml` file, then run the pipeline in Azure DevOps.

### 6. Grant Pipeline Permissions

You need to grant permission to access the Dev environment:

1. When you run the pipeline for the **first time**, it will likely pause at the Deploy stage with a message: "This pipeline needs permission to access a resource before this run can continue to Deploy".

2. Click on the "View" button next to this message.

3. On the next page, you'll see the pipeline's access requests. There should be a request for access to the Dev environment.

4. Click "Permit" to grant the pipeline access to the Dev environment.

5. After granting permission, you'll need to re-run the pipeline.

This step is necessary because Azure DevOps uses this approval process as a security measure. It ensures that pipelines have explicit permission to deploy to environments, preventing unauthorized deployments and giving you control over which pipelines can deploy to which environments.

## Conclusion

After the pipeline completes successfully:

1. Go to the Azure DevOps project
2. Navigate to Pipelines > Runs
3. You should see your pipeline run with both Build and Deploy stages completed successfully

In this lab, you've successfully:
- Set up a Spring Boot web application with basic security
- Built and tested the application locally
- Created an Azure DevOps pipeline for continuous integration
- Simulated a deployment process in a Dev environment

While we haven't deployed to an actual web server in this lab, we've laid the groundwork for more advanced deployment scenarios in future labs.

## Next Steps

In future labs, we'll explore:
- Deploying the application to an actual web server or cloud service
- Enhancing the security features of this application
- Improving the application's performance
- Adding more sophisticated deployment strategies

## Reflection Questions

1. How does Spring Boot simplify the process of creating web applications with security features?
2. What benefits does the Azure DevOps pipeline provide in terms of automating the build process?
3. How might you modify the security configuration to use a database for user authentication instead of in-memory authentication?
4. What additional steps would be necessary to deploy this application to an actual web server or cloud service?
5. How could you enhance this pipeline to include more thorough testing or security checks?

By completing this lab, you've gained hands-on experience with Spring Boot application structure, basic Azure DevOps pipeline configuration, and the foundations of CI/CD practices. This sets the stage for more advanced topics in subsequent labs.
