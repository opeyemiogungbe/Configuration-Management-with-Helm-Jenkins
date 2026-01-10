# Configuration-Management-with-Helm-Jenkins

Project Scenario

This capstone project demonstrates how Helm charts can be used alongside Jenkins to automate application deployment in a simplified CI/CD pipeline.

As a DevOps Engineer, the goal is to:

Understand Helm chart fundamentals

Deploy a basic web application using Helm

Integrate Helm into Jenkins for automated deployments

Apply basic security best practices suitable for beginners

This project focuses on clarity, hands-on learning, and practical understanding, rather than enterprise-level complexity.


## 2 Project Objectives

Set up a Jenkins server for CI/CD automation

Create and understand a basic Helm chart

Deploy and manage a web application using Helm

Integrate Helm with Jenkins for automated deployments

Apply simplified but meaningful security practices


## 3 Prerequisites

Basic understanding of Jenkins

Completion of:

Introduction to Helm Charts

Working with Helm Charts mini-projects

Familiarity with:

Linux terminal

Git

Docker (basic level)

Kubernetes fundamentals (pods, services)

## 4. Project Architecture (High Level)

```
Developer → GitHub Repository
                ↓
           Jenkins Pipeline
                ↓
              Helm
                ↓
        Kubernetes Cluster
                ↓
        Web Application
```

## 5. Jenkins Server Setup
Objective

Configure a Jenkins server to automate CI/CD tasks.

### 5.1 Jenkins Installation (Beginner-Friendly)

Environment Assumption

Ubuntu 20.04 / 22.04 server

Jenkins installed on a dedicated server (cloud)

### Step 1: Update the system

```
sudo apt update && sudo apt upgrade -y
```
### Step 2: Install Java (Jenkins requirement)

```
sudo apt install openjdk-17-jdk -y
```
- Verify Java 

```
java -version
```
### Step 3: Install Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
/usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install jenkins -y
```
### Step 3: Install Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
/usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install jenkins -y
```

### Step 4: Start Jenkins

```
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

- Access Jenkins:

```
http://<server-ip>:8080
```
## 5.2 Jenkins Initial Setup

- Retrieve admin password:

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
## 5.3 Install Suggested Plugins:

  - Kubernetes Plugin: which allows Jenkins to dynamically launch build agents (pods) inside Kubernetes. This is essential when Jenkins itself is deployed via Helm in a cluster.

  - Pipeline Plugin (Declarative & Scripted Pipelines), this provides the foundation for CI/CD pipelines in Jenkins. It also lets you define build, test, and deploy stages as code.

  - Git Plugin: This integrates Jenkins with Git repositories, which is needed for pulling source code and triggering builds.

  - GitHub / GitLab Plugins: This helps to add webhooks and integration with GitHub/GitLab, which is useful for automated builds when code is pushed.

  - Credentials Binding Plugin: This securely manages secrets (like Docker registry passwords, cloud keys), which works well with Kubernetes secrets managed via Helm.

  - Configuration as Code (JCasC) Plugin: This lets us define Jenkins configuration in YAML. which is perfect for Helm deployments, since you can keep Jenkins setup declarative.

  - Blue Ocean Plugin: This provides a modern UI for Jenkins pipelines, which is helpful for visualizing CI/CD flows.

  - Docker Pipeline Plugin: This enables building and running Docker containers inside pipelines, which is often paired with Kubernetes deployments.

⚖️ Why These Plugins Matter with Helm: 

-Plugin overload: Jenkins has 2000+ plugins; installing too many can slow performance. Stick to essentials.

- Version compatibility: Ensure plugin versions match your Jenkins core version (Helm charts often specify tested versions).

- Security: Plugins can introduce vulnerabilities; always update regularly and audit usage.

### Create an admin user: This allow to set up your username and password for easy login


## 5.4 Basic Security Measures (Beginner Level)

### Measure
  
- User authentication: prevents anonymous access

- Role-based access: Limits actions per user

- Credentials manager: secures kubeconfig & Git tokens

- Firewall:	opens only required ports (8080, 22)

All these controls provide basic protection without complexity.

## 6. Helm Chart Basics Objective: Introduce Helm charts and their purpose.

### 6.1 What is Helm?

- Helm is a package manager for Kubernetes. Helm charts define:

 - Application structure

 - Configuration

 - Kubernetes resources

### Why do we use Helm?: 
Reusable deployments, Version control and Easy upgrades and rollbacks

## 6.2 Helm Chart Structure

- Create a Helm chart:

```
helm create webapp
```
- Expected helm structure

```
webapp/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── _helpers.tpl

```

## 6.3 Key Files Explained

### Chart.yaml: 

This defines helm metadata. Like the identity card of the chart. E.g - Chart name, Version, Description
- API version


```
apiVersion: v2
name: webapp
description: Simple web application
version: 0.1.0
```
### values.yaml: 

This is the settings file for the chart. which stores configuration values that can be customized (like image version, replica count, service type).

```
replicaCount: 2

image:
  repository: nginx
  tag: latest

service:
  type: ClusterIP
  port: 80
```
### templates/

This is the blueprints of Kubernetes resources. it contains YAML manifests (Deployments, Services, ConfigMaps, etc.) but with placeholders. Helm fills in those placeholders using values from values.yaml.

```
spec:
  replicas: {{ .Values.replicaCount }}
  containers:
    - image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

## 7. Working with Helm Charts

### Objective: Deploy and manage applications using Helm.

- 7.1 Deploy Application

```
helm install webapp-release ./webapp
```

- verify

```
kubectl get pods
kubectl get svc
```

## 7.2 Helm Values & Templates

- Helm allows dynamic values using templates:

```
replicas: {{ .Values.replicaCount }}
```
- We can also change values without editing templates:

```
helm upgrade webapp-release ./webapp --set replicaCount=3
```

## 7.3 Upgrade and Rollback

- Upgrade:

```
helm upgrade webapp-release ./webapp
```
- Rollback:

```
helm rollback webapp-release 1
```

## 8. Integrating Helm with Jenkins

- Objective: Automate Helm deployments via Jenkins pipeline.

## 8.1 Jenkins Credentials Setup

Add Kubernetes config as a Secret File

Add GitHub credentials (if private repo)














