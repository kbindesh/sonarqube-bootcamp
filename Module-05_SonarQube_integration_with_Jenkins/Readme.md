# Lab: Integrate SonarQube with Jenkins pipeline

## Lab Overview

- In this lab, you will learn how to integrate `SonarQube` with `Jenkins pipeline` to automate the code review process.
- Lab comprises of following components:
  - **Cloud Platform**: Amazon Web Services
  - **Maven**: Project Orchestrator (Java)
  - **GitHub**: SCM
  - **Jenkins**: Automation server
  - **SonarQube**: Static Code Analysis Tool

## `Prerequisites` for reviewing Maven project on Jenkins server

- [Jenkins server](https://github.com/kbindesh/jenkins-masterclass/blob/main/Module-03_Setting_up_Jenkins/01-jenkins-on-amazon-linux/Readme.md)

- [Maven](https://github.com/kbindesh/maven-bootcamp/blob/main/Module-02_Setting_up_Maven_Environment/01-setup-mvn-on-amzn-linux-2.md)

- Git (in case not installed - sudo dnf install -y git)

## Step-02: Install necessary `Jenkins Plugins`

- Open Jenkins Dashboard >> **Manage Jenkins** >> **Plugins** >> **Available Plugins**

- Install following Jenkins plugins:

  1. **Maven Invoker**
  2. **Maven Integration**
  3. **Github**
  4. **Pipeline: Stage View**

## Step-03: Configure Global Config Settings for `Java` & `Maven` on Jenkins

- Jenkins Dashboard >> **Manage Jenkins** >> **Tools** >>

  ```
  # Under "JDK installations" section
  Name: Java-17
  JAVA_HOME: /usr/lib/jvm/java-17-amazon-corretto.x86_64

  # Under "Git installations" section
  Name: Default
  Path to git executable: /usr/bin/git

  # Under "Maven installations" section
  Name: Maven-3.9.8
  MAVEN_HOME: /opt/apache-maven-3.9.8
  ```

## Step-XX: Develop a new Maven Project

- Launch VS Code (IDE) >> Create a new project folder and open it in the VS Code >> **View** menu >> **Terminal**

  ```
  # Run the following maven command to create a new sample maven project
  mvn archetype:generate "-DgroupId=com.mycompany.app" "-DartifactId=my-app" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DarchetypeVersion=1.5" "-DinteractiveMode=false"
  ```

- Get inside the project folder:

  ```
  cd my-app
  ```

  - The **src/main/java** directory contains the project source code
  - The **src/test/java** directory contains the test source
  - The **pom.xml** file is the project's Project Object Model, or POM.

## Step-XX: Compile and Build the Maven Project

```
# Validate Maven project
mvn validate

# Compile Maven project
mvn compile

# To see the output of your application, run the compiled version of app (.class) present in target folder
java '-XX:+ShowCodeDetailsInExceptionMessages' '-cp' 'target\classes' 'com.mycompany.app.App'

# Now, package the maven application in a .jar file
mvn package

# Finally, test the app by executing the app build (.jar)
java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App

[The preceding command should display the "Hello World" message without any errors]
```

## Step-XX: Create a new GitHub repo and Push the Maven project to it

## Step-XX: Create a new `SonarQube Cloud Account`

- Navigate to https://www.sonarsource.com/products/sonarcloud/signup/ >> Select **GitHub** >> Enter your GitHub credentials to login.

## Step-XX: Create `SonarQube Organization` and `Project`

### Create a new `SonarQube Organization`

- Sign-in to SonarQube Cloud Account (https://sonarcloud.io/login)
- From the right-top corner, click on **+** button >> **Create New Organization** >> create one manually.
  - **Organization Name**: bin_project_org
  - **Plan**: Free

### Create a new `SonarQube Project`

- From the right-top corner, click on **+** button >> select **Analyze New Project** >> Click **create a project manually** link.
  - **Organization**: bin_project_org
  - **Project Name**: bin_project
  - **Project visibility**: Public
  - **The new code for this project will be based on**: Previous version

## Step-XX: Generate a new SonarQube Authentication Token

- Sign-in to your SonarQube Account >> Click on your user drop-down list (top-right corner) >> **My Account**
- Select **Security** tab >> click on **Generate Token** button
  - Name: jenkins
- Copy the generated token and store at save place as we'll need it in our next step.

## Step-XX: Save SonarQube Account token on Jenkins server

- Jenkins Dashboard >> **Manage Jenkins** >> **Credentials** >> **System** >> **Global credentials (unrestricted)**
- Click on New Credentials button
  - **Kind**: Secret Text
  - **Scope**: Global
  - **Secret**: <paste_the_sonarqube_token_generated_in_last_step>
  - **ID**: sonarqube
- Click on **Create** button

## Step-XX: Save SonarQube Server details on Jenkins server

- Jenkins Dashboard >> **Manage Jenkins** >> **System**.
- Scroll down all the way to **SonarQube server section** (thanks to sonarqube scanner plugin).
- Click on **Add SonarQube** button
  - Name: sonarqube-server
  - Server URL: https://sonarcloud.io
  - **Server Authentication Token**: <select_sonar_token_we_created_earlier>
- Click **Save** button

## Step-XX: `Install SonarScanner plugin` on Jenkins server

- Jenkins Dashboard >> **Manage Jenkins** >> **Plugins**
- Select Available plugins tab >> serach for **SonarQube scanner** >> _Select_ and _Install_

## Step-XX: Setup `SonarScanner CLI for Maven` on Jenkins server

- **Ref**
  - https://docs.sonarsource.com/sonarqube-server/8.9/analyzing-source-code/scanners/sonarscanner/
  - https://docs.sonarsource.com/sonarqube-server/8.9/analyzing-source-code/scanners/sonarscanner-for-maven/

### Download and Configure SonarScanner

- Jenkins Dashboard >> **Manage Jenkins** >> **Tools** >> Scroll to **SonarQube Scanner** section
- Click on **Add SonarQube Scanner** button
  - **Name**: sonar-scanner
  - **Install Automatically**: Enable
  - **Version**: <select_lastet_version>
- Click on **Apply & Save**

### Set the sonar-scanner location (env variable)

- Open your shell's configuration file, ~/.bash_profile and add a line to the file to append the SonarScanner's bin directory to the PATH variable e.g. /opt/sonar-scanner

```
export PATH="$PATH:/opt/sonar-scanner/bin"

[Replace /opt/sonar-scanner/bin with the actual path to your SonarScanner bin directory]

# Reload the config
source ~/.bash_profile

# Check Sonarscanner version
sonar-scanner --version
```

## Step-XX: Create a `Jenkinsfile` for the Maven project

- Add SonarQube stage to the Jenkinsfile

  - Search Jenkins extension for SonarQube >> https://docs.sonarsource.com/sonarqube-server/latest/analyzing-source-code/scanners/jenkins-extension-sonarqube/

    ```
    pipeline {
      agent any

      tools {
        jdk 'Java-17'
        maven 'Maven-3.9.8'
      }

      stages {
        stage('Clean and Build the Application'){
          steps{
            sh 'mvn clean package'
          }
        }

        stage('SonarCloud analysis') {
            steps{
              withSonarQubeEnv('sonarqube-server') {
                sh 'mvn clean package sonar:sonar -Dsonar.projectKey=bin-project-org_bin-project -Dsonar.projectName=bin_project -Dsonar.organization-bin_project_org -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=cacf9e1489915183a5a1695356b859106f3f8a7f -X'
              }
            }
        }
      }
    }
    ```

## Step-XX: Create a sonar-project.properties file

- On your local system, Visual Studio Code >> Open your Maven (java) project >> Create a new file **sonar-project.properties**

```
sonar.verbose=true
sonar.organization=bin_project_org
sonar.projectKey=bin-project-org_bin-project
sonar.projectName=bin_project
sonar.language=java
sonar.sourceEncoding=UTF-8
sonar.sources=.
sonar.java.binaries=target/classes
sonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
```

## Step-XX: Push the Maven project and other config files to GitHub

- From you local system, commit the maven app code and Jenkinsfile updates and push it to GitHub:

```

git add .

git commit -m "Created Maven Project and Jenkinfile v0.1"

git remote add origin <your_github_repo_url_here>

git remote -v

git push -u origin <branch_name>

```

- To verify the code push, switch to your GitHub repo and validate it.

## Step-XX: `Configuring SonarScanner for Maven` on Jenkins server

- Ref: https://docs.sonarsource.com/sonarqube-server/9.6/analyzing-source-code/scanners/sonarscanner-for-maven/

- Edit the **settings.xml** file, located in <MAVEN_HOME>/conf or ~/.m2, to set the plugin prefix and optionally the SonarQube server URL.

```

<settings>
    <pluginGroups>
        <pluginGroup>org.sonarsource.scanner.maven</pluginGroup>
    </pluginGroups>
    <profiles>
        <profile>
            <id>sonar</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <sonar.host.url>
                  http://sonarcloud.io
                </sonar.host.url>
            </properties>
        </profile>
     </profiles>
</settings>
```

- Now, analyze and review the app locally.

- Analyzing a Maven project consists of running a Maven goal: `sonar:sonar` from the directory that holds the main project **pom.xml**.

```
# To Analyze a maven project using sonarqube
mvn clean verify sonar:sonar


# (optional) To run the sonar:sonar goal as a dedicated step
mvn clean install
mvn sonar:sonar -Dsonar.login=myAuthenticationToken

# (optional) To specify the version of sonar-maven-plugin instead of using the latest
mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar

```

## Step-XX: Create a new GitHub Webhook for triggering Jenkins Job automatically

## Step-XX: Create & Run Jenkins Job

- Navigate to Jenkins server Dashboard >> **New Item**
  - **Name**: sonarqube-pipeline
  - **Project Type**: Pipeline

## Step-XX: Check the code review report from SonarQube web portal

- Launch SonarQube cloud dashboard >> Select the project >> You will see the analyzed report
