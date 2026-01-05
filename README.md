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
```
sudo apt install openjdk-17-jdk -y
```
java -version

```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
/usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install jenkins -y
```
