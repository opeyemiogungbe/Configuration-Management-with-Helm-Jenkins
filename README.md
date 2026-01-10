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

Jenkins is installed on a dedicated server (cloud)

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

### Step 4: Start Jenkins

```
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
![Start Jenkins](https://i.postimg.cc/B6gnZmjj/Screenshot-2025-07-16-054500.png)

- Access Jenkins:

```
http://<server-ip>:8080
```
![Jenkins](https://i.postimg.cc/vmrKCjKP/Screenshot-2025-07-16-060119.png)

## 5.2 Jenkins Initial Setup

- Retrieve admin password:

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

## 5.3 Install Suggested Plugins:

  - Kubernetes Plugin: This allows Jenkins to launch and build agents (pods) inside Kubernetes dynamically. This is particularly important when Jenkins is deployed via Helm in a cluster.

  - Pipeline Plugin (Declarative & Scripted Pipelines), this provides the foundation for CI/CD pipelines in Jenkins. It also lets you define build, test, and deploy stages as code.

  - Git Plugin: This integrates Jenkins with Git repositories, which is needed for pulling source code and triggering builds.

  - GitHub / GitLab Plugins: This helps to add webhooks and integration with GitHub/GitLab, which is useful for automated builds when code is pushed.

  - Credentials Binding Plugin: This securely manages secrets (like Docker registry passwords, cloud keys), which works well with Kubernetes secrets managed via Helm.

  - Configuration as Code (JCasC) Plugin: This lets us define Jenkins configuration in YAML. which is perfect for Helm deployments, since you can keep Jenkins setup declarative.

  - Blue Ocean Plugin: This provides a modern UI for Jenkins pipelines, which helps visualize CI/CD flows.

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

Reusable deployments, Version control, and Easy upgrades and rollbacks

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

![Helm chart](https://i.postimg.cc/6ph6pR35/Screenshot-2026-01-10-145531.png)

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

This is the blueprint of Kubernetes resources. It contains YAML manifests (Deployments, Services, ConfigMaps, etc.) but with placeholders. Helm fills in those placeholders using values from values.yaml.

```
spec:
  replicas: {{ .Values.replicaCount }}
  containers:
    - image: "{{.Values.image.repository }}:{{.Values.image.tag }}"
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
![cluster](https://i.postimg.cc/wBQwghjb/Screenshot-2025-07-19-113728.png)

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

### ✅Create an EKS Cluster using eksctl

Note: Before creating our cluster we need to set up our AWS CLI so we can manage aws from our terminal. Example of our CLI configuration is shown below

![AWS CLI](https://i.postimg.cc/fWmhXPkn/Screenshot-2025-07-19-070414.png)

Then we go ahead to create our cluster

```
eksctl create cluster \
  --name eksctl-helm-cluster \
  --version 1.29 \
  --region us-east-1 \
  --nodegroup-name jenkins-nodes \
  --node-type t3.medium \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 3 \
  --managed
```
![Cluster](https://i.postimg.cc/YSYJgGqw/Screenshot-2025-07-19-071030.png)

Run the following to confirm:

```
kubectl get nodes
```

You should see something like:

![cluster](https://i.postimg.cc/wBQwghjb/Screenshot-2025-07-19-113728.png)

### 🚀 Provision Jenkins EC2 Server

Launch an EC2 instance (Ubuntu) with a security group allowing:

Port 22 (SSH)

Port 8080 (Jenkins)

SSH into the instance and install Jenkins:

```
sudo apt update
sudo apt install openjdk-17-jdk -y
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
```
Start and enable Jenkins:

```
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

![Start Jenkins](https://i.postimg.cc/B6gnZmjj/Screenshot-2025-07-16-054500.png)

Access Jenkins at: http://<EC2-Public-IP>:8080

![Jenkins](https://i.postimg.cc/vmrKCjKP/Screenshot-2025-07-16-060119.png)




### 🔐 Configure AWS Credentials on Jenkins Server

We must ensure AWS CLI is installed on the Jenkins server so they can access each other:

```
sudo apt install awscli -y
```
Then run:

```
aws configure
```
Note: We need to input our AWS credentials when using aws configure

### 📦 Install kubectl and Helm:

```
# kubectl
curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# helm
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

### ⚙️ Configure kubeconfig for Jenkins

 - Ensure kubeconfig is generated and available at:

```
mkdir -p /var/lib/jenkins/.kube
cp /home/ubuntu/.kube/config /var/lib/jenkins/.kube/config
chown -R jenkins:jenkins /var/lib/jenkins/.kube
chmod 600 /var/lib/jenkins/.kube/config
```

🧰 Jenkins Pipeline Setup

1. We create a Jenkins Pipeline Job

Set the pipeline source to Pipeline script from SCM

SCM: Git

Repo URL: https://github.com/<our-username>/Integrating-jenkins-with-Helm-.git

Script Path: Jenkinsfile

📝 Jenkinsfile

```
pipeline {
  agent any

  environment {
    HELM = '/usr/local/bin/helm'
    KUBECONFIG = '/var/lib/jenkins/.kube/config'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Verify Cluster Access') {
      steps {
        sh 'kubectl get nodes'
      }
    }

    stage('Helm Deploy') {
      steps {
        sh '${HELM} upgrade --install webapp ./webapp -f ./webapp/values.yaml --namespace default --create-namespace'
      }
    }
  }
}
```

![Jenkinsfile](https://i.postimg.cc/ydd0H3dY/Screenshot-2026-01-04-074556.png)

## GitHub Webhook Integration (Auto-trigger Builds) 🔄 

Go to GitHub → Your Repo → Settings → Webhooks → Add Webhook

Payload URL: http://<your-local-ip>:8080/github-webhook/

Content Type: application/json

Events: Just the push event

Save

Make sure your Jenkins server is accessible from GitHub (use ngrok or localtunnel if needed):

![webhook](https://i.postimg.cc/RC3fQQdy/Screenshot-2025-07-20-001315.png)



## Verify the Deployment through Jenkins pipeline🔍

Now, let's check if our pipeline trigger and run the content of our Jenkinsfile.

![jenkins trigger](https://i.postimg.cc/8k29spf6/Screenshot-2025-07-20-071729.png)

### Update Helm Chart and push changes

Now, we will open 'Values.yaml' in our webapp directory and change the replicacount to 3, then save.

![edit value.yaml](https://i.postimg.cc/prXYXmgD/Screenshot-2025-07-20-022548.png)

We are also going to open 'deployment.yaml' in 'template directory' under resources block, and update the resource requests as follows:

```
resources:

requests:

memory: "180Mi"

cpu: "120m"
```
![deployment.yaml](https://i.postimg.cc/fLHSCBr9/Screenshot-2025-07-21-050947.png)

Looking at the image above, we can see our deployment.yaml is in modular form, referencing values.yaml. So we'll need to edit our resource block in values.yaml instead.

![edit resource block](https://i.postimg.cc/d3b1NXmL/Screenshot-2025-07-21-051845.png)

We commit and push changes to our GitHub repository. This way, Jenkins can pick up our changes through a webhook and apply the necessary changes.

![commit and push](https://i.postimg.cc/tCJwBJYZ/Screenshot-2025-07-20-025741.png)

Now let's see if GitHub picks up our changes

![jenkins update](https://i.postimg.cc/TwhNFfRq/Screenshot-2025-07-20-072252.png)

Now we can see our Jenkins apply our changes, which means we have successfully integrated Helm with Jenkins

## 9. Pipeline Flow Explanation

- Jenkins pulls Helm chart from Git

- Helm deploys or updates the application

- Kubernetes applies changes automatically

- This is a simplified CI/CD pipeline suitable for beginners.

## 10. 9. Demonstration (Step-by-Step)

- Push Helm chart to GitHub

- Trigger Jenkins build manually or via webhook

- Observe Jenkins pipeline execution

- Verify deployment on Kubernetes

- Modify values.yaml

- Re-run pipeline

- Observe upgrade

- Roll back if needed  

## 11. Security Measures Summary

```
Area	                                       Measure

Jenkins	                             Authentication enabled

Jenkins	                              Credential Store

Helm	                                 Versioned releases

Kubernetes	                          Controlled deployments
```
Security is intentionally simple, focusing on awareness rather than complexity.

## 12. Conclusion

This capstone project successfully demonstrates:

- Helm chart fundamentals

- Jenkins CI/CD automation

- Safe deployment practices

- Beginner-level DevOps security

It provides a strong foundation for progressing into advanced CI/CD, Kubernetes, and cloud-native deployments.





