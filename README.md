# Linux-Demo
Demo for linux


## Level 1 – Basic (Foundational Skills)
 

# **1️. Set up Users & Groups for Dev Team**

### **Create a group**

```bash
sudo groupadd development-team
```

### **Create users**

```bash
sudo useradd -m -s /bin/bash saivarun
sudo useradd -m -s /bin/bash akhil
sudo useradd -m -s /bin/bash rakesh
```

### **Add users to group**

```bash
sudo usermod -aG development-team saivarun
sudo usermod -aG development-team akhil
sudo usermod -aG development-team rakesh
```

### **Set up SSH access (only if needed)**

```bash
sudo mkdir -p /home/saivarun/.ssh
sudo chmod 700 /home/saivarun/.ssh
sudo chmod 600 /home/saivarun/.ssh/authorized_keys
sudo chown -R saivarun:saivarun /home/saivarun/.ssh
```

---

# **2️. Manage Permissions for Project Directories**

### **Create project directory**

```bash
sudo mkdir -p /opt/project
```

### **Give group ownership**

```bash
sudo chown :development-team /opt/project
```

### **Set permissions**

```bash
sudo chmod 775 /opt/project
```

### Why `775`?

| Number | Meaning                                        |
| ------ | ---------------------------------------------- |
| **7**  | Owner full access                              |
| **7**  | Group full access                              |
| **5**  | Others read & execute                          |

This ensures all dev team members can read/write inside project folder.

---

# **3️. Install Required Packages (git, nginx, java)**

Different Linux systems use different package managers.

---

##  If using Amazon Linux 2023

### Install Git

```bash
sudo yum install git -y
```

### Install Nginx

```bash
sudo yum install nginx -y
sudo systemctl enable nginx --now
sudo systemctl start nginx --start
sudo systemctl status nginx --status
```

### Install Java (OpenJDK 17)

```bash
sudo yum install java-17-amazon-corretto -y
java -version
```

---


# **4️. Check System Information (Memory, CPU, Disk)**

###  **Check CPU**

```bash
lscpu
```


###  **Check Memory**

```bash
free -h
```



###  **Check Disk Usage**

```bash
df -h
```




## Level 2 – Intermediate (Daily DevOps Tasks)
 - Automate backups with Cron
 - Create shell scripts: Log cleanup, service restart, health checks
 - Manage logs under /var/log
 - Monitor system performance and troubleshoot services

## Level 3 – Advanced (Production-Ready Linux Admin)
 - Create custom systemd service for your application
 - SSH hardening for security
 - LVM setup for storage scaling
 - Configure firewall rules
 - Implement logrotate for app logs
