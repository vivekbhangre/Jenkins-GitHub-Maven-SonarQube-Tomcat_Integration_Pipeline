# Jenkins Declarative CI/CD Pipeline Project

This repository demonstrates an automated Continuous Integration and Continuous Deployment (CI/CD) workflow using a **Jenkins Declarative Pipeline**. The pipeline automatically fetches code from GitHub, builds and packages a Java web application, performs static code analysis with SonarQube, and deploys the artifact onto an Apache Tomcat server.

---

## 🚀 Pipeline Architecture Workflow

1. **Source Code Management:** Pulls the latest code from the GitHub repository on code changes.
2. **Build Stage:** Uses **Apache Maven** to compile code and package it into a deployable `.war` file.
3. **Code Quality Review:** Scans code vulnerabilities, bugs, and code smells via **SonarQube**.
4. **Deployment Stage:** Automates deployment of the `.war` application to the target **Tomcat Server**.

---

## 🛠️ Prerequisites & Prerequisites Setup

Ensure the following tools are installed, configured, and accessible by your Jenkins instance:

### 1. Jenkins Plugins Required
Go to `Manage Jenkins` > `Plugins` > `Available Plugins` and install:
* **Git Plugin**
* **Pipeline**
* **SonarQube Scanner for Jenkins**
* **Deploy to container Plugin** (or context-equivalent script block)

### 2. Global Tool Configuration
Set up the following tools under `Manage Jenkins` > `Tools`:
* **JDK:** Configured with your appropriate version (e.g., Java 11 or 17).
* **Maven:** Configured and named to match the `Jenkinsfile` (e.g., `Maven-3.9`).

### 3. Credentials Configuration
Go to `Manage Jenkins` > `Credentials` and add:
* **Tomcat Manager Credentials:** `Username with password` for the Tomcat GUI/text manager role (`manager-script`). Give it an ID like `tomcat-manager-creds`.

---

## 🔧 SonarQube & Tomcat Configurations

### SonarQube Integration
1. Generate an authentication token in SonarQube (`My Account` > `Security` > `Tokens`).
2. Add this token in Jenkins credentials as a `Secret text`.
3. Configure your SonarQube server in `Manage Jenkins` > `System` > `SonarQube servers`. Name it exactly `SonarQube-Server`.

### Tomcat Configuration
Ensure your `tomcat-users.xml` contains the appropriate deployment roles:
```xml
<role rolename="manager-script"/>
<user username="admin" password="yourpassword" roles="manager-script"/>
```

---

## 🏃 How to Run the Pipeline

1. Create a new **Pipeline** job in Jenkins.
2. Under the **Pipeline** section, set the Definition to **Pipeline script from SCM**.
3. Select **Git**, provide this repository URL, and specify your branch (e.g., `*/main`).
4. Set the Script Path to `Jenkinsfile`.
5. Click **Save** and trigger **Build Now**.
