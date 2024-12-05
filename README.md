# **🚀 CI/CD Pipeline with Jenkins, SonarQube, ArgoCD, and Monitoring**<br>
This project demonstrates a robust CI/CD pipeline leveraging Jenkins, SonarQube, and ArgoCD to deploy on Kubernetes cluster and monitoring tools like Prometheus and Grafana. And implementation for Ideas for **logging and monitoring**.

![Complete ci cd (1) (1)](https://github.com/user-attachments/assets/faadab4b-4a55-4442-b525-36986a9ec49e)

## **Project Overview**

**Architecture:-**

**Jenkins UI**: Hosted on an EC2 instance.<br>
****Jenkins** Agent**: Deployed on Azure to execute build and deployment tasks.<br>
**SonarQube**: Code quality and security scanner hosted on Azure.<br>
**ArgoCD**: GitOps-based continuous deployment tool hosted on Azure deploys on the Kubernetes cluster.<br>
**Prometheus & Grafana**: Both hosted on the EC2 instance alongside Jenkins for system monitoring and visualization.<br>

---

### **Key Features**
Automated build, test, and deploy pipeline.<br>
Static code analysis using SonarQube.<br>
Container vulnerability scanning with Trivy.<br>
Continuous deployment with ArgoCD.<br>
Real-time monitoring and dashboards using Prometheus and Grafana.<br>

---

## Setup 

### 1. **Jenkins**
- **Installation**: Jenkins is deployed on an EC2 instance.
- **Plugins**: Ensure the following plugins are installed:
  - `Git`
  - `Pipeline`
  - `Docker Pipeline`
  - `Email Extension`
  - `SonarQube Scanner`
- **Configuration**:
  - Set up a Jenkins agent on Azure and configure the label as `Jenkins-agent.`

### 2. **SonarQube**
- **Installation**: Deployed on Azure with a configured database.
- **Integration**: Add SonarQube server details and authentication token in Jenkins global configuration.

### 3. **ArgoCD/Kubernetes**
- **Installation**: Hosted on VM(Kind Cluster).
- **Configuration**:
  - Log in using the ArgoCD CLI in Jenkins pipeline steps.
  - Set up a Kubernetes cluster connection for deployments.

### 4. **Prometheus & Grafana**
- **Installation**:
  - Deployed on the same EC2 instance as Jenkins.
  - Prometheus is used for metrics collection.
  - Grafana is configured to visualize metrics from Prometheus.
- **Dashboards**:
  - Create custom Grafana dashboards for Jenkins and system performance.

---
## Pipeline Workflow

1. **Checkout Source Code**:
   - Retrieves code from the Git repository.

2. **Build & Test**:
   - Builds the application using Maven and runs tests.

3. **Static Code Analysis**:
   - Scans the code using SonarQube and ensures the quality gate is passed.

4. **Docker Image Build**:
   - Builds and pushes Docker images to Docker Hub.

5. **Trivy Vulnerability Scan**:
   - Scans the Docker image for vulnerabilities and archives the report.

6. **Update Deployment Files**:
   - Modifies Kubernetes deployment manifests with the new image version.

7. **Sync with ArgoCD**:
   - Pushes changes to Git and syncs with ArgoCD for deployment.

8. **Monitoring**:
   - Observe pipeline and system performance using Prometheus and Grafana.


## How the pipeline runs
### The pipeline gets triggered if a commit is made on the main branch
OR
### Trigger the pipeline from the Jenkins UI.

## **View reports:**

**SonarQube**: Navigate the artifacts.
**Trivy**: Check the trivy-report.json artifact.

**Monitoring**:  Grafana dashboards via the EC2 instance.


## **Deployment updates will be automatically synced via ArgoCD.**

## Logging and Monitoring

### 1. **Jenkins Logging**

Jenkins generates logs for each build, which are crucial for debugging and understanding pipeline executions.

- **Build Logs**: Jenkins stores logs for each pipeline execution. You can view the logs in real-time from the Jenkins UI. This log includes detailed information about each stage, including any errors or warnings that occurred during the build, test, or deployment process.
  
- **Console Output**: Each Jenkins job provides a console output, which shows the logs for the running process. You can access this output by navigating to the specific build in Jenkins and selecting "Console Output."

- **System Logs**: Jenkins also provides system-level logs, which you can access via the Jenkins UI (Manage Jenkins → System Log). These logs are helpful for diagnosing issues with the Jenkins environment itself.

- **Log Aggregation**: For large Jenkins instances, it's recommended to use a centralized log management tool (e.g., ELK stack or Fluentd) to aggregate logs from multiple sources and enable advanced querying.

### 2. **Prometheus Monitoring**

Prometheus collects metrics about the Jenkins build system and other services in the pipeline, providing insights into system health and performance.

- **Metrics Collection**: Prometheus gathers metrics from Jenkins, ArgoCD, and the underlying infrastructure. Metrics such as build duration, failed build count, system resource usage (CPU, memory), and request latencies can be tracked.
  
- **Prometheus Setup**: Prometheus is set up to scrape metrics from various exporters:
  - **Jenkins Exporter**: Collects Jenkins metrics like build success, failure, and performance data.
  - **Node Exporter**: Collects system metrics such as CPU, memory, and disk usage from the EC2 instance and Azure machines.

- **Alerting**: Prometheus can be configured to trigger alerts based on predefined conditions such as high CPU usage or build failures. Alerts are typically sent via email, Slack, or integrated with Grafana.

### 3. **Grafana Dashboards**

Grafana is used to visualize the metrics collected by Prometheus.

- **Pre-built Dashboards**: Grafana has pre-built dashboards for Jenkins, system metrics, and container performance. You can use these dashboards to get an overview of the pipeline's health and performance.
  
- **Custom Dashboards**: You can create custom dashboards to track specific metrics relevant to your CI/CD pipeline, such as:
  - **Jenkins Job Metrics**: Track job success rate, execution time, and failures.
  - **System Health Metrics**: Monitor CPU, memory, and disk usage on your EC2 and Azure instances.
  - **Pipeline Metrics**: Track the performance of each stage in the Jenkins pipeline, from build to deployment.

- **Alerting in Grafana**: You can set up alerts in Grafana for important events, such as high resource usage or failed jobs, and get notified via email or Slack.

---

### **Contact**
For any issues or inquiries: <br>
Email: **sayyedmooaz@gmail.com**
