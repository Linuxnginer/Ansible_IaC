# 🚀 Hybrid App Deployment with Ansible

This project demonstrates how to use **Ansible** to deploy a microservice Python application across **on-premises** and **cloud-based** Linux servers.

---

## 🧰 What This Project Demonstrates

- ✅ Automated deployment using Ansible  
- ✅ Streamlit Python application  
- ✅ Hybrid infrastructure management (on-prem & cloud)  
- ✅ Role-based and inventory-driven architecture  
- ✅ Full **Infrastructure as Code (IaC)** using DevOps practices  

---

## 🌐 Supported Environments

- RHEL / CentOS / Rocky Linux 8 or 9  
- Cloud instances (e.g., **AWS**, **Azure**, **GCP**)  
- On-prem virtual machines or bare-metal servers  

---

## ⚙️ Prerequisites & Setup

Make sure Ansible is properly set up to manage and deploy tasks across your infrastructure.
External link: Install ansible service
https://www.ansiblepilot.com/articles/ansible-install/

### 1. Create an Ansible user on each target server

Run the following on **each managed node** (on-prem and cloud):

```bash
sudo useradd -m -s /bin/bash ansible
sudo passwd ansible
sudo usermod -aG wheel ansible
```

### 2. Generate SSH keys on your Ansible control node (e.g., `10.0.1.200`)

```bash
ssh-keygen -t ed25519 -C "ansiblekeys"
```

### 3. Copy the public key to each managed node

```bash
ssh-copy-id ansible@10.0.1.201         # On-prem server
ssh-copy-id ansible@199.232.10.X      # Azure cloud server
```

### 4. Create an inventory file

Name it `hosts` or `inventory.ini`, and structure it like this:

```ini
[onprem]
onprem1 ansible_host=10.0.1.201 ansible_user=ansible

[azure]
azurerhel ansible_host=199.232.10.X ansible_user=ansible

[all:vars]
ansible_ssh_private_key_file=~/.ssh/id_ed25519
```

### 5. Test connectivity

From your Ansible control node:

```bash
ansible all -i hosts -m ping
```

✅ A successful result should look like:

```json
onprem1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
azurerhel | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

---

## 🚀 Deploy the Application

Before deploying the app, make sure port `8080` is open to allow web traffic (required for Streamlit access).

### 1. Allow Port 8080 on All Remote Hosts

Run the following playbook to install and configure the firewall:

```bash
ansible-playbook -i hosts firewall_port.yml
```

This will:
- Install and start `firewalld` if it's not already running  
- Open TCP port 8080 permanently  
- Reload the firewall rules  

---

### 2. Deploy the Streamlit Microservice App

Run the main playbook to install Python dependencies, transfer the app, and launch the service:

```bash
ansible-playbook -i hosts deploy_app.yml
```

The application will be available at:

```
http://<remote_server_ip>:8080
```

📌 **Note:** Make sure your cloud provider's firewall/security group (e.g., in AWS or Azure) allows inbound traffic on port `8080`.

---
