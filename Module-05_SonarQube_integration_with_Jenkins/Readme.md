# Integrate Jenkins wit SonarQube Server

## Prerequisites

- [A Jenkins server](https://github.com/kbindesh/jenkins-masterclass/tree/main/Module-03_Setting_up_Jenkins/02-Jenkins_on_EC2_Amzn_Linux)
- [A SonarQube server](https://github.com/kbindesh/sonarqube-bootcamp/blob/main/Module-02_Setup_SonarQube/01-setup-sonarqube-on-linux.md)
- [Maven on Jenkins server](https://github.com/kbindesh/maven-bootcamp/blob/main/Module-02_Setting_up_Maven_Environment/01-setup-mvn-on-amzn-linux-2.md)

## Step-01: Setup SonarQube server

### Step-1.1: Create a new SonarQube Project

- Navigate to SonarQube dashboard (web) >> Sign-in.
- Click **Projects** tab >> **Add Project** >> Manuallyss

  - **Project display name**: binapp-projecst
  - **Project key**: binapp-project

- How do you want to analyze your repository?: Locally

- **Generate a Token**

  - **Name of Token**: binapp-project >> **Generate**

    [Copy the token and store it at safe place. We'll need it in later steps]

### Step-1.2: Configure Quality Profile

## Step-02: Setup Jenkins Server

### Step-2.1: Install SonarQube plugin

- Navigate to Jenkins server Dashboard >> **Manage Jenkins** >> **Plugins**.
- Select **Available** tab >> Search for **SonarQube Scanner** plugin >> **Install**.

### Step-2.2: Configure SonarQube credentials

- Navigate to Jenkins server Dashboard >> **Manage Jenkins** >> Credentials.
- Click on **Jenkins** store >> Global Credentials (unrestricted) >> Add Credentials
  - **Kind**: Secret Text
  - **Scope**: Global
  - **Secret**: <your_sonarqube_project_token>
  - **ID**: sonarqube-token
  - **Discription**: sonarqube-token

### Step-2.3: Add SonarQube server details to Jenkins Server

- Navigate to Jenkins server Dashboard >> **Manage Jenkins** >> Configure System
- Scroll down to SonarQube Servers section >> Click Add SonarQube button
  - **Name**: sonarqube
  - **Server URL**: http://<public_ip_of_sonarqube_server>:9000/
  - **Server Authentication Token**: [Select the token we created in the preceding command]
- Click Apply & Save

### Step-2.4: Install SonarScanner

- Navigate to Jenkins server Dashboard >> **Manage Jenkins** >> Global Tools Configuration
- Scroll down to **SonarQube Scanner** section
  - Click on **Add SonarQube Scanner** button
  - **Name**: SonarQubeScanner
  - **Install Automatically**: Enable
- Click Apply & Save

## Step-03: Clone application code (java) for review (manually)

```
git clone https://github.com/kbindesh/mvn-lab-project.git
```

## Step-04: Review the code with Maven & SonarQube

- Analyzing a Maven project consists of running a Maven goal: **sonar:sonar** from the directory that holds the main project _pom.xml_.
- You need to pass an authentication token using the **sonar.login** property in your command line.

```
mvn clean verify sonar:sonar \
  -Dsonar.projectKey=sonarqube-project \
  -Dsonar.host.url=http://<public_ip_of_sonarqube_server>:9000 \
  -Dsonar.login=sqp_a3d1960230b2423fe059f34434941d8d1559dc0b
```

- Ref: https://docs.sonarsource.com/sonarqube/9.8/analyzing-source-code/scanners/sonarscanner-for-maven/

## Step-05: Create & Run Jenkins Pipeline Job (automatically)

- Navigate to Jenkins server Dashboard >> **New Item**
  - **Name**: sonarqube-pipeline
  - **Project Type**: Pipeline

## Step-06: Check the code review report from SonarQube web portal

- Launch SonarQube dashboard >> Sign-in
