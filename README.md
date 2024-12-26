# devops-ci-cd

# DevSecOps Project: Netflix Clone Deployment

Hi! I’m back again with an interesting DevSecOps project, which involves deploying a Netflix clone. We will use Jenkins as a CI/CD tool to deploy our application on a Docker container and a Kubernetes cluster. Additionally, we will monitor Jenkins and Kubernetes metrics using Grafana, Prometheus, and Node Exporter. I hope this detailed blog is useful!

## Workflow Diagram of my Project
(Insert workflow diagram here)

### Let's get started 🏃‍♀️

Below is the code for Netflix, the Jenkinsfile, and everything else you will need, including the Dockerfile, all of which play a crucial role in today’s project. Visit for more information.

- **GitHub Repository:** [DevSecOps-CICD-Pipeline-NetflixClone](https://github.com/mdazfar2/DevSecOps-CICD-Pipeline-NetflixClone)

## Prerequisites

Before we embark on our DevSecOps journey, let’s ensure we have the necessary setup in place. Here are the prerequisites:

1. **Launch an EC2 Instance**
   - Begin by launching an EC2 instance on the Ubuntu image of `t2.large`. Allocate a storage capacity of at least 35GB.

2. **Install Jenkins**
   - Jenkins serves as the cornerstone of our CI/CD pipeline. Follow these steps to install Jenkins:
     ```bash
     # Create file using vim
     vim jenkins.sh
     # Add script to install Jenkins (see original instructions)
     chmod +x ./jenkins.sh
     ./jenkins.sh
     ```

3. **Install Docker**
   - Install Docker to containerize our application:
     ```bash
     sudo apt update -y
     sudo apt install docker.io -y
     sudo usermod -a -G docker $USER
     newgrp docker
     sudo chmod 777 /var/run/docker.sock
     ```

4. **Install SonarQube**
   - SonarQube integrates with Jenkins to analyze code quality and security vulnerabilities:
     ```bash
     docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
     ```

5. **Install Trivy Image**
   - Trivy scans container images for vulnerabilities:
     ```bash
     vim trivy.sh
     # Add script to install Trivy (see original instructions)
     chmod +x ./trivy.sh
     ./trivy.sh
     ```

6. **Install Prometheus and Grafana**
   - Launch a new EC2 instance of type `t2.medium` and install Prometheus and Grafana for monitoring metrics.

## Follow These Steps

1. **Launch EC2 Instance**
   - Create your EC2 instance with Ubuntu AMI and instance type `t2.large`.
   - Set up storage, key pair, and network settings.

2. **Install Jenkins, Docker, SonarQube, Trivy, Prometheus, and Grafana**
   - Follow the instructions above to install and configure Jenkins, Docker, SonarQube, Trivy, Prometheus, and Grafana.

## Jenkins Pipeline

Here’s the Jenkins pipeline script that automates the build, test, and deploy process:

```groovy
pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/mdazfar2/DevSecOps-CICD-Pipeline-NetflixClone.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix -Dsonar.projectKey=Netflix '''
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'your-credentials-id'
                }
            }
        }
    }
}






#######################################
# DevSecOps Project: Deploying a Netflix Clone

Hi! I’m back again with an interesting **DevSecOps project**, which involves deploying a Netflix clone.  
We will use **Jenkins** as a CI/CD tool to deploy our application on a **Docker container** and a **Kubernetes cluster**. Additionally, we will monitor Jenkins and Kubernetes metrics using **Grafana**, **Prometheus**, and **Node Exporter**.  

I hope this detailed guide is useful. Let's dive in! 🚀  

---

## Workflow Diagram of My Project

![Workflow Diagram](link-to-diagram)

---

## Prerequisite

Before we embark on our DevSecOps journey, let’s ensure we have the necessary setup in place.  

1. **Launch an EC2 Instance**  
   - Ubuntu image of **t2.large**
   - Storage: At least **35GB**  

2. **Install Jenkins**  
   - Facilitates automated building, testing, and deployment.  

3. **Install Docker**  
   - Containerizes our applications for consistency and portability.  

4. **Install SonarQube**  
   - For code quality analysis and security vulnerability detection.  

5. **Install Trivy Image**  
   - Scans container images for vulnerabilities.  

---

### Install Prometheus and Grafana

1. Launch a new **EC2 Instance**:
   - Ubuntu image of **t2.medium**
   - Storage: At least **12GB**  

2. Install **Prometheus** and **Node Exporter**.  
3. Install **Grafana** to visualize metrics.  

---

## Steps to Set Up the Project

### Step 1: Launch EC2 Instance and Install Jenkins

1. Launch a **t2.large** EC2 instance with **40GB** storage.  
2. Connect to the instance via SSH and run the following script:  

```bash
vim jenkins.sh


#!/bin/bash
sudo apt update -y
wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | sudo tee /etc/apt/keyrings/adoptium.asc
echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | sudo tee /etc/apt/sources.list.d/adoptium.list
sudo apt update -y
sudo apt install temurin-17-jdk -y
/usr/bin/java --version
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
sudo systemctl start jenkins
sudo systemctl status jenkins


    Run the script:

chmod +x jenkins.sh
./jenkins.sh

Step 2: Install Docker

sudo apt update -y
sudo apt install docker.io -y
sudo usermod -a -G docker $USER
newgrp docker
sudo chmod 777 /var/run/docker.sock

Step 3: Install SonarQube

docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

Access SonarQube:
http://<your-ec2-public-ip>:9000

Login credentials:

    Username: admin
    Password: admin

Step 4: Install Trivy

Create the trivy.sh script:

vim trivy.sh

#!/bin/bash
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y

Run the script:

chmod +x trivy.sh
./trivy.sh

Prometheus Installation

    Launch a t2.medium instance with 15GB storage.
    Run the following commands:

sudo useradd --system --no-create-home --shell /bin/false prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz
tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
sudo mkdir -p /data /etc/prometheus
cd prometheus-2.47.1.linux-amd64/
sudo mv prometheus promtool /usr/local/bin/
sudo mv consoles/ console_libraries/ /etc/prometheus/
sudo mv prometheus.yml /etc/prometheus/prometheus.yml
sudo chown -R prometheus:prometheus /etc/prometheus/ /data/

Node Exporter Installation

sudo useradd --system --no-create-home --shell /bin/false node_exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
rm -rf node_exporter*

Grafana Installation

sudo apt-get install -y apt-transport-https software-properties-common
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
sudo apt-get update
sudo apt-get -y install grafana
sudo systemctl enable grafana-server
sudo systemctl start grafana-server

Access Grafana:
http://<your-ec2-public-ip>:3000
Integrating Jenkins and Prometheus

    Install the Prometheus Plugin in Jenkins.
    Update the Prometheus configuration:

sudo vim /etc/prometheus/prometheus.yml

- job_name: 'jenkins'
  metrics_path: '/prometheus'
  static_configs:
    - targets: ['<jenkins-ip>:8080']

Reload the configuration:

curl -X POST http://localhost:9090/-/reload

Monitoring with Grafana

    Import Dashboard 1860 for Node Exporter.
    Import Dashboard 9964 for Jenkins monitoring.

Author

Name: Mjahid(Faisal)
GitHub ID: faisal-khan-dev


You can copy-paste this into a `README.md` file in your GitHub repository. Let me know if you need further


