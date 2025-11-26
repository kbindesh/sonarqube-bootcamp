# Install and Configure the SonarQube server

- SonarQube official download page link: https://www.sonarsource.com/products/sonarqube/downloads/

## Prerequisites

1. [**Java | JDK 17 or above**](https://github.com/kbindesh/maven-bootcamp/blob/main/Module-02_Setting_up_Maven_Environment/01-setup-mvn-on-amzn-linux-2.md)

2. [**Maven**](https://github.com/kbindesh/maven-bootcamp/blob/main/Module-02_Setting_up_Maven_Environment/01-setup-mvn-on-amzn-linux-2.md)

3. A Machine (virtual/physical) with atleast **2 GB RAM**.

   [In this lab, we will setup sonarqube on EC2 instance]

4. **Storage**: 20GB (recommended)

## Step-01: Create an EC2 Instance for SonarQube Server

- Sign-in to AWS Account (https://console.aws.amazon.com/).
- Navigate to EC2 service >> **Launch Instance**.
- **Name**: sonarqube-server
- **AMI**: Amazon Linux 2 (Kernel 5.10)
- **Instance Type**: t2.small
- **Key Pair**: <create_new_keypair>/<existing>
- **VPC/Subnet**: Default
- **Elastic IP**: Enable
- **Security Group**: <create_new_sg>
  - **Ingress**: Allow SSH (22), 9000
- **Storage**: 20 GB, GP2
- Click on **Launch Instance** button

## Step-02: Installing the SonarQube server

- Connect to SonarQube server created in the preceding step and execute following commands:

```
# Change the directory to opt
cd /opt

# Download the SonarQube binaries
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.5.90363.zip

# Extract the downloaded *.zip file
unzip sonarqube-9.9.5.90363.zip

ls -l sonarqube-9.9.5.90363/
[The preceding command will list contents of sonarqube home directory]
```

## Step-03: Create a new user for sonarqube actions

- SonarQube uses elasticsearch engine which doesn't work with root user previlages and requires a dedicated user.

- In this section, we will create a new user on sonarqube server.

```
# Create a new user for SonarQube
useradd sonaradmin

# Change the ownership of /opt/sonarqube-9.9.5.90363 directory to "sonaradmin" user
chown -R sonaradmin:sonaradmin /opt/sonarqube-9.9.5.90363

# To confirm the ownership change, simply list the sonarqube home directory
ls -l /opt/sonarqube-9.9.5.90363
```

## Step-04: Start the SonarQube service

```
# Switch to sonaradmin user
su - sonaradmin

# Start the Sonar service
cd /opt/sonarqube-9.9.5.90363/bin/linux-x86-64/

./sonar.sh start

# To check & confirm the sonarqube service status
./sonar.sh status

[The preceding command should say that "SonarQube is running"]

# To check on which port sonarqube is running
netstat -tulpn

[The preceding command will show you one java service running on port 9000]
```

## Step-05: Access the SonarQube web dashboard

- Launch the browser and navigate to following link:
  **http://<public_ip_or_dns_of_sonarqube_server>:9000**

- You will see the message _SonarQube is starting_ followed by a login page.

- Sign-in to SonarQube with default credentials:

  - **Username**: admin
  - **Password**: admin

- You will be prompted to reset the password by entering new password.

## Step-06: Create a new SonarQube Project

- Click on **Projects** tab >> **Add Project**

  - **Project Key**: maven-project
  - **Display Name**: maven-project

- **Generate a Token**

  - Enter a name for your token: maven-project >> Click on **Generate** button >> **Continue**.

- **Run analysis on your Project** >> Select **Maven**

## Step-07: Clone application code (java) for review (manually)

```
git clone https://github.com/kbindesh/mvn-lab-project.git
```

## Step-08: Review the code with Maven & SonarQube

- Analyzing a Maven project consists of running a Maven goal: **sonar:sonar** from the directory that holds the main project _pom.xml_.
- You need to pass an authentication token using the **sonar.login** property in your command line.

```
mvn clean verify sonar:sonar \
  -Dsonar.projectKey=sonarqube-project \
  -Dsonar.host.url=http://<public_ip_of_sonarqube_server>:9000 \
  -Dsonar.login=sqp_a3d1960230b2423fe059f34434941d8d1559dc0b
```

- Ref: https://docs.sonarsource.com/sonarqube/9.8/analyzing-source-code/scanners/sonarscanner-for-maven/
  s
