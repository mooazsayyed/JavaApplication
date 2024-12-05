# **CI/CD Pipeline with Jenkins, SonarQube, ArgoCD, and Monitoring**
This project demonstrates a robust CI/CD pipeline leveraging Jenkins, SonarQube, ArgoCD to deploy on kubernetes cluster, and monitoring tools like Prometheus and Grafana.

##**Project Overview**
**Architecture**<br>
**Jenkins UI**: Hosted on an EC2 instance.<br>
****Jenkins** Agent**: Deployed on Azure to execute build and deployment tasks.<br>
**SonarQube**: Code quality and security scanner hosted on Azure.<br>
**ArgoCD**: GitOps-based continuous deployment tool hosted on Azure deploys on the Kubernetes cluster.<br>
Prometheus & Grafana: Both hosted on the EC2 instance alongside Jenkins for system monitoring and visualization.<br>
**Key Features
**
Automated build, test, and deploy pipeline.
Static code analysis using SonarQube.
Container vulnerability scanning with Trivy.
Continuous deployment with ArgoCD.
Real-time monitoring and dashboards using Prometheus and Grafana.

**Setup Instructions
**1. Jenkins
Installation: Jenkins is deployed on an EC2 instance.
Plugins: Ensure the following plugins are installed:
Git
Pipeline
Docker Pipeline
Email Extension
SonarQube Scanner
Configuration:
Set up a Jenkins agent on Azure and configure the label as jenkins-agent.
2. SonarQube
Installation: Deployed on Azure with a configured database.
Integration: Add SonarQube server details and authentication token in Jenkins global configuration.
3. ArgoCD
Installation: Hosted on Azure Kubernetes Service (AKS).
Configuration:
Log in using the ArgoCD CLI in Jenkins pipeline steps.
Set up a Kubernetes cluster connection for deployments.
4. Prometheus & Grafana
Installation:
Deployed on the same EC2 instance as Jenkins.
Prometheus is used for metrics collection.
Grafana is configured to visualize metrics from Prometheus.
Dashboards:
Create custom Grafana dashboards for Jenkins and system performance.
Pipeline Workflow
Checkout Source Code:

Retrieves code from the Git repository.
Build & Test:

Builds the application using Maven and runs tests.
Static Code Analysis:

Scans the code using SonarQube and ensures the quality gate is passed.
Docker Image Build:

Builds and pushes Docker images to Docker Hub.
Trivy Vulnerability Scan:

Scans the Docker image for vulnerabilities and archives the report.
Update Deployment Files:

Modifies Kubernetes deployment manifests with the new image version.
Sync with ArgoCD:

Pushes changes to Git and syncs with ArgoCD for deployment.
Monitoring:

Observes pipeline and system performance using Prometheus and Grafana.
How to Run
Clone the repository:

bash
Copy code
git clone https://github.com/mooazsayyed/JavaApplication.git
cd JavaApplication
Trigger the pipeline from the Jenkins UI.

View reports:

SonarQube: Navigate to the configured server on Azure.
Trivy: Check the trivy-report.json artifact.
Monitoring: Access Grafana dashboards via the EC2 instance.
Deployment updates will be automatically synced via ArgoCD.

Monitoring Dashboards
Grafana
URL: http://<EC2-Public-IP>:3000
Credentials:
Username: admin
Password: <configured-password>
Prometheus
URL: http://<EC2-Public-IP>:9090
Contact
For any issues or inquiries:

Email: sayyedmooaz@gmail.com
