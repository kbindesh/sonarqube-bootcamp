# Install and Configure the SonarQube server

- SonarQube official download page link: https://www.sonarsource.com/products/sonarqube/downloads/

## Step-00: Prerequisites

1. **Java** (JDK 17 or above)

   ```
   # Switch to root user
   sudo su -

   # Install Java
   yum install -y java-17-amazon-corretto.x86_64
   ```

2. A Machine (virtual/physical) with atleast **2 GB RAM**.

   [In this lab, we will setup sonarqube on EC2 instance]

3. **Storage**: 20GB (recommended)

## Step-xx: Create an EC2 Instance for SonarQube Server

## Step-xx: Installing the SonarQube server

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

## Step-02: Create a new user for sonarqube actions

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

## Step-xx: Start the SonarQube service

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

## Step-xx: Access the SonarQube web dashboard

- Launch the browser and navigate to following link:
  **http://<public_ip_or_dns_of_sonarqube_server>:9000**

- You will see the message _SonarQube is starting_ followed by a login page.

- Sign-in to SonarQube with default credentials:
  - **Username**: admin
  - **Password**: admin

## Step-xx: Create a new SonarQube Project

- Click on Projects tab >> Add Project

  - Project Key: maven-project
  - Display Name: maven-project

- Generate a Token

  - Enter a name for your token: maven-project >> Click on **Generate** button >> **Continue**.

- **Run analysis on your Project** >> Select **Maven**

## Step-xx: Initialize/Clone the application code for review (manually)

## Step-xx: Scan the code with Maven & SonarQube

## Step-xx: SonarQube integration with Jenkins
