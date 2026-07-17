# Ansible Nginx Deployment on AWS EC2

## Project Overview

This project demonstrates how to automate the installation and configuration of an Nginx web server on an AWS Ubuntu EC2 instance using Ansible.

---

## Technologies Used

- AWS EC2
- Ubuntu 24.04 LTS
- Ansible
- SSH
- Nginx
- Git
- GitHub

---

## Prerequisites

### AWS

Create two Ubuntu EC2 instances.

### Control Node

- Ubuntu Server 24.04 LTS
- SSH (22) → Your IP

### Managed Node

- Ubuntu Server 24.04 LTS
- SSH (22) → Control Node Security Group (Recommended) or Control Node Public IP
- HTTP (80) → Anywhere (0.0.0.0/0)

---

## Step 1 - Connect to the Control Node

```bash
ssh -i control-key.pem ubuntu@<CONTROL_NODE_PUBLIC_IP>
```

---

## Step 2 - Install Ansible

Update package index.

```bash
sudo apt update
```

Install Ansible.

```bash
sudo apt install ansible -y
```

Verify installation.

```bash
ansible --version
```

---

## Step 3 - Generate SSH Key

Generate an SSH key pair.

```bash
ssh-keygen
```

Display the public key.

```bash
cat ~/.ssh/id_ed25519.pub
```

---

## Step 4 - Configure Passwordless SSH

On the Managed Node:

Create the SSH directory.

```bash
mkdir -p ~/.ssh
```

Set directory permissions.

```bash
chmod 700 ~/.ssh
```

Open the `authorized_keys` file.

```bash
vim ~/.ssh/authorized_keys
```

Paste the Control Node public key into the file.

Set file permissions.

```bash
chmod 600 ~/.ssh/authorized_keys
```

---

## Step 5 - Verify Passwordless SSH

From the Control Node:

```bash
ssh ubuntu@<MANAGED_NODE_PUBLIC_IP>
```

---

## Step 6 - Create Project Directory

```bash
mkdir ~/ansible-project
```

```bash
cd ~/ansible-project
```

---

## Step 7 - Create Inventory

Create the inventory file.

```bash
vim inventory
```

Contents:

```ini
[web]
<MANAGED_NODE_PUBLIC_IP> ansible_user=ubuntu
```

Verify the inventory.

```bash
cat inventory
```

---

## Step 8 - Test Ansible Connectivity

```bash
ansible web -i inventory -m ping
```

Expected output:

```text
SUCCESS => {
    "ping": "pong"
}
```

---

## Step 9 - Create the Playbook

Create the playbook.

```bash
vim nginx.yml
```

Paste:

```yaml
---
- name: Install and Configure Nginx
  hosts: web
  become: yes

  tasks:

    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Start Nginx
      service:
        name: nginx
        state: started

    - name: Enable Nginx
      service:
        name: nginx
        enabled: yes
```

Verify the playbook.

```bash
cat nginx.yml
```

---

## Step 10 - Validate the Playbook

```bash
ansible-playbook --syntax-check -i inventory nginx.yml
```

---

## Step 11 - Execute the Playbook

```bash
ansible-playbook -i inventory nginx.yml
```

Expected output:

```text
PLAY RECAP

ok=5
changed=2
failed=0
```

---

## Step 12 - Verify Nginx

SSH into the Managed Node.

```bash
systemctl status nginx
```

Expected output:

```text
Active: active (running)
```

Or open the following URL in your browser:

```text
http://<MANAGED_NODE_PUBLIC_IP>
```

You should see the **Welcome to nginx!** page.

---

## Project Structure

```text
ansible-project/
│
├── .gitignore
├── inventory
├── nginx.yml
└── README.md
```

---

## Useful Commands

Check Ansible version.

```bash
ansible --version
```

Test connectivity.

```bash
ansible web -i inventory -m ping
```

Validate the playbook.

```bash
ansible-playbook --syntax-check -i inventory nginx.yml
```

Run the playbook.

```bash
ansible-playbook -i inventory nginx.yml
```

Dry run.

```bash
ansible-playbook -i inventory nginx.yml --check
```

---

## Author

**Aravind CJ**
