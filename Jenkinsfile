pipeline {
    agent any
    tools {
        //tool type
        jdk 'jdk17'
        maven 'maven3'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage ('Git checkout'){
            steps {
                git 'https://github.com/formycore/devops_shack_cicd_boardgame.git'
            }
        }
    
        stage ('Maven compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage ('Maven test') {
            steps {
                sh 'mvn test'
            }
        }
        stage ('File System Scan'){
            steps{
             sh 'trivy fs --format table -o trivy-fs-report.html .'
            }
        }
        stage ('Sonarqube Analysis'){
            steps {
            withSonarQubeEnv('sonar') // we have already configured this 
            {
                sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectName=BoardGame \
                    -Dsonar.projectKey=BoardGame \
                    -Dsonar.java.binaries=.
                '''
            }
            }
        }
        stage ('Quality Gate Analysis'){
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage ('Build') {
            steps {
                sh 'mvn clean install '
            }
        }
        stage ('Publish Artifacts'){
            steps {
                withMaven(globalMavenSettingsConfig: 'global-setting', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                   sh 'mvn deploy'
                }
            }
        }
      }
    }