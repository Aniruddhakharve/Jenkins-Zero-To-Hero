# Jenkins-Zero-To-Hero 🚀

This repository demonstrates how to set up a complete Jenkins pipeline project for a simple Java HelloWorld application using:

- A **Jenkins master + agent** setup
- A **label-based pipeline**
- A **Docker agent-based pipeline**

Perfect for beginners to learn DevOps CI/CD with Jenkins, GitHub, and Docker.

---

## 🔧 Prerequisites

You will need:

- 2 Linux machines (AWS EC2 or VirtualBox or local VMs):
  - **1 Master node**
  - **1 Agent (slave) node**
- Open ports: `8080` (Jenkins) and `22` (SSH)
- Basic Linux knowledge

---

## 🛠️ Step-by-Step Jenkins Setup

### 1. Install Java (on both master and agent)

```bash
sudo yum install -y java-17-openjdk
java -version
```
### 2. Install Jenkins on Master

```bash
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

### 3. Access Jenkins
- Go to: http://< your-master-ip >:8080
- Initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### 4. Install Recommended Plugins
- Install all recommended plugins during setup.
- Create your Jenkins admin user.
---

## 🔗 Connect Slave (Agent) Node to Jenkins Master

### 1. On Agent Node, Create a Jenkins User
```bash
sudo useradd jenkins
sudo mkdir /home/jenkins && sudo chown jenkins:jenkins /home/jenkins
sudo passwd jenkins
```
### 2. Enable SSH Access to Agent
- Make sure port 22 is open and sshd is running:
```bash
sudo systemctl start sshd
```
### 3. On Jenkins Master, Configure New Node
- Go to: Manage Jenkins → Nodes → New Node
- Name: javapipe
- Type: Permanent Agent
- Add details:
            - Remote root dir: /home/jenkins
            - Launch method: SSH
            - Host: < Agent IP >
            - Credentials: Add Jenkins agent username/password or SSH key
- Click Save and test the connection.
  
---

## ✅ Project 1: Label-Based Java HelloWorld Pipeline
### 📁 Folder: /javapipe
Contains a basic Jenkins pipeline that:
- Compiles HelloWorld.java
- Tests if main method exists
- Runs the app

### 🧪 Jenkinsfile (Label Agent)
```bash
pipeline {
    agent { label 'javapipe' }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Compiling java program...'
                sh '''
                mkdir -p out
                javac -d out HelloWorld.java
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running Test...'
                sh '''
                if grep -q "public static void main" HelloWorld.java; then
                    echo "Main method exists - Test Passed"
                else
                    echo "Test Failed"
                    exit 1
                fi
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Running java app'
                sh 'java -cp out HelloWorld'
            }
        }
    }
}
```
---

## ✅ Project 2: Docker Agent-Based Pipeline
### 📁 Folder: /docker-agent
Runs the pipeline inside Docker containers using Jenkins’ Docker agent feature.
📌 Make sure the Jenkins master has:
- Docker installed and running
- Jenkins user added to Docker group:
```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```
### 📦 Install Docker Pipeline Plugin
Go to Manage Jenkins → Plugins → Search and install Docker Pipeline.
### 🧪 Jenkinsfile (Docker Agent)
```bash
pipeline {
    agent none

    stages {
        stage('Back-end') {
            agent {
                docker { image 'maven:3.8.1-adoptopenjdk-11' }
            }
            steps {
                sh 'mvn --version'
            }
        }

        stage('Front-end') {
            agent {
                docker { image 'node:16-alpine' }
            }
            steps {
                sh 'node --version'
            }
        }
    }
}
```
---

## 📂 Folder Structure
```bash
Jenkins-Zero-To-Hero/
│
├── javapipe/
│   ├── Jenkinsfile         # Label-based agent pipeline
│   └── HelloWorld.java     # Simple Java program
│
├── docker-agent/
│   └── Jenkinsfile         # Docker agent pipeline
│
└── README.md               # Full setup instructions
```

---

## 🧠 Learnings from This Project
- Jenkins master-agent architecture
- Running pipelines on label-based and docker-based agents
- GitHub SCM integration
- Docker agent troubleshooting
- Basic CI/CD concepts

---

## 📎 Connect with Me

- This project is part of my DevOps learning journey.
- Check out more on my GitHub: Aniruddhakharve
            
