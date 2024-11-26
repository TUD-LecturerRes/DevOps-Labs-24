# Lab 2: Implementing Testing, Coverage, and Security Analysis

In this lab, we will enhance our web application by implementing comprehensive testing, coverage reporting, and security analysis.

## Part 1: Implementing Testing and Coverage

### Task Description
Based on your knowledge from previous labs about testing and code coverage:
1. Update your `build.gradle` to include necessary testing and coverage reporting configurations
2. Ensure JaCoCo coverage reports are generated
3. Add some basic unit tests to your application

<details>
<summary>Click to see the complete build.gradle configuration</summary>

```gradle
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.3.0'
    id "org.sonarqube" version "5.1.0.4882"
    id 'jacoco'  // Add JaCoCo plugin
    id 'eclipse'  // Optional: for Eclipse integration
    id 'idea'    // Optional: for IntelliJ integration
}

apply plugin: 'io.spring.dependency-management'

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '17'

repositories {
    mavenCentral()
}

dependencies {
    // Keep your existing dependencies
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity6:3.1.2.RELEASE'
    testImplementation 'org.springframework.security:spring-security-test'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    
    // Add JUnit dependencies if needed (though Spring Boot starter test should include these)
    testImplementation(platform('org.junit:junit-bom:5.11.3'))
    testImplementation('org.junit.jupiter:junit-jupiter')
    testRuntimeOnly('org.junit.platform:junit-platform-launcher')
}

test {
    useJUnitPlatform()
    testLogging {
        events "passed", "skipped", "failed"
        showStandardStreams = true
    }
    finalizedBy jacocoTestReport
}

jacocoTestReport {
    dependsOn test
    reports {
        xml.required = true
        csv.required = false
        html.outputLocation = layout.buildDirectory.dir('jacocoHtml')
    }
}

// Optional: Add SonarQube properties
sonar {
    properties {
        property "sonar.sourceEncoding", "UTF-8"
        property "sonar.coverage.jacoco.xmlReportPaths", "${buildDir}/reports/jacoco/test/jacocoTestReport.xml"
    }
}
```
</details>

## Part 2: Updating Pipeline Configuration

### Task Description
Update your Azure DevOps pipeline to:
1. Execute tests during the build phase
2. Generate and publish coverage reports
3. Include test and coverage results in SonarQube analysis

### Pipeline Structure Explanation

The pipeline is organized into distinct stages for better organization and separation of concerns:

1. **Build Stage**
   - Compiles the code
   - Runs unit tests (fail-fast principle)
   - Generates coverage reports
   - Archives build outputs and test reports
   - Why here? We want to ensure code quality before proceeding further

2. **CodeAnalysis Stage**
   - Downloads build artifacts from previous stage
   - Performs SonarQube analysis using:
     - Compiled code
     - Test results
     - Coverage reports
   - Why separate? 
     - Isolates concerns
     - Can be skipped if build fails
     - Allows parallel execution if needed

3. **Artifact Handling**
   - Build outputs are archived using CopyFiles and PublishBuildArtifacts tasks
   - Downloaded in CodeAnalysis stage using DownloadBuildArtifacts
   - Why? Ensures all stages work with the same set of files and maintains build consistency

<details>
<summary>Click to see the complete azure-pipelines.yml configuration</summary>

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
    
    - task: Gradle@3
      inputs:
        workingDirectory: ''
        gradleWrapperFile: 'gradlew'
        gradleOptions: '-Xmx3072m'
        javaHomeOption: 'JDKVersion'
        jdkVersionOption: '$(javaVersion)'
        jdkArchitectureOption: 'x64'
        publishJUnitResults: true
        testResultsFiles: '**/TEST-*.xml'
        tasks: 'clean build test jacocoTestReport'

    # Archive build outputs and test reports
    - task: CopyFiles@2
      inputs:
        contents: |
          **/build/**
          **/build/reports/jacoco/**
        targetFolder: '$(Build.ArtifactStagingDirectory)'
    
    - task: PublishBuildArtifacts@1
      inputs:
        pathToPublish: '$(Build.ArtifactStagingDirectory)'
        artifactName: 'build-outputs'

    # Publish code coverage results
    - task: PublishCodeCoverageResults@2
      inputs:
        summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/build/reports/jacoco/test/jacocoTestReport.xml'
        pathToSources: '$(System.DefaultWorkingDirectory)/src/main/java/'
        failIfCoverageEmpty: true

- stage: CodeAnalysis
  jobs:
  - job: SonarCloudAnalysis
    steps:
    - checkout: self
      fetchDepth: 0

    # Download build artifacts for analysis
    - task: DownloadBuildArtifacts@0
      inputs:
        buildType: 'current'
        downloadType: 'single'
        artifactName: 'build-outputs'
        downloadPath: '$(System.DefaultWorkingDirectory)'

    - task: SonarCloudPrepare@3
      inputs:
        SonarCloud: 'SonarConnection'
        organization: 'your-sonarcloud-organization'
        scannerMode: 'other'
        extraProperties: |
          # Additional properties that will be passed to the scanner
          sonar.projectKey=your-project-key
          sonar.projectName=your-project-name
          sonar.java.binaries=$(System.DefaultWorkingDirectory)/**/build/classes
          sonar.coverage.jacoco.xmlReportPaths=$(System.DefaultWorkingDirectory)/**/build/reports/jacoco/test/jacocoTestReport.xml
          sonar.junit.reportPaths=$(System.DefaultWorkingDirectory)/**/build/test-results/test
          sonar.java.libraries=$(System.DefaultWorkingDirectory)/**/build/libs/*.jar

    - task: Gradle@3
      inputs:
        workingDirectory: ''
        gradleWrapperFile: 'gradlew'
        gradleOptions: '-Xmx3072m'
        javaHomeOption: 'JDKVersion'
        jdkVersionOption: '$(javaVersion)'
        jdkArchitectureOption: 'x64'
        tasks: 'sonar'

    - task: SonarCloudPublish@3
      inputs:
        pollingTimeoutSec: '300'
```
</details>

### Expected Outcomes
- Successfully running unit tests in your pipeline
- Generated coverage reports
- SonarQube analysis incorporating test results and coverage data

### Verification
1. Check Azure DevOps pipeline execution
2. Review test results in Test Plans section
3. Examine coverage reports in Build artifacts
4. Verify SonarCloud analysis results:
   - Code quality metrics
   - Test coverage percentage
   - Security vulnerabilities
   - Code smells and technical debt

## Part 3: Basic Security Scanning

### Overview
After implementing testing, coverage, and SonarQube analysis, we'll enhance our security posture by adding basic security scanning. This will help us identify potential security issues early in our development cycle.

### Prerequisites
- Completed Lab 2.1 and 2.2
- Azure DevOps Organization admin rights (to install extensions)
- Existing pipeline configuration

### Step 1: Install Microsoft Security DevOps Extension
1. Navigate to Azure DevOps Marketplace
2. Search for "Microsoft Security DevOps for Azure DevOps"
3. Install the extension to your organization
4. Verify installation in Organization Settings > Extensions

Why? This extension provides built-in security scanning tools and integrates seamlessly with Azure DevOps pipelines.

### Step 2: Add Security Scanning Stage
For our Java Spring Boot application, let's implement basic security scanning:

```yaml
- stage: SecurityScanning
  displayName: 'Security Scanning'
  jobs:
  - job: SecurityCheck
    steps:
    # Download artifacts from previous build stage
    - task: DownloadBuildArtifacts@0
      inputs:
        buildType: 'current'
        downloadType: 'single'
        artifactName: 'build-outputs'
        downloadPath: '$(System.DefaultWorkingDirectory)'

    # Run security scanning
    - task: MicrosoftSecurityDevOps@1
      inputs:
        categories: 'sources'  # Only scanning source code for now
        break: false # Starting with false to avoid breaking builds
        tools: 'credscan'    # Basic credential scanning
        sourcePath: '$(System.DefaultWorkingDirectory)'
```

Understanding the configuration:
- `categories`: Specifies what to scan. Options include:
  - `sources`: Source code scanning (what we need for our Java project)
  - `IaC`: Infrastructure as Code (not needed for our basic Java project)
  - `containers`: Docker container scanning (not needed yet)

- `break`: Whether to fail the build on findings (false for initial setup)

- `tools`: Available security scanning tools:
  - `credscan`: Scans for hardcoded credentials (relevant for any project)
  
  Other available tools (note platform limitations):
  - `antimalware`: Windows agents only
  - `bandit`: Python-specific scanning
  - `binskim`: Windows/ELF binary scanning
  - `checkov`, `terrascan`, `templateanalyzer`: Infrastructure as Code scanning
  - `eslint`: JavaScript scanning
  - `trivy`: Container and IaC scanning

- `sourcePath`: Points to the root directory containing source code

Setup Requirements:
1. Install the [Microsoft Security DevOps Extension](https://marketplace.visualstudio.com/items?itemName=MS-CST-E.MicrosoftSecurityDevOps) from Azure DevOps Marketplace
2. Make sure your build agent has necessary permissions

Note: For Java-specific security scanning:
1. We're already using SonarQube in another stage
2. Consider adding Java-specific tools like OWASP Dependency Check later (covered in next step)
3. Be aware of platform limitations when selecting security tools

For complete tool list and configuration options, see [Microsoft Security DevOps documentation](https://github.com/microsoft/security-devops-azdevops).

### Step 3: Pipeline Integration
Your stages should now flow like this:
1. Build (existing)
2. CodeAnalysis (existing)
3. SecurityScanning (new)

## Part 4: Add Dependency Security Scanning
We'll implement dependency security scanning using OWASP Dependency Check to identify vulnerabilities in project dependencies.

### 1. Obtain NVD API Key
1. Visit [NVD API Key Registration Page](https://nvd.nist.gov/developers/request-an-api-key)
2. Fill out the registration:
3. Submit the form
4. Check your email for the API key (usually arrives within minutes)

### 2. Install Required Extension
Install the OWASP Dependency Check extension from the Azure DevOps Marketplace:
1. Go to [OWASP Dependency Check Azure DevOps Extension](https://marketplace.visualstudio.com/items?itemName=dependency-check.dependencycheck)
2. Click "Get it free"
3. Select your organization and install

### 3. Add Pipeline Configuration
Add this stage to your azure-pipelines.yml:

```yaml
- stage: DependencySecurity
  displayName: 'Dependency Security Scanning'
  jobs:
  - job: OWASPCheck
    steps:
    - task: dependency-check-build-task@6
      inputs:
        projectName: '$(Build.Repository.Name)'
        scanPath: '$(System.DefaultWorkingDirectory)'
        format: 'HTML'
        uploadReports: true
        uploadSARIFReport: true
        failOnCVSS: '7'
        nvdApiKey: 'your-nvd-api-key'  # Replace with your API key
```

### 4. Configuration Options Explained
- `projectName`: Name of your project (using repository name)
- `scanPath`: Directory to scan for dependencies
- `format`: Output format for reports (HTML)
- `uploadReports`: Automatically uploads reports to pipeline artifacts
- `uploadSARIFReport`: Enables SARIF report generation and upload
- `failOnCVSS`: Fails build if vulnerabilities above this CVSS score are found (7 = High severity)
- `nvdApiKey`: Your NVD API key for faster vulnerability database updates

### 6. Best Practices
1. Start with CVSS threshold of 7 (adjust based on security requirements)
2. Review dependency reports after each build
3. Document reasons for suppressions
4. Regular review of suppressed vulnerabilities
5. Include vulnerability reports in release documentation
6. Schedule regular dependency updates

### 7. Viewing Results
1. After pipeline run, go to the build summary
2. Check 'Artifacts' section
3. Find reports in the 'Dependency Check' artifact
4. Review any identified vulnerabilities
5. Update dependencies or add suppressions as needed

## Part 5: Optimize Dependency Security Scanning using Azure Blob Storage (Optional)

### 1. Create Azure Storage Account (GUI Method)
1. Go to [Azure Portal](https://portal.azure.com)
2. Click "Create a resource"
3. Search for "Storage account"
4. Click "Create"
5. Fill in the basics:
   - Subscription: (your subscription)
   - Resource group: (your resource group)
   - Storage account name: "owaspdepcheck" (or similar unique name)
   - Region: (choose nearest)
   - Performance: Standard
   - Redundancy: LRS
6. Click "Review + Create"
7. Click "Create"

### 2. Create Container
1. Once storage account is created, go to it
2. In left menu, click "Containers"
3. Click "+ Container"
4. For name, enter "nvd-database"
5. Leave access level as "Private"
6. Click "Create"

### 3. Download NVD Database Locally
1. Download OWASP Dependency Check:
   - Go to https://github.com/jeremylong/DependencyCheck/releases
   - Download latest release zip
   - Extract the zip to a location (e.g., D:\Tools\dependency-check)

2. Create data directory:
   - Create a folder for the database (e.g., D:\Tools\database)

3. Download NVD Database:
   
   **For Windows:**
   ```cmd
   D:\Tools\dependency-check\bin\dependency-check.bat --updateonly --nvdApiKey YOUR_API_KEY --data "D:\Tools\database"
   ```

   **For Linux/Mac:**
   ```bash
   ./dependency-check/bin/dependency-check.sh --updateonly --nvdApiKey YOUR_API_KEY --data "./database"
   ```

   Note: Replace YOUR_API_KEY with your NVD API key
   
4. Wait for download to complete (may take several minutes)
   - You should see messages about updating the NVD database
   - Process is complete when command returns to prompt

### 4. Upload Database Files
1. Navigate to your downloaded database files (e.g., D:\Temp\database)
2. Select all files and folders
3. Right-click > Send to > Compressed (zipped) folder
4. Name it "nvd-db.zip"
5. In Azure Portal:
   - Go to your container "nvd-database"
   - Click "Upload"
   - Browse to your nvd-db.zip
   - Click "Upload"

### 5. Configure Pipeline Permissions
1. In Azure DevOps:
   - Go to Project Settings
   - Select "Service connections"
   - Click "New service connection"
   - Choose "Azure Resource Manager"
   - Select "Workload Identity federation (automatic)"

2. Fill in the details:
   - Subscription: Select your subscription
   - Service connection name: "Azure-Storage-Connection"
   - Security:
     - ✓ Grant access permission to all pipelines
   - Click "Save"

### 6. Modify Your Pipeline
Update your `azure-pipelines.yml` to include these steps before the dependency check task:

```yaml
- stage: DependencySecurity
  displayName: 'Dependency Security Scanning'
  jobs:
  - job: OWASPCheck
    steps:
    - task: AzureCLI@2
      inputs:
        azureSubscription: 'Azure-Storage-Connection'
        scriptType: 'bash'
        scriptLocation: 'inlineScript'
        inlineScript: |
          # Create cache directory
          mkdir -p "$(Pipeline.Workspace)/owasp-cache"
                  
          # Download database
          az storage blob download --account-name owaspdepcheck --container-name nvd-database --name nvd-db.zip --file nvd-db.zip

          # Extract database
          unzip nvd-db.zip -d "$(Pipeline.Workspace)/owasp-cache"    # Changed from Expand-Archive to unzip

          # Check if critical file exists despite warnings
          if [ -f "$(Pipeline.Workspace)/owasp-cache/odc.mv.db" ]; then
            echo "Files extracted successfully"
            exit 0
          else
            echo "Extraction failed - missing critical files"
            exit 1
          fi
    - task: dependency-check-build-task@6
      inputs:
        projectName: '$(Build.Repository.Name)'
        scanPath: '$(System.DefaultWorkingDirectory)'
        format: 'HTML'
        uploadReports: true
        uploadSARIFReport: true
        failOnCVSS: '7'
        additionalArguments: '--data $(Pipeline.Workspace)/owasp-cache' # Custom cache directory
        nvdApiKey: 'your-nvd-api-key'  # Replace with your API key'
```

### 7. Verify Implementation
1. Commit and push your updated pipeline
2. Run the pipeline
3. Monitor the execution:
   - Check that database download is successful (in Azure CLI task logs)
   - Verify extraction completes successfully
   - Confirm dependency check runs with cached database
4. Compare execution times:
   - Note the time taken for this run
   - Compare with previous runs without blob storage
   - You should see significant improvement (typically 5-10 minutes faster)
