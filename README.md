# ansible
this is my READNME.MD
t# Complete Ansible Guide

## 🧠 What is Ansible?
Ansible is an **open-source automation tool** primarily used for **configuration management**, **software deployment**, and **task automation**.  
It is **agentless** — you only install Ansible on a control node and manage other systems remotely over SSH or WinRM.

---

## ⚙️ Core Components of Ansible

| Component | Purpose / Description |
|------------|-----------------------|
| **Control Node** | Central machine where Ansible is installed and automation triggered. |
| **Managed Nodes** | Remote servers managed by Ansible (no agent required). |
| **Inventory** | File listing managed nodes, grouped by environment or function. |
| **Modules** | Reusable scripts for specific tasks like installing packages or managing files. |
| **Playbooks** | YAML-based automation scripts defining tasks and configurations. |
| **Roles** | Structured reusable units for organizing playbooks. |
| **Plugins** | Extend Ansible functionality (connections, inventory, callbacks, etc.). |
| **Templates** | Dynamic configuration files generated using variables and logic. |

---

## 🧩 Installing Ansible

### On Ubuntu:
```bash
sudo apt update
sudo apt install ansible -y
```

### On CentOS/RHEL:
```bash
sudo dnf install epel-release
sudo dnf install ansible -y
```

---

## 🔐 Generate SSH Keys (for Passwordless Authentication)

```bash
ssh-keygen -t rsa -b 2048
ssh-copy-id ubuntu@<server_ip>
```
This allows Ansible to connect securely to managed nodes without needing passwords.

---

## 📂 Project Structure Example
```
ansible/
├── inventories/
├── playbooks/
├── roles/
```

---

## 🧾 Inventory Example
```ini
[servers]
server1 ansible_host=192.168.1.10
server2 ansible_host=192.168.1.11

[servers:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3
```

---

## 🧪 Test Connectivity
```bash
ansible all -m ping -i inventories/prod
```

---

## ⚡ Run Ad-Hoc Commands

Update all servers:
```bash
ansible all -m apt -a "upgrade=yes update_cache=yes cache_valid_time=86400" --become -i inventories/prod
```

Check uptime:
```bash
ansible all -m command -a "uptime" -i inventories/prod
```

---

## 📘 Basic Playbook Example
```yaml
---
- name: Install and Start Nginx
  hosts: webservers
  become: yes
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
    - name: Ensure Nginx is running
      service:
        name: nginx
        state: started
```

Run the playbook:
```bash
ansible-playbook -i inventory.ini nginx.yml
```

---

## 🧱 Advanced Playbook with Roles & Error Handling

**Directory Structure:**
```
project/
├── inventories/
├── playbooks/
│   └── site.yml
├── roles/
│   └── web/
│       ├── tasks/
│       ├── handlers/
│       ├── templates/
│       ├── files/
│       └── vars/
```

**Playbook (site.yml):**
```yaml
---
- name: Web servers setup
  hosts: web
  become: yes
  roles:
    - web
```

**Role Example (roles/web/tasks/main.yml):**
```yaml
---
- name: Install Nginx
  apt:
    name: nginx
    state: present
  register: nginx_status
  ignore_errors: yes

- name: Ensure Nginx is running
  service:
    name: nginx
    state: started
```

---

## 🧮 Variables in Ansible

Variables are placeholders for dynamic values — making playbooks reusable and flexible.

### Common Ways to Define Variables

| Location | Example |
|-----------|----------|
| Playbook-level | `vars: http_port: 80` |
| Inventory file | `host1 ansible_user=ubuntu app_env=prod` |
| Group/Host files | `group_vars/web.yml`, `host_vars/db1.yml` |
| Role variables | `roles/web/vars/main.yml` |
| Extra-vars (CLI) | `-e "var1=value1 var2=value2"` |
| Facts | `ansible_hostname`, `ansible_os_family` |

**Example:**
```yaml
---
- hosts: web
  vars:
    nginx_port: 8080
    app_root: /var/www/app
  tasks:
    - name: Configure Nginx
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
```

Template file (`nginx.conf.j2`):
```jinja
server {
    listen {{ nginx_port }};
    root {{ app_root }};
}
```

---

## 🧰 Types of Variables in Ansible

| Variable Type | Description / Use Case |
|----------------|------------------------|
| **Playbook/Inline** | Defined directly in playbooks for short-term overrides. |
| **Inventory** | Defined in inventory files for host/group customization. |
| **Host/Group Vars** | Stored in host_vars/ or group_vars/ for fine control. |
| **Role Vars** | Used within roles for defaults or constants. |
| **Facts** | Auto-gathered system info (OS, IP, hostname). |
| **Registered** | Store output of tasks for later use. |
| **Environment** | Set runtime environment variables. |
| **Extra Vars** | Passed at runtime using `-e` (highest precedence). |
| **Magic Vars** | Built-in vars like `inventory_hostname`, `group_names`. |

---

## 🧠 Key Use Cases of Ansible

- **Configuration Management** – automate and standardize server setup.  
- **Application Deployment** – deploy software and updates reliably.  
- **Cloud Provisioning** – create and manage cloud resources (AWS, Azure, GCP).  
- **CI/CD Integration** – automate build, test, deploy pipelines.  
- **Security Automation** – patching and compliance.  
- **Network Automation** – manage routers, switches, and firewalls.  
- **Orchestration** – coordinate multi-tier deployments.

---
