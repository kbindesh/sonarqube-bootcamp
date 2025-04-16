# Static Code Analysis using SonarQube (Cloud) on Win 11

## Overview

- In this article, we will learn how to use SonarQube for performaing static code analysis of a Maven project.
- We will integrate SonarScanner for Maven Project for analyzing a Java project in SonarQube?.

## Prerequisites

- Windows 11 system (with admin rights)
- [Visual Studio Code](https://code.visualstudio.com/download)
- [Java (JDK 17 or above)](https://www.oracle.com/in/java/technologies/downloads/#java17-windows)
- [Maven](https://maven.apache.org/download.cgi)
- Internet Connectivity

## Step-01: Create a new Maven project

- Launch VS Code (IDE) >> Create a new project folder and open it in the VS Code >> **View** menu >> **Terminal**

  ```
  mvn archetype:generate "-DgroupId=com.mycompany.app" "-DartifactId=my-app" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DarchetypeVersion=1.5" "-DinteractiveMode=false"
  ```

- Get inside the project folder:

  ```
  cd my-app
  ```

  - The **src/main/java** directory contains the project source code
  - The **src/test/java** directory contains the test source
  - The **pom.xml** file is the project's Project Object Model, or POM.

## Step-02: Compile and Build the Maven Project

```
# Validate the Maven project
mvn validate

# Compile the Maven project
mvn compile

# To see the output of your application
java '-XX:+ShowCodeDetailsInExceptionMessages' '-cp' 'target\classes' 'com.mycompany.app.App'

# Package the Maven App
mvn package

# See the output of App using App build (.jar)
java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App
```

## Step-03: `Setup SonarScanner CLI`

- Official SonarScanner CLI Download page: <br/> https://docs.sonarsource.com/sonarqube-server/10.4/analyzing-source-code/scanners/sonarscanner/

- Download the **Windows x64** version of SonarScanner CLI and extract it.

- Copy the extracted folder, rename it to **sonar-scanner** and move it somewhere in the C: drive. Here, I've created a folder **Binaries** (C:\Binaries)

- The new location of sonar-scanner CLI app: C:\Binaries\sonar-scanner

  ```
  # Check the sonar-scanner version
  sonar-scanner -v

  [NOTE: In case VS Code is not recognizing the sonar-scanner, close it and reopen]
  ```

## Step-04: `Set the Path of SonarScanner` on your System

- Start menu >> **Edit the system environment variables** >> Advanced tab >> Click **Environment Variables** button >> Under **System Variables** section
- Select **Path** variable & click **Edit** >> Paste the SonarScanner's bin directory location (e.g. C:\Binaries\sonar-scanner\bin) >> Click Ok >> Ok.

## Step-05: Add SonarQube Configuration to Maven `setting.xml`

- Open your **settings.xml** file located in <MAVEN_HOME>/conf using a text editor and add the following sonarqube releated settings:

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
                  https://sonarcloud.io
                </sonar.host.url>
            </properties>
        </profile>
    </profiles>
</settings>
```

## Step-06: Create a new SonarQube Cloud Account

- Navigate to https://www.sonarsource.com/products/sonarcloud/signup/ >> Select **GitHub** >> Enter your GitHub credentials to login

## Step-07: Create `SonarQube Organization` and `Project`

### 7.1 Create a new `SonarQube Organization`

- Sign-in to SonarQube Cloud Account (https://sonarcloud.io/login)
- From the right-top corner, click on **+** button >> **Create New Organization** >> create one manually.
  - **Organization Name**: bin_project_org
  - **Plan**: Free

### 7.2 Create a new `SonarQube Project`

- From the right-top corner, click on **+** button >> select **Analyze New Project** >> Click **create a project manually** link.
  - **Organization**: bin_project_org
  - **Project Name**: bin_project
  - **Project visibility**: Public
  - **The new code for this project will be based on**: Previous version

## Step-08: Generate a new SonarQube Authentication Token

- SonarQube Cloud Account >> From top-right corner, click user dropdown list and select **My Account** >> **Security** tab

  - **Token Name**: jenkins

- TIP: Keep this generated auth token at safe place; will need it in the next step.

## Step-09: Create a `sonar-project.properties` file

- VS Code - Maven Project folder >> Create a new file **sonar-project.properties** in the root of the maven project folder.

```
sonar.verbose=true
sonar.projectKey=bin-project-org_bin-project
sonar.organization=bin-project-org
sonar.projectName=bin_project
sonar.language=java
sonar.sourceEncoding=UTF-8
sonar.sources=.
sonar.java.binaries=target/classes
```

## Step-10: Update the `sonarscanner.properties file`

- On your system, open your sonarscanner's conf folder (e.g. C:\Binaries\sonar-scanner)

- Edit sonar-scanner.properties as follows:

```
# Replace below sonarcloud auth token with yours
sonar.host.url=https://sonarcloud.io
sonar.login=cacf9e1485a1695356b859106f3f8a7f
```

## Step-11: Update the POM.xml file for sonarqube

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>my-app</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.release>17</maven.compiler.release>
  </properties>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.junit</groupId>
        <artifactId>junit-bom</artifactId>
        <version>5.11.0</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!-- https://mvnrepository.com/artifact/org.sonarsource.scanner.maven/sonar-maven-plugin -->
      <dependency>
          <groupId>org.sonarsource.scanner.maven</groupId>
          <artifactId>sonar-maven-plugin</artifactId>
          <version>3.11.0.3922</version>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <dependencies>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <scope>test</scope>
    </dependency>
    <!-- Optionally: parameterized tests support -->
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-params</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <!-- clean lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#clean_Lifecycle -->
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.4.0</version>
        </plugin>
        <!-- default lifecycle, jar packaging: see https://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_jar_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.3.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.13.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>3.3.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-jar-plugin</artifactId>
          <version>3.4.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>3.1.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>3.1.2</version>
        </plugin>
        <!-- site lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#site_Lifecycle -->
        <plugin>
          <artifactId>maven-site-plugin</artifactId>
          <version>3.12.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-project-info-reports-plugin</artifactId>
          <version>3.6.1</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>

```

## Step-12: Analyze the Maven project code using SonarQube

```
sonar-scanner

mvn package sonar:sonar '-Dsonar.projectKey=bin-project-org_bin-project' '-Dsonar.organization=bin-project-org' '-Dsonar.projectName=bin_project' '-Dsonar.host.url=https://sonarcloud.io' '-Dsonar.token=cacf9e1485a1695356b859106f3f8a7f' -X
```
