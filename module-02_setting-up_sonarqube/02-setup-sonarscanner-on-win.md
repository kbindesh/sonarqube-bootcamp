# Install and Configure `SonarScanner` CLI on Windows 11

## Step-01: Download SonarScanner for Windows

- Download the Windows version of SonarScanner (e.g., sonar-scanner-cli-4.6.2.2472-windows.zip).
- Extract the ZIP File
  - Once the it is downloaded, extract it to a location of your choice, for example, C:\binaries.

## Step-02: Add SonarScanner to your System path

- To make SonarScanner accessible from the command line:
  1. Navigate to Start Menu >> **Edit the System Environment Variables**
  2. Select **Advanced** tab >> Click on **Environment Variables** button >> Under **Systems Variables** section >> Select Path & click Edit
  3. Click **New** button and paste your sonar-scanner bin directory location (e.g. C:\binaries\sonar-scanner\bin) >> Click **Ok** button.

## Step-03: Verify Installation

- Open a new Command Prompt window and run:

  ```
  sonar-scanner -v
  ```
