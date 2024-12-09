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
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}:${RELEASE}-${env.BUILD_NUMBER}"
        RELEASE = "2.0.0"
        DOCKER_USER = "mooaz"
        ARGOCD_SERVER = "65.2.149.15:30102" // Replace with ArgoCD URL
        ARGOCD_USERNAME = "admin"
        JOB_URL = ${env.JOB_URL}
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
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                        sh "mvn sonar:sonar"
                        archiveArtifacts artifacts: 'target/sonar/**/*', allowEmptyArchive: true
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
        stage("Trivy Scan") {
            steps {
                script {
                    echo "Scanning Docker image for vulnerabilities..........................."
                    sh """
                        trivy image --scanners vuln --severity HIGH,CRITICAL --exit-code 1 --format json --output trivy-report.json ${IMAGE_NAME} || true
                    """
                }
                archiveArtifacts artifacts: 'trivy-report.json', allowEmptyArchive: true
            }
        }
        stage("Update Deployment File") {
    steps {
        withCredentials([string(credentialsId: 'github1', variable: 'GITHUB_TOKEN')]) {
            script {
                echo "Setting up repository..........................."
                sh """
                    if [ ! -d "javaapplication-gitops" ]; then
                        git clone https://\${GITHUB_TOKEN}@github.com/mooazsayyed/javaapplication-gitops.git
                    fi
                    cd javaapplication-gitops
                    git config user.email "sam2221195@sicsr.ac.in"
                    git config user.name "mooazsayyed"
                    git fetch origin main
                    git pull origin main
                    sed -i 's|image: .*|image: ${IMAGE_NAME}|g' deployment.yaml
                    git add .
                    git commit -m "Update deployment image to version ${IMAGE_NAME}"
                    git push https://\${GITHUB_TOKEN}@github.com/mooazsayyed/javaapplication-gitops.git main
                """
            }
        }
    }
}

        stage("Sync with ArgoCD") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'argocd-credentials', usernameVariable: 'ARGOCD_USERNAME', passwordVariable: 'ARGOCD_PASSWORD')]) {
                    script {
                        echo "Synchronizing with ArgoCD..........................."
                        sh """
                            argocd login ${ARGOCD_SERVER} --username ${ARGOCD_USERNAME} --password ${ARGOCD_PASSWORD} --insecure
                            argocd app sync ${APP_NAME}
                        """
                    }
                }
            }
        }
    }
    post {
        always {
            emailext (
                subject: 'Build Notification',
                body: 'The build is complete.',
                to: 'sayyedmooaz@gmail.com',
                attachmentsPattern: '**/*.log'
            )
        }
        success {
            emailext(
            subject: 'Success Notification',
            body: 'The build is successful. Job URL: ${env.JOB_URL}',
            to: 'sayyedmooaz@gmail.com',
            attachmentsPattern: '**/*.log'
            ) 
        }
        failure {
            emailext(
            subject: 'Failure Notification',
            body: 'The build ${env.BUILD_NUMBER} is failed. Job URL: ${env.JOB_URL}',
            to: 'sayyedmooaz@gmail.com',
            attachmentsPattern: '**/*.log'
            )
        }
    }
}
