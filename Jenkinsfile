pipeline {
    agent any

    tools {
        // Ensure these match the names configured in Jenkins Global Tool Configuration
        maven 'Maven-3.9' 
        jdk   'Java-17'
    }

    environment {
        // Define your environment variables here
        TOMCAT_CREDENTIALS_ID = 'tomcat-manager-creds'
        TOMCAT_URL            = 'http://localhost:8080/manager/text'
        SONAR_SERVER_ENV      = 'SonarQube-Server'
    }

    stages {
        stage('Git Checkout') {
            steps {
                echo 'Fetching source code from GitHub...'
                checkout scm
            }
        }

        stage('Build & Package') {
            steps {
                echo 'Compiling and packaging the application...'
                // Cleans old builds and creates a deployment-ready .war file
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Code Review') {
            steps {
                echo 'Running static code analysis via SonarQube...'
                // Executes SonarQube analysis using the configured Jenkins server environment
                withSonarQubeEnv("${SONAR_SERVER_ENV}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo 'Deploying artifact to Apache Tomcat Server...'
                // Deploys the generated .war file using the Cargo or Tomcat Deployer plugin style
                deploy artifacts: 'target/*.war', 
                       contextPath: '/my-webapp', 
                       war: 'target/*.war', 
                       url: "${TOMCAT_URL}", 
                       credentialsId: "${TOMCAT_CREDENTIALS_ID}"
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
            cleanWs() // Cleans up the workspace to save disk space
        }
        success {
            echo 'CI/CD Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed. Please check the console logs.'
        }
    }
}