

# 🌟 Ultimate CI/CD Pipeline 🌟

Welcome to the **Ultimate CI/CD Pipeline** project! This guide will help you set up an end-to-end CI/CD pipeline for Java applications using Jenkins, Docker, Kubernetes, and ArgoCD. Follow these steps to automate your build, test, and deployment processes effectively. 

---

## Table of Contents 📚

1. [Introduction](#introduction)
2. [Setup and Installation](#setup-and-installation)
   - [1. Build and Run Locally](#1-build-and-run-locally)
   - [2. Setup Jenkins](#2-setup-jenkins)
   - [3. Install SonarQube](#3-install-sonarqube)
   - [4. Install Minikube and Kubernetes](#4-install-minikube-and-kubernetes)
   - [5. Install and Configure ArgoCD](#5-install-and-configure-argocd)
3. [Troubleshooting](#troubleshooting)
4. [Conclusion](#conclusion)

---

## Introduction 🎯

The **Ultimate CI/CD Pipeline** project automates the build, test, and deployment processes of Java-based applications. This project uses:
- **Jenkins** for CI/CD automation
- **Docker** for containerization
- **Kubernetes** for orchestration
- **ArgoCD** for continuous deployment

This setup ensures efficient development workflows, automated testing, and seamless deployments.

---

## Setup and Installation 🛠️

### 1. Build and Run Locally 🏠

Ensure your application runs correctly before deploying it using CI/CD.

#### 1.1 Clone the Repository 🌀

```bash
git clone <repo-url>
cd <project-path>
```

#### 1.2 Generate Artifacts Using Maven 🏗️

```bash
mvn clean package
```
This command generates a `.jar` file in the `target` directory.

#### 1.3 Build and Run the Docker Image 🐳

```bash
docker build -t ultimate-cicd-pipeline:v1 .
docker run -d -p 8010:8080 -t ultimate-cicd-pipeline:v1
```

#### 1.4 Access the Application 🌐

Visit `http://<your-ip-address>:8010` in your browser.

---

### 2. Setup Jenkins 🚀

Jenkins will handle the CI/CD processes.

#### 2.1 Create an EC2 Instance ☁️

Launch an EC2 instance (e.g., `t2.large`).

#### 2.2 Install Java and Jenkins ☕🔧

**Install Java:**
```bash
sudo apt update
sudo apt install openjdk-17-jre
java -version
```
Ensure Java 17 is installed.

**Install Jenkins:**
```bash
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

#### 2.3 Open Jenkins Port 🔓

Update the security group to allow traffic on port 8080.

#### 2.4 Access Jenkins 🔑

Visit `http://<ec2-ip>:8080` and retrieve the Jenkins password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

#### 2.5 Configure Jenkins Pipeline 🛠️

1. **Create a Jenkinsfile:** Define the build pipeline in this file.
2. **Add Jenkinsfile to Repository:** Ensure the Jenkinsfile is in your repository.

---

### 3. Install SonarQube 🔍

SonarQube will analyze your code for quality and security.

#### 3.1 Install Unzip Utility 🗂️

```bash
sudo apt-get install unzip
```

#### 3.2 Add SonarQube User and Download SonarQube 👤⬇️

**Add User:**
```bash
sudo adduser sonarqube
sudo -su sonarqube
```

**Download and Install SonarQube:**
```bash
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
unzip sonarqube-9.4.0.54424.zip
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
cd /home/sonarqube/sonarqube-9.4.0.54424/bin/linux-x86-64/
./sonar.sh start
```

#### 3.3 Access SonarQube 🌐

Visit `http://<ec2-ip>:9000`. Default credentials are `admin/admin`.

#### 3.4 Configure Jenkins with SonarQube 🔧

1. **Install SonarQube Scanner Plugin:** Manage Jenkins -> Manage Plugins -> Available tab -> Search for "SonarQube Scanner" and install it.
2. **Configure SonarQube Credentials:** Manage Jenkins -> Manage Credentials -> Add Credentials -> Select "Secret text" and enter the token generated from SonarQube.

---

### 4. Install Minikube and Kubernetes 🛠️

Minikube sets up a local Kubernetes cluster for testing and development.

#### 4.1 Install Minikube 📦

**Download and Install Minikube:**
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

**Start Minikube:**
```bash
minikube start --driver=docker
```

#### 4.2 Verify Installation ✅

Check that Minikube and Kubernetes are running:
```bash
kubectl get nodes
```

#### 4.3 Increase EC2 Volume Size (If Necessary) 💾

Increase the size of your EC2 volume in the EC2 Management Console if you encounter storage issues.

---

### 5. Install and Configure ArgoCD 🌟

ArgoCD will manage the deployment of your applications in Kubernetes.

#### 5.1 Install ArgoCD 📥

**Create Namespace and Apply Manifest:**
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

#### 5.2 Access ArgoCD 🔐

**Change Service Type to NodePort:**
```bash
kubectl get svc -n argocd
kubectl edit svc argocd-server -n argocd
```
Change `type: ClusterIP` to `type: NodePort`.

**Retrieve ArgoCD Password:**
```bash
kubectl get pods -n argocd
kubectl exec -it <argocd-server-pod> -n argocd -- argocd admin initial-password
```

**Access ArgoCD:**
Visit [http://<ec2-ip>:<node-port>](http://<ec2-ip>:<node-port>).

#### 5.3 Configure ArgoCD with GitHub and Docker Hub 🐙🐋

1. **Add GitHub Credentials:** Settings -> Repositories -> Add repository.
2. **Add Docker Hub Credentials:** Configure Docker credentials in ArgoCD for image pulling.

---

## Troubleshooting ⚠️

### Jenkins Issues 🔧

1. **Java Version Compatibility:**
   - Ensure Java 17 or later is installed.

2. **Port 8080 Already in Use:**
   - Check and terminate any processes using port 8080: `sudo lsof -i :8080`.

### Docker Issues 🐳

1. **Docker Restart Required:**
   - Restart Docker if changes do not apply: `sudo systemctl restart docker`.

### ArgoCD Issues 🌐

1. **CRDs Not Installed:**
   - Ensure CRDs are applied correctly. Check logs for errors.

2. **Service Type Not Updated:**
   - Confirm that the ArgoCD service type is set to NodePort.

---

## Conclusion 🎉

Congratulations! 🎊 You’ve successfully set up a comprehensive CI/CD pipeline for Java applications using Jenkins, Docker, Kubernetes, and ArgoCD. Your pipeline is now capable of automating the build, test, and deployment processes, ensuring efficient and reliable application delivery.

