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
        
        stage('Update Deployment File') {
        environment {
            GIT_REPO_NAME = "JavaApplication"
            GIT_USER_NAME = "mooazsayyed"
        }
        steps {
            withCredentials([string(credentialsId: 'github')]) {
                sh '''
                    git config user.email "sam2221195@sicsr.ac.in"
                    git config user.name "mooazsayyed"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i 's|image: .*|image: ${IMAGE_NAME}|g'  java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
                    git add java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
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
