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
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 154449.png>)
E:\Demo-Projects\Linux-demo\Evidences\Screenshot 2025-12-01 154449.png
Screenshot 2025-12-01 154449.png

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
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 155707.png>)
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 160132.png>)
### Install Java (OpenJDK 17)

```bash
sudo yum install java-17-amazon-corretto -y
java -version
```

---
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 160603.png>)
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 160703.png>)

# **4️. Check System Information (Memory, CPU, Disk)**

###  **Check CPU**

```bash
lscpu
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 160836.png>)
###  **Check Memory**

```bash
free -h
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 160921.png>)

###  **Check Disk Usage**

```bash
df -h
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 160954.png>)

#  **LEVEL 2 – Intermediate (Daily DevOps Tasks)**

---

# **1️. Automate Backups with Cron**

### **Example: Backup `/opt/project` every day at 1 AM → saved to `/backup/project`**

### Step 1: Create backup directory

```bash
sudo mkdir -p /backup/project
```

### Step 2: Create a backup script

```bash
sudo nano /usr/local/bin/project_backup.sh
```

Paste:

```bash
#!/bin/bash
SRC="/opt/project"
DEST="/backup/project/project-$(date +%F-%H-%M).tar.gz"
tar -czf $DEST $SRC
```

Make executable:

```bash
sudo chmod +x /usr/local/bin/project_backup.sh
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 164557.png>)
### Step 3: Create cron job

Open cron:

```bash
sudo crontab -e
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 165055.png>)
Add:

```bash
0 1 * * * /usr/local/bin/project_backup.sh
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 165141.png>)
✔ Runs every day at 1AM
✔ Backup file name includes date/time

---

# **2️. Create Shell Scripts**

These are real DevOps scripts used in production.

---

## **A) Log Cleanup Script (Delete logs older than 15 days)**

Create script:

```bash
sudo nano /usr/local/bin/log_cleanup.sh
```

Paste:

```bash
#!/bin/bash
find /var/log -type f -name "*.log" -mtime +15 -exec rm -f {} \;
```

Make executable:

```bash
sudo chmod +x /usr/local/bin/log_cleanup.sh
```

Add cron job (run daily at 2 AM):

```bash
sudo crontab -e
```

Add:

```bash
0 2 * * * /usr/local/bin/log_cleanup.sh
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 170851.png>)
---

## **B) Service Restart Script (Example: Restart Nginx if down)**

```bash
sudo nano /usr/local/bin/restart_nginx.sh
```

Paste:

```bash
#!/bin/bash
if ! systemctl is-active --quiet nginx; then
    systemctl restart nginx
fi
```

Run automatically every 5 minutes:

```bash
sudo crontab -e
```

Add:

```bash
*/5 * * * * /usr/local/bin/restart_nginx.sh
```

---
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 171444.png>)
## **C) Health Check Script (CPU, Memory, Disk)**

Save output to `/var/log/healthcheck.log`.

```bash
sudo nano /usr/local/bin/health_check.sh
```

Paste:

```bash
#!/bin/bash

echo "======== Health Check: $(date) ========" >> /var/log/healthcheck.log

echo "CPU Usage:" >> /var/log/healthcheck.log
top -bn1 | grep "Cpu" >> /var/log/healthcheck.log

echo "Memory Usage:" >> /var/log/healthcheck.log
free -h >> /var/log/healthcheck.log

echo "Disk Usage:" >> /var/log/healthcheck.log
df -h >> /var/log/healthcheck.log

echo "" >> /var/log/healthcheck.log
```

Make executable:

```bash
sudo chmod +x /usr/local/bin/health_check.sh
```

Cron (every 10 minutes):

```bash
sudo crontab -e
```

Add:

```bash
*/10 * * * * /usr/local/bin/health_check.sh
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 172102.png>)
---

# **3️. Manage Logs under `/var/log`**

Linux logs live in `/var/log`.

### Common log files:

| Log File                   | Description         |
| -------------------------- | ------------------- |
| `/var/log/messages`        | General system logs |
| `/var/log/secure`          | SSH & security      |
| `/var/log/nginx/*.log`     | Nginx logs          |
| `/var/log/audit/audit.log` | Security audit      |
| `/var/log/dmesg`           | Kernel logs         |
| `/var/log/auth.log`        | User auth logs      |

### Useful commands:

#### View logs in real time

```bash
sudo tail -f /var/log/messages
```

#### View SSH logs

```bash
sudo tail -f /var/log/secure
```

#### Nginx logs

```bash
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

#### Search inside logs

```bash
grep "error" /var/log/messages
```

#### Check log size

```bash
sudo du -h /var/log
```

---

# **4️. Monitor System Performance & Troubleshoot Services**

---

## **A) CPU Monitoring**

```bash
top
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 161430.png>)


## **B) Memory Monitoring**

```bash
free -h
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 162621.png>)
---

## **C) Disk Monitoring**

```bash
df -h
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 162725.png>)

## **D) Network Monitoring**

```bash
ss -tulnp
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 163908.png>)
Check open ports:

```bash
netstat -tulnp
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 164005.png>)
Ping to test connectivity:

```bash
ping google.com
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 164059.png>)
---

## **E) Service Troubleshooting**

### Check service status

```bash
systemctl status nginx
```

### Check logs for service

```bash
journalctl -u nginx
```
![alt text](<../../Demo-Projects/Linux-demo/Evidences/Screenshot 2025-12-01 164239.png>)
### Restart service

```bash
sudo systemctl restart nginx
```

### Enable service on boot

```bash
sudo systemctl enable nginx
```

---



#  **LEVEL 3 – Advanced (Production-Ready Linux Administration)**

---

# **1️. Create a Custom systemd Service (Run Your App as a Service)**

You want to run your application (Spring Boot / Node / Python) as a managed service.

### Example: Your app jar file → `/opt/app/myapp.jar`

### **Step 1: Create a service file**

```bash
sudo nano /etc/systemd/system/myapp.service
```

Paste:

```ini
[Unit]
Description=My Application Service
After=network.target

[Service]
User=ec2-user
WorkingDirectory=/opt/app
ExecStart=/usr/bin/java -jar /opt/app/myapp.jar
Restart=always
RestartSec=10
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=myapp

[Install]
WantedBy=multi-user.target
```

### **Step 2: Apply changes**

```bash
sudo systemctl daemon-reload
```

### **Step 3: Start and enable**

```bash
sudo systemctl start myapp
sudo systemctl enable myapp
```

### **Check logs**

```bash
journalctl -u myapp -f
```

✔ Runs automatically at boot
✔ Restarts if the app crashes
✔ Sends logs to `journalctl`

---

# **2️. SSH Hardening for Production Security**

Edit SSH config:

```bash
sudo nano /etc/ssh/sshd_config
```

Apply below changes:

```text
# Disable root login
PermitRootLogin no

# Disable password login
PasswordAuthentication no

# Allow only specific users
AllowUsers ec2-user alice

# Reduce login attempts
MaxAuthTries 3

# Disable unused features
X11Forwarding no
```

### Restart SSH:

```bash
sudo systemctl restart sshd
```


# **3️. LVM Setup for Storage Scaling (Real DevOps Task)**

### **Use Case:**

Your disk space is low → you add a new EBS volume and want to extend storage.

---

## **Step 1: Attach a new disk (example: `/dev/xvdf`)**

Check disks:

```bash
lsblk
```

---

## **Step 2: Create physical volume (PV)**

```bash
sudo pvcreate /dev/xvdf
```

---

## **Step 3: Add PV to volume group (VG)**

Assume VG name is `vgdata`:

```bash
sudo vgextend vgdata /dev/xvdf
```

Check:

```bash
sudo vgs
```

---

## **Step 4: Extend logical volume (LV)**

Assume your LV is `/dev/vgdata/lvapp`.

```bash
sudo lvextend -l +100%FREE /dev/vgdata/lvapp
```

---

## **Step 5: Resize the filesystem**

For ext4:

```bash
sudo resize2fs /dev/vgdata/lvapp
```

For XFS:

```bash
sudo xfs_growfs /mountpoint
```

---

# **4️. Configure Firewall Rules (firewalld / ufw)**

---

##  **A) firewalld (CentOS / RHEL / Amazon Linux)**

### Start and enable:

```bash
sudo systemctl enable firewalld --now
```

### Allow ports:

Allow HTTP:

```bash
sudo firewall-cmd --permanent --add-service=http
```

Allow custom port (example 8080):

```bash
sudo firewall-cmd --permanent --add-port=8080/tcp
```

Reload:

```bash
sudo firewall-cmd --reload
```

List rules:

```bash
sudo firewall-cmd --list-all
```

---


# **5️. Implement logrotate for Application Logs (Production Log Management)**

Your app writes logs to:

```
/opt/app/logs/app.log
```

### Create logrotate config:

```bash
sudo nano /etc/logrotate.d/myapp
```

Paste:

```text
/opt/app/logs/app.log {
    daily
    rotate 14
    compress
    missingok
    notifempty
    copytruncate
}
```

### What this means:

| Option           | Meaning                                          |
| ---------------- | ------------------------------------------------ |
| **daily**        | Rotate logs daily                                |
| **rotate 14**    | Keep 14 log files                                |
| **compress**     | gzip old logs                                    |
| **copytruncate** | Copies then truncates active log (safe for apps) |
| **notifempty**   | Skip empty logs                                  |

### Test configuration:

```bash
sudo logrotate -d /etc/logrotate.d/myapp
```

Force run:

```bash
sudo logrotate -f /etc/logrotate.d/myapp
```

---
