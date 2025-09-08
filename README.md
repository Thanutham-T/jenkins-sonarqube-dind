# Jenkins + SonarQube (DIND)  

This project is designed for **learning and experimenting** with **Jenkins** and **SonarQube** using **Docker-in-Docker (DIND)**.  
It includes a custom Jenkins controller and agent setup for building, testing, and code analysis.

---

## Table of Contents

- [Features](#features)  
- [Prerequisites](#prerequisites)  
- [Setup](#setup)  
- [Usage](#usage)  
- [Notes](#notes)  

---

## Features

- Jenkins Controller with **JDK 21**  
- Jenkins SSH Agent with **JDK 21** and **Python**  
- Docker-in-Docker support for running builds inside containers  
- Ready for integration with **SonarQube** for static code analysis  

---

## Prerequisites

- Docker ≥ 20.x  
- Docker Compose ≥ 1.29.x  
- Basic knowledge of Jenkins and Docker

---

## Setup

1. **Navigate to the project directory**

```bash
cd cicd-jenkins
```

2. **Build the Jenkins Controller image**

```bash
docker build -f Dockerfile.jenkinsCtrl -t jenkins/jenkins:custom-jdk21 .
```

3. **Build the Jenkins Agent image**

```bash
docker build -f Dockerfile.jenkinsAgent -t jenkins/ssh-agent:custom-deb-jdk21-python .
```
4. **Generate SSH Key for Jenkins Agent**

Generate an SSH key pair for secure communication between the Jenkins controller and agent.

<details>
<summary><strong>On Windows</strong></summary>

```bash
./setup.bat
```
</details>

<details>
<summary><strong>On Linux</strong></summary>

```bash
./setup.sh
```
</details>

This will create a new SSH key pair in the appropriate directory for use by the Jenkins agent.

5. **Update `docker-compose.yaml` with Your SSH Public Key**

After generating the SSH key, update the `JENKINS_AGENT_SSH_PUBKEY` environment variable in your `docker-compose.yaml` file with the contents of your newly created public key (typically found at `id_rsa.pub`):

```yaml
environment:
    - JENKINS_AGENT_SSH_PUBKEY: "ssh-rsa AAAA...your_public_key_here..."
```

Replace `ssh-rsa AAAA...your_public_key_here...` with the actual contents of your public key file.

6. **Start all services**

```bash
docker-compose up -d
```

7. **set docker Permissions for Jenkins**

⚠️ **Warning**: For learning purposes, changing permissions on the Docker socket is convenient.  

> 7.1) **Start a bash session inside the Jenkins container as root**

```bash
docker exec -u root -it jenkins bash
```

> 7.2) **Give Jenkins permission to access Docker**

```bash
chmod 666 /var/run/docker.sock
```

---

## Usage

* Access Jenkins: `http://localhost:8080`
* Access Sonarqube: `http://localhost:9001`

---

## Notes

* **Default Jenkins admin password**: check the logs or `/var/jenkins_home/secrets/initialAdminPassword`
* Use **agents** for executing builds, tests, and SonarQube scans
* The jenkinsCtrl image is your main Jenkins controller.
* The jenkinsAgent image is your agent that Jenkins controller access via SSH.
* Ensure Docker is running on your host before executing these commands.
