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
        BUILD_NUMBER = "${env.BUILD_NUMBER}"
        DOCKER_USER = "mooaz"
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}:${RELEASE}-${BUILD_NUMBER}"
        ARGOCD_SERVER = "13.202.1.32:30102" // Replace with  ArgoCD URL
        ARGOCD_USERNAME = "admin"
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
        stage("SonarQube Analysis") {
            steps {
                script {
                    echo "Running SonarQube analysis..........................."
                    withSonarQubeEnv(credentialsId:'jenkins-sonarqube-token'){
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
        stage("SonarQube Quality Gate") {
            steps {
                script {
                    echo "Running SonarQube quality gate..........................."
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
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
        stage("Update Deployment File") {
            environment {
                GIT_REPO_NAME = "JavaApplication"
                GIT_USER_NAME = "mooazsayyed"
            }
            steps {
                withCredentials([string(credentialsId: 'github1', variable: 'GITHUB_TOKEN')]) {
                    script {
                        echo "Updating deployment file..........................."
                        sh """
                            git config user.email "sam2221195@sicsr.ac.in"
                            git config user.name "mooazsayyed"
                            sed -i 's|image: .*|image: ${IMAGE_NAME}|g' k8s/deployment.yaml
                            git add k8s/deployment.yaml
                            git commit -m "Update deployment image to version ${IMAGE_NAME}"
                            git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                        """
                    }
                }
            }
        }
        stage("Sync with ArgoCD") {
            environment {
                ARGOCD_PASSWORD = credentials('argocd-credentials')
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'argocd-credentials', usernameVariable: 'ARGOCD_USERNAME', passwordVariable: 'ARGOCD_PASSWORD')]) {
                    script {
                        echo "Synchronizing with ArgoCD..........................."
                        sh """
                            argocd login ${env.ARGOCD_SERVER} --username ${env.ARGOCD_USERNAME} --password ${env.ARGOCD_PASSWORD} --insecure
                        """
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
