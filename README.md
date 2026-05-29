# Jenkins Declarative CI/CD Pipeline Project

This repository demonstrates an automated Continuous Integration and Continuous Deployment (CI/CD) workflow using a **Jenkins Declarative Pipeline**. The pipeline automatically fetches code from GitHub, builds and packages a Java web application, performs static code analysis with SonarQube, and deploys the artifact onto an Apache Tomcat server.

---

## 🚀 Pipeline Architecture Workflow

1. **Source Code Management:** Pulls the latest code from the GitHub repository on code changes.
2. **Build Stage:** Uses **Apache Maven** to compile code and package it into a deployable `.war` file.
3. **Code Quality Review:** Scans code vulnerabilities, bugs, and code smells via **SonarQube**.
4. **Deployment Stage:** Automates deployment of the `.war` application to the target **Tomcat Server**.

---

## 🏗️ Infrastructure Setup Guide

To run this pipeline, you need to set up the following infrastructure (Jenkins, SonarQube, and Tomcat). It is recommended to use separate Linux servers (e.g., Ubuntu) for production, but they can be installed on a single machine for testing.

### 1. Prerequisites (Install Java)
Java is required for all three services. Run this on all servers:
cmd
sudo apt update -y
sudo apt install openjdk-17-jdk -y
java -version

```

### 2. Setup Jenkins Server

Install Jenkins and start the service:

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc [https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key](https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key)
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" [https://pkg.jenkins.io/debian-stable](https://pkg.jenkins.io/debian-stable) binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins

```

* Access Jenkins at `http://<JENKINS_SERVER_IP>:8080`.
* Retrieve the initial password: `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
* Install suggested plugins.

### 3. Setup SonarQube Server

SonarQube requires a database (PostgreSQL) and cannot run as the root user.

**Install PostgreSQL:**

```bash
sudo apt install postgresql postgresql-contrib -y
sudo systemctl start postgresql
sudo systemctl enable postgresql

```

**Install SonarQube:**

```bash
sudo apt install unzip -y
wget [https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.0.zip](https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.0.zip)
unzip sonarqube-9.9.0.zip
sudo mv sonarqube-9.9.0 /opt/sonarqube

```

**Create User and Start:**

```bash
sudo adduser --system --no-create-home --group --disabled-login sonarqube
sudo chown -R sonarqube:sonarqube /opt/sonarqube
sudo su - sonarqube -c "/opt/sonarqube/bin/linux-x86-64/sonar.sh start"

```

* Access SonarQube at `http://<SONARQUBE_SERVER_IP>:9000` (Default: `admin` / `admin`).

### 4. Setup Apache Tomcat Server

Install Tomcat to serve as your deployment environment.

**Download and Extract:**

```bash
wget [https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.8x/bin/apache-tomcat-9.0.8x.tar.gz](https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.8x/bin/apache-tomcat-9.0.8x.tar.gz)
tar -xvzf apache-tomcat-9.0.8x.tar.gz
sudo mv apache-tomcat-9.0.8x /opt/tomcat

```

**Configure Users:**
Edit `sudo nano /opt/tomcat/conf/tomcat-users.xml` and add:

```xml
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<user username="tomcat-admin" password="your_secure_password" roles="manager-gui,manager-script"/>

```

**Allow Remote Access:**
Edit `sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml` and comment out the `Valve` tag:

```xml

```

**Start Tomcat:**

```bash
sudo /opt/tomcat/bin/startup.sh

```

* Access Tomcat at `http://<TOMCAT_SERVER_IP>:8080`.

---

## 🛠️ Jenkins Configuration & Integration

Once the infrastructure is up, configure Jenkins to communicate with your tools.

### 1. Install Jenkins Plugins

Go to `Manage Jenkins` > `Plugins` > `Available Plugins` and install:

* **Git Plugin**
* **Pipeline**
* **SonarQube Scanner for Jenkins**
* **Deploy to container Plugin**

### 2. Global Tool Configuration

Set up the tools under `Manage Jenkins` > `Tools`:

* **JDK:** Add your Java installation path.
* **Maven:** Name it `Maven-3.9` (must match the name in your `Jenkinsfile`).

### 3. Add Credentials

Go to `Manage Jenkins` > `Credentials` and add:

* **SonarQube Token:** Generate a token in SonarQube (`My Account` > `Security`) and add it to Jenkins as a `Secret text`.
* **Tomcat Credentials:** Add a `Username with password` using the Tomcat `tomcat-admin` credentials created earlier. Give it an ID like `tomcat-manager-creds`.

### 4. SonarQube Server Link

* Go to `Manage Jenkins` > `System` > `SonarQube servers`.
* Name it exactly `SonarQube-Server` (to match the `Jenkinsfile`) and provide the SonarQube server URL and the credential token.

---

## 🏃 How to Run the Pipeline

1. Create a new **Pipeline** job in Jenkins.
2. Under the **Pipeline** section, set the Definition to **Pipeline script from SCM**.
3. Select **Git**, provide this repository URL, and specify your main branch.
4. Set the Script Path to `Jenkinsfile`.
5. Click **Save** and trigger **Build Now**.

```

```
