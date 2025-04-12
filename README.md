Here's a `README.md` file that explains the full process from Part 2 (Jenkins with Ansible via Docker Compose) *without including the actual scripts*. It outlines the purpose, structure, and instructions so others can follow along while keeping the code/scripts in separate files.

---

# Jenkins + Ansible CI/CD Automation Setup

This project demonstrates how to set up a **Jenkins CI/CD pipeline** using **Docker Compose** to automate remote deployments via **Ansible**. The goal is to integrate Jenkins and Ansible for seamless automation of infrastructure and application deployments to remote servers.

---

## 🚀 Project Overview

### Part 2: Jenkins with Ansible via Docker Compose

We use Docker Compose to:
- Deploy Jenkins with required configurations.
- Set up an Ansible environment.
- Mount shared directories to allow Jenkins to trigger Ansible playbooks.
- Enable passwordless SSH access to remote servers for deployment.

---

## 🧱 Project Structure

```
project-root/
├── ansible/
│   ├── deploy.yml            # Ansible playbook for deployment
│   ├── inventory             # Static Ansible inventory file  
├── Jenkinsfile               # CI/CD pipeline definition
├── docker-compose.yml       # Jenkins + Ansible setup
├── private_key               # SSH key for remote access 
└── README.md                 # Project documentation
```

---

## ⚙️ How It Works

1. **Jenkins Setup with Docker Compose**
   - Jenkins is containerized and configured to run without the setup wizard.
   - A shared directory (`./ansible`) is mounted to Jenkins to access playbooks.
   - Docker socket is mounted to allow Jenkins to run containers.

2. **Ansible Sidecar Container**
   - A separate lightweight container is used for running Ansible.
   - Playbooks are mounted from the same shared directory.
   - This container installs and runs Ansible, keeping it isolated from Jenkins.

3. **SSH Key Authentication**
   - Passwordless access is enabled via an SSH key pair.
   - The private key is either mounted in the containers or added to Jenkins credentials.
   - The public key is copied to the remote host's `~/.ssh/authorized_keys`.

4. **Ansible Inventory Management**
   - A static `inventory` file lists the IPs or hostnames of target servers.
   - The inventory is referenced in the Jenkins pipeline when executing the playbook.

5. **Jenkins Pipeline**
   - Pulls source code from a Git repository.
   - Executes Ansible playbook inside the Ansible container.
   - Deploys to target servers based on the inventory file.

---

## 🔑 Prerequisites

- Docker & Docker Compose installed
- A remote server accessible via SSH
- Public SSH key added to the remote server
- Ports 8080 (Jenkins) and 50000 (agent) available
- Git repository for Jenkins pipeline integration

---

## 🧪 Steps to Run

1. Clone this repository:
   ```bash
   git clone https://github.com/your-username/jenkins-ansible-cicd.git
   cd jenkins-ansible-cicd
   ```

2. Start Jenkins and Ansible services:
   ```bash
   docker-compose up -d
   ```

3. Access Jenkins:
   ```
   http://localhost:8080
   ```

4. Configure Jenkins:
   - Install required plugins:
     - Git Plugin
     - Docker Pipeline
     - Ansible Plugin
   - Create a **Pipeline Job**
   - Use the `Jenkinsfile` from this repo as the pipeline definition

5. Trigger the pipeline:
   - Jenkins will check out code, install Ansible (if needed), and run the playbook on the remote server

---

## 🛡️ Security Notes

- Never commit your private SSH keys to version control.
- Use Jenkins credentials manager to handle sensitive data securely.
- Apply firewall rules to restrict access to your Jenkins server.
- Consider adding authentication and role-based access control (RBAC).

---



