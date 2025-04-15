# Installing `SonarScanner` CLI on Windows

## Step-01: Download SonarScanner for Windows
 
- Download the Windows version of SonarScanner (e.g., sonar-scanner-cli-4.6.2.2472-windows.zip).
- Extract the ZIP File
  - Once the it is downloaded, extract it to a location of your choice, for example, C:\binaries.

## Step-02: Add SonarScanner to your System path

- To make SonarScanner accessible from the command line:
  1. Navigate to Start Menu and search for **Environment Variables** >> Systems Variables >> click Edit.
  5. Click New and add the path to your SonarScanner bin directory (e.g., C:\binaries\sonar-scanner\bin).
  6. Click Save >> Ok & Exit the Environment Variables window.

## Step-03: Verify Installation

- Open a new Command Prompt window and run:

```
sonar-scanner -v
```
