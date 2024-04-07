pipeline {
    agent any
    tools {
        //tool type
        
        maven 'maven3'
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
        stage ('Build') {
            steps {
                sh 'mvn clean install '
            }
        }
        stage ('Docker build'){
            steps{
                script {
                    withDockerRegistry(credentialsId: 'docker_token', toolName: 'docker') {
                        sh 'docker build -t formycore/apr06:latest .'
                    }
                }
            }
        }
        stage ('Docker push'){
            steps{
                script {
                    withDockerRegistry(credentialsId: 'docker_token', toolName: 'docker') {
                        sh "docker push formycore/apr06:latest"
                    }
                }
            }
        
      }
    }