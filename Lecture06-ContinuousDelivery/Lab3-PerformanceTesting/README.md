# Lab 3: Performance Testing with JMeter and Azure DevOps Integration

## Prerequisites and Initial Setup
✅ Required before starting:
- Azure DevOps account with permissions to create pipelines and environments
- Git installed locally
- Java JDK 17 installed
- Your Spring Boot application from previous labs
- Basic understanding of HTTP and REST APIs
- VSCode or preferred IDE

## Estimated Time: 2-3 hours

## Learning Objectives
By completing this lab, you will:
- Learn how to create and execute JMeter performance tests
- Understand basic performance testing concepts
- Configure CI/CD pipelines to include automated performance testing
- Analyze and interpret performance test results

## Understanding Performance Testing
Key concepts you'll be working with:
- Validating application responsiveness under load
- Identifying bottlenecks before production deployment
- Establishing performance baselines
- Ensuring scalability requirements are met

# Lab 3 - Part 1: Setting Up JMeter

## Prerequisites
- Java JDK 17 installed
- Maven or Gradle installed
- Your Spring Boot application from previous labs

### Repository Setup

#### 1. Create Performance Tests Directory
Create a new directory in your repository root for performance tests:
```bash
your-spring-boot-app/
├── src/
├── performance-tests/          # Create this new directory
│   ├── homepage-test.jmx      # JMeter test plans will go here
│   └── README.md              # Optional: documentation about tests
├── .gitignore
├── build.gradle
└── gradle/
```

#### 2. Update .gitignore
Add these lines to your existing `.gitignore` file:

```bash
# Existing content
# Gradle
.gradle/
build/

# Ignore Gradle GUI config
gradle-app.setting

# Eclipse
/.classpath
/.settings/
/.project
/bin/

# IntelliJ
.idea/
*.iml
*.ipr
*.iws

# Visual Studio Code
.vscode/

# Misc
*.log

# JMeter specific ignores (add these)
**/*.jtl
**/jmeter.log
performance-tests/*.log
performance-tests/results/
# Keep JMeter test plans in git
!**/*.jmx
```

This will ensure:
- JMeter test plans (`.jmx` files) are tracked in version control
- Test results and logs are ignored
- Your repository stays clean from temporary test files

## 1. Running the Application Locally

### 1.1 First, let's ensure our application is running
Navigate to your Spring Boot application directory and run:

```bash
#Gradle
./gradlew bootRun
```

### 1.2 Verify the application is running
Open your browser and navigate to:
- http://localhost:8080 - Should see the application homepage

Keep this terminal window open, as we'll need the application running for our JMeter tests.

## 2. Installing JMeter

### 2.1 Download and Install
Download Apache JMeter:
```bash
# For Windows users:
1. Visit https://jmeter.apache.org/download_jmeter.cgi
2. Download the latest binary release (zip file)
3. Extract to C:\Tools\apache-jmeter (or your preferred location)

# For Mac users:
brew install jmeter

# For Linux users:
sudo apt-get install jmeter
```

### 2.2 Configure Environment Variables
```bash
# For Windows:
1. Open System Properties → Environment Variables
2. Under System Variables, find 'Path'
3. Click 'Edit' and add new entry: C:\Tools\apache-jmeter\bin

# For Mac/Linux:
export JMETER_HOME=/usr/local/opt/jmeter
export PATH=$JMETER_HOME/bin:$PATH
```

### 2.3 Verify Installation
Open a new terminal window and run:
```bash
jmeter -v
```
You should see JMeter version information. If not please restart your VSCode. 

## 3. Creating Your First Test Plan

### 3.1 Launch JMeter GUI
```bash
jmeter
```

### 3.2 Create Basic Test Plan Structure
1. Right-click on 'Test Plan' → Add → Threads (Users) → Thread Group
2. Name your Thread Group "Homepage Test"
3. Configure Thread Group:
   - Number of Threads (users): 5
   - Ramp-up period: 10
   - Loop Count: 1

### 3.3 Add HTTP Request Defaults
1. Right-click on Homepage Test → Add → Config Element → HTTP Request Defaults
2. Configure:
   - Protocol: http
   - Server Name or IP: localhost
   - Port Number: 8080

### 3.4 Add HTTP Request
1. Right-click on Homepage Test → Add → Sampler → HTTP Request
2. Configure:
   - Method: GET
   - Path: /

### 3.5 Add Listeners
1. Right-click on Homepage Test → Add → Listener → View Results Tree
2. Right-click on Homepage Test → Add → Listener → Summary Report
3. Right-click on Homepage Test → Add → Listener → Graph Results

### 3.6 Save Test Plan
1. Click File → Save Test Plan As
2. Save as `homepage-test.jmx` in a new directory called `performance-tests`

## 4. Running Your First Test

### 4.1 Run the Test
1. Ensure your Spring Boot application is still running
2. In JMeter GUI, click the green "Play" button (or Ctrl + R)
3. Watch the results in real-time in the View Results Tree

### 4.2 Analyze Results
1. Check View Results Tree:
   - Green checkmarks indicate successful requests
   - Red X's indicate failures
2. Review Summary Report:
   - Average response time
   - Throughput
   - Error rate (if any)

### 4.3 Save Results
1. In View Results Tree, click "Save As" to save the results
2. Save as `homepage-test-results.xml`

## Practice Exercise
1. Modify the Thread Group settings:
   - Increase to 10 users
   - Change ramp-up to 30 seconds
   - Set Loop Count to 2
2. Run the test again
3. Compare the results with your first test
4. What differences do you notice in:
   - Response times?
   - Throughput?
   - Error rates (if any)?

## Next Steps
After completing this section, you should:
- Have JMeter installed and configured
- Understand how to create a basic test plan
- Know how to run tests and view results

# Lab 3 - Part 2: Creating Advanced Performance Test Scenarios

## Overview
In this part, we'll create more complex test scenarios that represent real-world usage of your application. We'll build upon your basic homepage test and create additional test plans for different functionalities of your Spring Boot application.

## 1. Creating Login Performance Test

### 1.1 Understanding the Scenario
For testing login functionality, we need to:
- Send POST request with credentials
- Handle JSON payload
- Verify successful login response
- Extract and store authentication token (if applicable)

### 1.2 Create Login Test Plan
1. Launch JMeter and create a new Test Plan
2. Name it "Login Performance Test"
3. Save as `login-test.jmx` in your `performance-tests` directory

### 1.3 Configure Test Elements
1. Add Thread Group:
   ```
   Right-click Test Plan → Add → Threads → Thread Group
   Configure:
   - Name: Login Users
   - Number of Threads: 5 (simulating 5 concurrent users)
   - Ramp-up period: 10
   - Loop Count: 2
   - Same user on each iteration: ✓
   ```

2. Add Cookie Manager:
   ```
   Right-click Login Users → Add → Config Element → HTTP Cookie Manager
   (Use default settings)
   ```

3. Add HTTP Request Defaults:
   ```
   Right-click Login Users → Add → Config Element → HTTP Request Defaults
   Configure:
   Basic tab:
   - Protocol: http
   - Server Name: localhost
   - Port Number: 8080
   ```

4. Add HTTP Request:
   ```
   Right-click Login Users → Add → Sampler → HTTP Request
   Configure:
   - Name: Login Verification
   - Method: POST
   - Path: /login
   - Content encoding: UTF-8
   ```
Add Parameters (click 'Add' button in the 'Parameters' section):

| Name     | Value    | URL Encode? | Content-Type | Include Equals? |
|----------|----------|-------------|--------------|-----------------|
| username | user     | ✓           | text/plain   | ✓              |
| password | password | ✓           | text/plain   | ✓              |

Note:
Check (✓) both 'URL Encode?' and 'Include Equals?' for each parameter
Content-Type shows 'text/plain' by default - this is fine for form parameters

5. Add Response Assertion for Login:
   ```
   Right-click Login Request → Add → Assertions → Response Assertion
   Configure:
   - Field to Test: Response Code
   - Pattern to Test: 200
   ```

6. Add Homepage HTTP Request:
   ```
   Right-click Login Users → Add → Sampler → HTTP Request
   Configure:
   - Name: Homepage Verification
   - Method: GET
   - Path: /
   ```

7. Add Response Assertion for Homepage:
   ```
   Right-click Homepage Verification → Add → Assertions → Response Assertion
   Configure:
   - Field to Test: Text Response
   - Pattern to Test: Welcome!
   - Test Pattern: Contains
   ```

8. Add Listeners:
   ```
   Right-click Login Users → Add → Listener → View Results Tree
   Right-click Login Users → Add → Listener → Summary Report
   Right-click Login Users → Add → Listener → Graph Results
   ```

Notes:
- Make sure to keep the sequence of requests in order (Login Request followed by Homepage Verification)
- The Cookie Manager will automatically handle the session between requests
- First request expects a 302 redirect
- Second request verifies successful login by checking for "Welcome!" text

### 1.4 Test Login Scenario

1. Ensure your application is running
2. Run the login test plan
3. Check View Results Tree:
   - Verify successful login responses
   - Check response times
   - Look for any errors


# Lab 3 Part 3 - Integrating JMeter Tests in Azure DevOps Pipeline

## Overview
In this lab, you will learn how to integrate JMeter performance tests into an Azure DevOps CI/CD pipeline. You'll configure a test environment, modify an existing pipeline, and run automated performance tests.

## Lab Instructions

### Part 1: Prepare the Pipeline
1. In your local repo open azure-piplines.yml file. 

2. Comment out non-essential stages to speed up development:
   ```
   - Select the CodeAnalysis, SecurityScanning, and DependencySecurity stages
   - Press Ctrl + / (Windows/Linux) or Cmd + / (macOS)
   ```

   Your pipeline should now look like:
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
       # ... (Build stage steps remain unchanged)

   # - stage: CodeAnalysis    # commented out
   # - stage: SecurityScanning    # commented out
   # - stage: DependencySecurity    # commented out
   ```

### Part 2: Update Build Stage

1. **Add Performance Test Files Copy**
   Add the following task to your build stage in azure-pipelines.yml:
   ```yaml
    - task: CopyFiles@2
      displayName: 'Copy jmx files'
      inputs:
        sourceFolder: '$(Build.SourcesDirectory)'
        contents: 'performance-tests/*.jmx'
        targetFolder: '$(Build.ArtifactStagingDirectory)'
   ```
   This ensures your JMeter test files are included in the build artifacts.

### Part 3: Create Test Environment

1. **Create new environment**
   - Navigate to Pipelines → Environments
   - Click 'New environment'
   - Name: Test
   - Click 'Create'

### Part 4: Add Deployment and Performance Testing Stage

Add the following stage to your azure-pipelines.yml file:

```yaml
- stage: Deploy
  jobs:
  - deployment: DeployJob
    pool:
      vmImage: 'ubuntu-latest'
    environment: 'Test'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: JavaToolInstaller@0
            inputs:
              versionSpec: '$(javaVersion)'
              jdkArchitectureOption: 'x64'
              jdkSourceOption: 'PreInstalled'
          
          # Download application
          - task: DownloadBuildArtifacts@0
            inputs:
              buildType: 'current'
              downloadType: 'single'
              artifactName: 'build-outputs'
              downloadPath: '$(System.ArtifactsDirectory)'
          
          # Setup and start service
          - script: |
              JAR_FILE="$(System.ArtifactsDirectory)/build-outputs/build/libs/securing-web-0.0.1-SNAPSHOT.jar"
              if [ ! -f "$JAR_FILE" ]; then
                echo "JAR file not found at: $JAR_FILE"
                ls -R $(System.ArtifactsDirectory)
                exit 1
              fi
              echo "Found JAR file: $JAR_FILE"
              
              # Find Java 17 path
              JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))
              echo "JAVA_HOME: $JAVA_HOME"
              
              # Copy JAR to a permanent location
              sudo mkdir -p /opt/springapp
              sudo cp "$JAR_FILE" /opt/springapp/app.jar
              
              # Create service file
              sudo tee /etc/systemd/system/springapp.service << EOF
              [Unit]
              Description=Spring Boot Application
              After=network.target
              
              [Service]
              Type=simple
              User=vsts
              Environment="JAVA_HOME=$JAVA_HOME"
              ExecStart=$JAVA_HOME/bin/java -jar -Dserver.address=0.0.0.0 /opt/springapp/app.jar
              Restart=always
              WorkingDirectory=/opt/springapp
              
              [Install]
              WantedBy=multi-user.target
              EOF
              
              # Start service
              sudo systemctl daemon-reload
              sudo systemctl enable springapp
              sudo systemctl start springapp
              
              # Check status and logs
              sleep 5
              sudo systemctl status springapp
              sudo journalctl -u springapp --no-pager -n 50
            displayName: 'Deploy Spring Boot app as service'

          # Install JMeter
          - task: JMeterInstaller@0
            inputs:
              jmeterVersion: '5.6.3'
    
          # Create directories for results
          - task: Bash@3
            displayName: 'Create Results Directories'
            inputs:
              targetType: 'inline'
              script: |
                mkdir -p $(Build.ArtifactStagingDirectory)/jmeter/homepage-dashboard
                mkdir -p $(Build.ArtifactStagingDirectory)/jmeter/login-dashboard

          # Run Homepage Test
          - task: CmdLine@2
            displayName: 'Run Homepage Test'
            inputs:
              script: |
                jmeter -n -t $(Pipeline.Workspace)/build-outputs/performance-tests/homepage-test.jmx \
                      -l $(Build.ArtifactStagingDirectory)/jmeter/homepage-results.jtl \
                      -e -o $(Build.ArtifactStagingDirectory)/jmeter/homepage-dashboard

          # Run Login Test
          - task: CmdLine@2
            displayName: 'Run Login Test'
            inputs:
              script: |
                jmeter -n -t $(Pipeline.Workspace)/build-outputs/performance-tests/login-test.jmx \
                      -l $(Build.ArtifactStagingDirectory)/jmeter/login-results.jtl \
                      -e -o $(Build.ArtifactStagingDirectory)/jmeter/login-dashboard
    
          # Publish Results
          - task: PublishBuildArtifacts@1
            inputs:
              pathToPublish: '$(Build.ArtifactStagingDirectory)/jmeter'
              artifactName: 'jmeter-results'
```

### Part 5: Review and Execute

1. **Commit and push changes**
   ```bash
   git add .
   git commit -m "Added deployment and performance testing stage"
   git push
   ```

2. **Monitor pipeline execution**
   - Navigate to Pipelines
   - Watch the pipeline progress through stages
   - You may need to approve the deployment to the Test environment

3. **Review test results**
   - After pipeline completion, go to the pipeline run summary
   - Download and examine the JMeter results artifacts
   - Open the dashboard reports to view performance metrics

#### Verification Checklist
- [ ] Build stage completes successfully and includes JMeter files
- [ ] Application deploys successfully as a service
- [ ] JMeter tests execute against the deployed application
- [ ] Performance test results are published as artifacts
