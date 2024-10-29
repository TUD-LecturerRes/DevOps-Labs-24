# Lab 1: Build Automation with Gradle and Azure Pipelines

## Objective
In this lab, you will learn how to use Gradle for build automation and integrate it with Azure Pipelines. You'll set up a simple Java project, create a Gradle build script, manage dependencies, and configure the build process. Finally, you'll integrate your Gradle build with Azure Pipelines.

## Prerequisites

### Required Software
1. Java Development Kit (JDK) - Latest LTS Version (minimum JDK 11)
   - Download and install from: https://adoptium.net/
   - Choose the latest LTS (Long Term Support) version
   - During installation:
     - Ensure the option to add Java to the PATH is selected
     - Look for an option labeled "Set or override JAVA_HOME variable" and select "Will be installed on local hard drive"
     - This will automatically set up the JAVA_HOME environment variable

   **Note**: While we recommend the latest LTS version for new projects, be aware that some organizations might still use JDK 11 for legacy reasons. Always check project requirements.

2. Gradle 7.0 or later (latest version recommended)
   - Download from: https://gradle.org/install/#manually and follow instructions to:
   - Extract to a directory (e.g., C:\Gradle)
   - Add Gradle's bin directory to your PATH environment variable

3. Git (minimum version 2.25)
   - Download and install from: https://git-scm.com/download/win

4. Visual Studio Code (minimum version 1.60)
   - Download and install from: https://code.visualstudio.com/

### VS Code Extensions
Install the following extensions in VS Code:
1. Java Extension Pack
2. Gradle for Java

To verify installation of VS Code extensions:
1. Open VS Code
2. Click on the Extensions icon in the left sidebar (or press Ctrl+Shift+X)
3. In the search bar, type the name of each extension
4. Ensure that the extensions are listed as "Installed"

### Accounts
1. GitHub account: https://github.com/
2. Azure DevOps account: https://azure.microsoft.com/en-us/services/devops/

### Verifying Installation
Open a new Command Prompt and run:
```bash
java -version
gradle -v
git --version
code --version
```
Each command should return version information if installed correctly.

## Part 1: Setting up a Java Project with Gradle

### 1.1 Create a new directory for your project
Open a command prompt and run:
```bash
mkdir gradle-demo
cd gradle-demo
```

### 1.2 Initialize a Gradle project
Run the following command to initialize a new Gradle project:
```bash
gradle init --type java-application
```

You'll see a message: "Starting a Gradle Daemon (subsequent builds will be faster)"

Then, you'll be prompted to answer several questions. Follow these steps:

1. "Enter target Java version (min: 7, default: 21):"
   - Press Enter to accept the default (21), which is the latest LTS version.

2. "Project name (default: gradle-demo):"
   - Press Enter to accept the default, or type a different name if you prefer.

3. "Select application structure:"
   1: Single application project
   2: Application and library project
   - Type `1` and press Enter to select "Single application project".

4. "Select build script DSL:"
   1: Kotlin
   2: Groovy
   - Type `2` and press Enter to select "Groovy".
   Note: While Kotlin is the default, we'll use Groovy for this lab as it's more widely used.

5. "Select test framework:"
   1: JUnit 4
   2: TestNG
   3: Spock
   4: JUnit Jupiter
   - Type `4` and press Enter to select "JUnit Jupiter".
   Note: JUnit Jupiter is the latest version of JUnit and provides more features.

6. "Generate build using new APIs and behavior (some features may change in the next minor release)? (default: no) [yes, no]"
   - Press Enter to accept the default (no).
   Note: We'll stick with the stable APIs to ensure consistency.

After answering these prompts, Gradle will initialize your project. 

This command creates a basic Java application structure with Gradle as the build tool, setting up:
- A `src` directory with main and test subdirectories
- A `build.gradle` file for build configuration
- A `settings.gradle` file for project settings
- A `gradlew` file for the Gradle wrapper

Note: The initialization process might take several minutes, especially if it's the first time you're running Gradle on your system.

**Note**: The output of Gradle commands may vary slightly depending on your Gradle version. If you see any significant differences, please refer to the Gradle documentation for your specific version.

### 1.3 Examine the project structure

After initializing the project, Gradle creates a specific directory structure. Let's explore it:

1. Root directory (`gradle-demo`):
   - `.gitattributes` and `.gitignore`: Git configuration files
   - `gradlew` and `gradlew.bat`: Gradle wrapper scripts for Unix-like systems and Windows
   - `settings.gradle`: Gradle settings file

2. `app` directory:
   - `build.gradle`: Main build script for the application

3. `app/src/main/java/org/example`:
   - `App.java`: Main application class

4. `app/src/test/java/org/example`:
   - `AppTest.java`: Test class for the application

5. `gradle` directory:
   - `libs.versions.toml`: Dependency version management file
   - `wrapper` subdirectory: Contains Gradle wrapper JAR and properties

Key points to note:
- The `app` directory contains your application code and tests.
- The `src/main/java` directory is for your main application code.
- The `src/test/java` directory is for your test code.
- The `org/example` package structure is created by default.

To explore this structure in Visual Studio Code:

1. Open VS Code
2. Go to File > Open Folder
3. Navigate to and select the `gradle-demo` folder
4. In the Explorer sidebar (Ctrl+Shift+E), you can now see and navigate this structure

Understanding this structure is important as it follows Gradle conventions, making it easier to organize your code and for other developers to understand your project layout.

### 1.4 Open and Explore the Project in Visual Studio Code

1. Launch Visual Studio Code
2. Go to File > Open Folder
3. Navigate to and select the `gradle-demo` folder you created
4. Click 'Select Folder' to open the project

5. Once the project is open, explore the structure in the Explorer sidebar (Ctrl+Shift+E):
   - Expand the `app` folder
   - Navigate to `src > main > java > org > example`
   - Open `App.java`

6. You should see the main application class. If Java icons and syntax highlighting are visible, your setup is correct.

### 1.5 Run the Application from VS Code

1. With `App.java` open, look for the "Run | Debug" text that appears above the `main` method
2. Click "Run" to execute the application
3. The output should appear in the integrated terminal at the bottom of VS Code
4. You should see the following output:
   ```
   Hello World!
   ```

This output confirms that your Java application is set up correctly and running as expected. The "Hello World!" message is a classic first output for programming tutorials, serving as a simple indicator that the program is executing successfully.

### 1.6 Examine the Test Class

1. In the Explorer sidebar, navigate to `app > src > test > java > org > example`
2. Open `AppTest.java`
3. This file contains a basic test for the `App` class

### Conclusion
In this section, you've created a basic Java project structure using Gradle and set it up in Visual Studio Code. This process demonstrates how build tools like Gradle can automate project creation, ensuring a consistent and correct setup. By opening the project in VS Code, you've prepared your development environment for efficient Java coding, with features like code completion, debugging, and integrated builds readily available. This setup forms the foundation for the rest of the lab, where you'll further explore Gradle's capabilities and integrate with Azure Pipelines.

In the next sections, we'll dive deeper into Gradle's build configuration and start preparing our project for integration with Azure Pipelines.

## Part 2: Understanding and Modifying the Gradle Build Script

In this section, we'll examine the `build.gradle` file, understand its structure, and make some modifications to explore Gradle's features.

### 2.1 Examine the default build.gradle file

Open the `build.gradle` file located in the `app` directory. This file defines how Gradle should build your project. Let's break it down:

1. Plugins:
   ```groovy
   plugins {
       id 'application'
   }
   ```
   This applies the 'application' plugin, which adds support for building a CLI application in Java.

2. Repositories:
   ```groovy
   repositories {
       mavenCentral()
   }
   ```
   This specifies that Gradle should use Maven Central to resolve dependencies.

3. Dependencies:
   ```groovy
   dependencies {
       testImplementation libs.junit.jupiter
       testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
       implementation libs.guava
   }
   ```
   This section defines the project dependencies, including JUnit for testing and Guava for the main application.

4. Java Toolchain:
   ```groovy
   java {
       toolchain {
           languageVersion = JavaLanguageVersion.of(21)
       }
   }
   ```
   This sets the Java version for the project.

5. Application Configuration:
   ```groovy
   application {
       mainClass = 'org.example.App'
   }
   ```
   This defines the main class for the application.

6. Test Configuration:
   ```groovy
   tasks.named('test') {
       useJUnitPlatform()
   }
   ```
   This configures Gradle to use JUnit Platform for running tests.

### 2.2 Modify the build.gradle file

Let's make some modifications to explore Gradle's features:

1. Add a new dependency:
   Add the following line in the `dependencies` block:
   ```groovy
   implementation 'org.apache.commons:commons-lang3:3.12.0'
   ```
   Explanation: We're adding Apache Commons Lang, a library of utility classes for Java. This library provides a host of helper utilities for handling strings, numbers, dates, concurrency, and more. It's widely used in Java projects to simplify common programming tasks.

2. Add a custom task:
   Add this at the end of the file:
   ```groovy
   task showProjectInfo {
       doLast {
           println "Project Name: $project.name"
           println "Java Version: ${java.toolchain.languageVersion.get()}"
           println "Main Class: ${application.mainClass.get()}"
       }
   }
   ```
   Explanation: This creates a custom Gradle task named 'showProjectInfo'. Custom tasks allow you to define project-specific operations. This task will print out some basic information about our project, demonstrating how to access project properties and create custom build logic.

3. Modify the Java version:
   Change the Java version to 17 (an LTS version) in the `java` block:
   ```groovy
   java {
       toolchain {
           languageVersion = JavaLanguageVersion.of(17)
       }
   }
   ```
   Explanation: This changes the Java version used by the project. We're switching to Java 17, which is a Long-Term Support (LTS) version. This demonstrates how Gradle can manage different Java versions for your project, ensuring consistency across different development environments.

After making these changes to the `build.gradle` file:

4. Save the file.

5. VS Code will detect the changes and show a prompt:
   "A build file was modified. Do you want to synchronize the Java classpath/configuration?"
   
   Click "Always" or "Yes" to this prompt.

   Explanation: This synchronization ensures that VS Code's Java extension updates its understanding of your project's classpath and configuration based on the changes you've made to the build file. This is crucial for features like code completion, error detection, and debugging to work correctly with your updated project structure and dependencies.

### 2.3 Run Gradle Tasks

Now, let's run some Gradle tasks to see our changes in action:

1. Open a terminal in VS Code:
   - Go to Terminal > New Terminal in the top menu

2. Run the build task:
   ```
   ./gradlew build
   ```
   This command will:
   - Compile your Java code
   - Run all tests
   - Create a JAR file of your application
   
   Expected output: You should see something similar to:
   ```
   Starting a Gradle Daemon...
   
   BUILD SUCCESSFUL in 24s
   7 actionable tasks: 7 executed
   ```

   Note: You might see a message about an invalid Java installation. This is usually not a problem for our lab, but if you encounter issues, you can run `./gradlew javaToolchains` for more details.

3. Run our custom task:
   ```
   ./gradlew showProjectInfo
   ```
   This will display the project information we defined in our custom task.
   
   Expected output:
   ```
   > Task :app:showProjectInfo
   Project Name: app
   Java Version: 17
   Main Class: org.example.App

   BUILD SUCCESSFUL in 1s
   1 actionable task: 1 executed
   ```

   Let's break down this output:
   
   - `> Task :app:showProjectInfo`: This indicates that our custom task is running.
   - `Project Name: app`: This is the name of our Gradle module.
   - `Java Version: 17`: This confirms that our project is set to use Java 17, as we specified in the build script.
   - `Main Class: org.example.App`: This is the fully qualified name of our application's main class.
   - `BUILD SUCCESSFUL in 1s`: This tells us that the task ran successfully and how long it took.
   - `1 actionable task: 1 executed`: This indicates that one task (our showProjectInfo task) was executed.

   This output confirms that:
   1. Our custom task is working correctly.
   2. Gradle is aware of our project structure and main class.
   3. The Java version is set as we intended.

   Understanding how to create and run custom Gradle tasks is valuable for automating various aspects of your build process and for quickly retrieving important project information.

### Conclusion

In this section, you've learned how to read and modify a Gradle build script. You've added dependencies, created custom tasks, and seen how Gradle manages the build process. These skills are fundamental to working with Gradle and will be essential as we move towards integrating with Azure Pipelines in the next sections.

## Part 3: Advanced Local Build and Debug

In this section, we'll explore advanced Gradle commands that are useful for troubleshooting, optimizing, and understanding your build process in detail.

### 3.1 Verbose Build Output
Run the build with more detailed output:
```bash
./gradlew build --info
```
Use this when: You need more information about the build process, such as task execution times, to identify performance bottlenecks or understand the build flow.

### 3.2 Debug Build Output
For even more detailed information:
```bash
./gradlew build --debug
```
Use this when: You're troubleshooting complex build issues or need to understand the internal workings of your build script, such as property evaluation or task dependencies.

### 3.3 Task Execution Graph
To see the task execution plan without actually performing the build:
```bash
./gradlew build --dry-run
```
Use this when: You want to understand the sequence of tasks that would be executed for a particular Gradle command, useful for diagnosing task ordering issues or understanding complex builds.

### 3.4 Detailed Build Logging and Analysis

For comprehensive local analysis of your build process, you can use Gradle's built-in logging and profiling features. These methods provide detailed information without sending data to external services.

1. Generate a build profile report:
```bash
./gradlew build --profile
```
This command runs the build and generates a detailed HTML report in the `build/reports/profile` directory. 

Use this when: You need a visual representation of task execution times and want to identify performance bottlenecks in your build.

2. Create a detailed build log file:
```bash
./gradlew build --debug > build_log.txt
```
This command runs the build with debug-level logging and saves the output to a text file.

Use this when: You need to analyze the step-by-step execution of your build, including fine-grained details about task execution, dependency resolution, and script evaluation.

**Caution**: Debug logs can be very large, especially for complex projects. Be prepared to manage large log files, and consider using log rotation or cleanup scripts if you frequently generate debug logs.

3. Use the built-in profiling options:
```bash
./gradlew build --scan --offline
```
This command enables build profiling features without publishing to the Gradle scan service.

Use this when: You want to leverage Gradle's advanced profiling capabilities without sharing data online. Note that some features may be limited in offline mode.

After running these commands, you can find the generated reports and logs in your project directory:
- The profile report will be at `build/reports/profile/profile-<timestamp>.html`
- The debug log will be in the `build_log.txt` file in your project root
- The offline scan data will be in the `build` directory

Analyzing these reports and logs can help you:
- Identify slow tasks or plugins
- Understand the sequence of task execution
- Debug issues with dependency resolution
- Optimize your build configuration for better performance

Remember to gitignore these files if you don't want to include them in your version control.

### Conclusion
These advanced build commands are essential tools in a developer's toolkit for several reasons:
1. Troubleshooting: They help identify and diagnose build failures or unexpected behavior.
2. Performance Optimization: By understanding task execution times and dependencies, you can optimize your build for speed.
3. Dependency Management: Clear insights into your project's dependency graph help manage complex dependency structures.
4. CI/CD Pipeline Setup: Understanding your build process in detail is crucial when setting up and optimizing CI/CD pipelines.

Mastering these commands will make you more effective at managing and optimizing Gradle builds, which is particularly valuable in large or complex projects.

## Part 4: Integrating with Azure Pipelines

### 4.1 Prepare Your GitHub Repository

1. Create a new repository on GitHub:
   - Navigate to https://github.com/ and sign in to your account
   - Click the '+' icon in the top right corner of the page and select 'New repository'
   - In the 'Repository name' field, enter 'gradle-azure-demo'
   - Choose to make your repository Public or Private
   - Click 'Create repository'

2. Update your local .gitignore file:
   - Open the .gitignore file in your project root directory
   - Replace its contents with the following:

     ```
     # Gradle files
     .gradle/
     build/

     # IDE files (VS Code)
     .vscode/

     # Compiled class files
     *.class

     # Log files
     *.log

     # Ignore build_log.txt
     build_log.txt

     # BlueJ files
     *.ctxt

     # Mobile Tools for Java (J2ME)
     .mtj.tmp/

     # Package Files #
     *.jar
     *.war
     *.nar
     *.ear
     *.zip
     *.tar.gz
     *.rar

     # virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
     hs_err_pid*

     # Ignore Gradle wrapper jar file
     !gradle-wrapper.jar

     # Ignore Gradle properties
     gradle.properties

     # Ignore build output
     out/

     # Ignore IntelliJ IDEA files (in case you switch to IntelliJ later)
     .idea/
     *.iml
     *.iws
     *.ipr

     # Ignore Eclipse files (in case you switch to Eclipse later)
     .classpath
     .project
     .settings/

     # Ignore macOS specific files
     .DS_Store

     # Ignore Windows specific files
     Thumbs.db

     # Ignore temporary files
     *.tmp
     *.bak
     *.swp
     *~.nib
     ```

   Explanation of key .gitignore entries:
   - `.gradle/` and `build/`: Ignore Gradle's temporary files and build outputs
   - `.vscode/`: Ignore VS Code specific settings
   - `*.class`: Ignore compiled Java class files
   - `*.log` and `build_log.txt`: Ignore log files to keep the repository clean
   - `!gradle-wrapper.jar`: Include the Gradle wrapper JAR, which is essential for CI/CD
   - `*.iml`, `.idea/`, `.classpath`, `.project`: Ignore IDE-specific files

   - Save the file

3. Initialize and push your local project to GitHub:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/your-username/gradle-azure-demo.git
   git push -u origin main
   ```
   Replace 'your-username' with your GitHub username.

4. Verify the push:
   - Refresh your GitHub repository page
   - You should see your project files, including the updated .gitignore

### 4.2 Set up Azure DevOps Project and Pipeline

1. Create a new Azure DevOps project:
   - Go to https://dev.azure.com/ and sign in (or create an account if you don't have one)
   - Click on "New project" in the top right corner
   - Enter a project name (e.g., "Gradle Azure Demo")
   - Set visibility to "Private"
   - Click "Create"

2. Set up Azure Pipelines:
   - In your new Azure DevOps project, click on "Pipelines" in the left sidebar
   - Click on "Create Pipeline"

3. Connect to GitHub:
   - On the "Where is your code?" page, select "GitHub"
   - You may need to authorize Azure Pipelines to access your GitHub account
     - Click "Authorize Azure Pipelines"
     - You might need to confirm your GitHub password
     - Select the repositories you want to allow Azure Pipelines to access, or select "All repositories"
     - Click "Save"

4. Select your repository:
   - Once authorized, you'll see a list of your GitHub repositories
   - Find and select the "gradle-azure-demo" repository you created earlier

5. Configure your pipeline:
   - On the "Configure your pipeline" page, select "Starter pipeline"
   - This will create a basic azure-pipelines.yml file

6. Review the pipeline YAML:
   - Azure DevOps will display the contents of the azure-pipelines.yml file
   - For now, leave it as is; we'll modify it in the next step

7. Save and run:
   - Click on the "Save" button (Not 'Save and Run'). We don't want to run a pipeline yet. 
   - In the pop-up window, leave the default commit message or customize it
   - Ensure "Commit directly to the main branch" is selected
   - Click "Save"

8. View pipeline execution:
   - Azure Pipelines will now create the YAML file in your GitHub repository

This process creates a new Azure DevOps project and sets up a basic pipeline connected to your GitHub repository. In the next section, we'll modify this pipeline to work specifically with our Gradle project.

### 4.3 Configure the Azure Pipeline

1. In your Azure DevOps project, go to Pipelines and select your pipeline
2. Click "Edit" to modify the azure-pipelines.yml file
3. Replace the entire content with the following:

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: Gradle@3
  inputs:
    workingDirectory: ''
    gradleWrapperFile: 'gradlew'
    gradleOptions: '-Xmx3072m'
    javaHomeOption: 'JDKVersion'
    jdkVersionOption: '17'
    jdkArchitectureOption: 'x64'
    publishJUnitResults: true
    testResultsFiles: '**/TEST-*.xml'
    tasks: 'build'

- task: Gradle@3
  inputs:
    workingDirectory: ''
    gradleWrapperFile: 'gradlew'
    gradleOptions: '-Xmx3072m'
    javaHomeOption: 'JDKVersion'
    jdkVersionOption: '17'
    jdkArchitectureOption: 'x64'
    tasks: 'showProjectInfo'
```

4. Click "Validate and Save" and then "Save" to apply the changes.

5. Click "Run" to run a pipeline. Observe the build and test results in Azure Pipelines.

6. View Pipeline Results:
   - After the pipeline run completes, click on the run number to view detailed results
   - Explore the 'Summary', 'Jobs', and 'Tests' tabs to see different aspects of the build
   - In the 'Jobs' tab, you can see a detailed log of each step in the pipeline
   - The 'Tests' tab will show results of your JUnit tests
     
   Note: You may see a warning message like this:
   ```
   ##[warning]Used 'chmod' method for gradlew file to make it executable.
   ```
   This is normal and not a cause for concern. Azure Pipelines is automatically making the Gradle wrapper script executable, which is necessary for the build to run on the Linux-based build agent. This is equivalent to running `chmod +x gradlew` on a Unix-like system.

   
Explanation of changes and configuration:

- `trigger: [main]`: This setting causes the pipeline to run whenever changes are pushed to the main branch.
- `pool: vmImage: 'ubuntu-latest'`: This specifies that the pipeline will run on the latest Ubuntu virtual machine image.
- The first `Gradle@3` task:
  - Runs the `build` task, which compiles the code, runs tests, and creates the project's output.
  - Uses Java 17 (`jdkVersionOption: '17'`), matching our project setup.
  - Publishes JUnit test results, making them visible in Azure DevOps.
- The second `Gradle@3` task:
  - Runs our custom `showProjectInfo` task that we created earlier.
  - Also uses Java 17 for consistency.

This configuration ensures that:
1. Our project is built and tested on every push to the main branch.
2. We're using the correct Java version (17) that matches our project setup.
3. Test results are published and easily viewable
4. Our custom task `showProjectInfo` is run, which can help verify project settings in the pipeline environment.

After saving and running this pipeline, you should see two stages in your pipeline execution: one for building and testing your project, and another for displaying the project information.

### Conclusion
By integrating your Gradle project with Azure Pipelines, you've automated the build and test process in a cloud environment. This setup ensures that your project is built and tested consistently, regardless of the developer's local environment. The pipeline you've created runs your Gradle build, executes tests, and even runs your custom task, demonstrating how local development practices can be mirrored in a CI/CD pipeline. This integration is a key step in implementing DevOps practices, enabling faster and more reliable software delivery.

## Overall Conclusion
In this lab, you've walked through the entire process of setting up a Java project with Gradle, from local development to CI/CD integration. You've learned how to:
1. Set up a Gradle project structure
2. Customize the build process
3. Run builds and tests locally
4. Integrate with Azure Pipelines for automated builds

These skills form the foundation of modern build automation and are crucial for implementing efficient DevOps practices. By automating the build and test process, you can catch issues earlier, ensure consistency across environments, and speed up the development cycle.

## Troubleshooting

Here are some common issues you might encounter and how to resolve them:

1. Gradle wrapper permission issues on Unix-like systems:
   - Error: "Permission denied"
   - Solution: Run `chmod +x gradlew` to make the wrapper executable

2. Java version mismatch:
   - Error: "Unsupported class file major version"
   - Solution: Ensure your project's Java version matches the version specified in `build.gradle`

3. Dependencies not resolving:
   - Error: "Could not resolve all dependencies"
   - Solution: Check your internet connection and ensure all repositories are accessible

4. Azure Pipelines failing to connect to GitHub:
   - Error: "Unable to connect to GitHub"
   - Solution: Verify your GitHub credentials and Azure Pipelines service connection

## Glossary

- Gradle: An open-source build automation tool
- JDK: Java Development Kit
- Azure Pipelines: A cloud service that automatically builds and tests code projects
- CI/CD: Continuous Integration/Continuous Deployment
- YAML: A human-readable data serialization format used for configuration files

## Additional Resources
- [Gradle User Manual](https://docs.gradle.org/current/userguide/userguide.html)
- [Azure Pipelines Documentation](https://docs.microsoft.com/en-us/azure/devops/pipelines/?view=azure-devops)
- [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Gradle Application Plugin Documentation](https://docs.gradle.org/current/userguide/application_plugin.html)
- [Azure DevOps Pricing Information](https://azure.microsoft.com/en-us/pricing/details/devops/azure-devops-services/)
