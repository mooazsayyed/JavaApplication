pipeline {
    agent {
        label "jenkins-agent"
    }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "java-application"
        RELEASE = "1.0.0"
        DOCKER_USER = "mooaz"
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}:${RELEASE}-${BUILD_NUMBER}"
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                echo "Cleaning workspace..........................."
                cleanWs()
            }
        }
        stage("Checkout from SCM") {
            steps {
                echo "Checking out code from SCM...................."
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/mooazsayyed/JavaApplication'
            }
        }
        stage("Build") {
            steps {
                echo "Building..........................."
                sh "mvn clean install"
            }
        }
        stage("Test") {
            steps {
                echo "Testing..........................."
                sh "mvn test"
            }
        }
        stage("SonarQube analysis") {
            steps {
                script {
                    echo "Running SonarQube analysis..........................."
                    withSonarQubeEnv(credentialsId:'jenkins-sonarqube-token') {
                        sh "mvn sonar:sonar"
                    }
                }   
            }
        }
        stage("SonarQube Quality Gate") {
            steps {
                script {
                    echo "Running SonarQube quality gate..........................."
                    waitForQualityGate abortPipeline: false , credentialsId: 'jenkins-sonarqube-token'
                }   
            }
        }
        stage("Docker Build and Push") {
            steps {
                script {
                    echo "Building and pushing the Docker image..........................."
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
                    sh "docker build -t ${IMAGE_NAME} ."
                    sh "docker push ${IMAGE_NAME}"
                }
            }
        }
        }
    }
    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
