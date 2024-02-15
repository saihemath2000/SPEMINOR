pipeline {
    agent any
    // Define a custom workspace
    options {
        // Clean workspace before each build
        skipDefaultCheckout(true)
        buildDiscarder(logRotator(numToKeepStr: '5'))
        // Define custom workspace location
    }
    environment {
        DOCKER_REGISTRY_CREDENTIALS = 'DockerHubCred'
        DOCKER_IMAGE_NAME = 'saihemanth1997/minicalculator'
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/master']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    submoduleCfg: [],
                    userRemoteConfigs: [[url: 'https://github.com/saihemath2000/SPEMINOR.git']]
                ])
            }
        }

        stage('Build') {
            steps {
                script{
                    def customWorkspace = "/home/hemanth/Desktop/iiitb/MiniCalculator/"
                    ws(customWorkspace){
                     checkout scm   
                     sh 'mvn clean package'   
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                script{
                def customWorkspace = "/home/hemanth/Desktop/iiitb/MiniCalculator/"
                    ws(customWorkspace){
                     checkout scm   
                     sh 'mvn test' 
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                    script {
                        def customWorkspace = "/home/hemanth/Desktop/iiitb/MiniCalculator/"
                        ws(customWorkspace){
                            checkout scm   
                            docker.build("${DOCKER_IMAGE_NAME}", '.')
                         }
                     }        
                }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    docker.withRegistry('', 'DockerHubCred') {
                        sh "docker tag ${DOCKER_IMAGE_NAME}:latest ${DOCKER_IMAGE_NAME}:latest"
                        sh "docker push ${DOCKER_IMAGE_NAME}:latest"
                    }
                }
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                script {
                    ansiblePlaybook(
                        playbook: 'deploy.yml',
                        inventory: 'inventory'
                    )
                }
            }
        }
    }
}
