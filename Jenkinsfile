pipeline {
    agent {
        label "jenkins-agent"
    }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    Environment {
        APP_NAME = "JavaApplication"
        RELEASE = "1.0.0"
        DOCKER_USER = "mooaz"
        DOCKER_PASS = "dockerhub"
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
                    echo "Running sonarqube analysis..........................."
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token'){
                        sh "mvn sonar:sonar"
                    }
                }   
            }
        }
        stage("SonarQube Quality Gate") {
            steps {
                script {
                    echo "Running sonarqube qualitygate..........................."
                    waitForQualityGate abortPipeline: false , credentialsId: 'jenkins-sonarqube-token'
                }   
            }
        }
        stage("Docker Build and Push" ) {
            steps {
                script {
                    echo "Building the docker image..........................."
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
                        sh "docker build -t ${IMAGE_NAME} ."
                        sh "docker push ${IMAGE_NAME}"
                    }
            }
        }
    }
}
