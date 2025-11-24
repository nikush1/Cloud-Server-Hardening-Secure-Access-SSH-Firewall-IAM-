# 🛡️ Cloud Server Hardening & Secure Access (SSH + Firewall + IAM)

**Author:** Niraj Kumar Sharma  
**Course:** CY-5A – Ethical Hacking  
**Project ERP:** 6700451  
**Title:** Cloud Server Hardening & Secure Access  
**Date:** November 2025  

---

## 📘 Project Overview  
This project demonstrates comprehensive Linux server hardening by deploying, configuring, and securing an Ubuntu-based cloud server. It implements least-privilege access, SSH key-based authentication, firewall rules (UFW), intrusion prevention using Fail2Ban, and system activity monitoring with Auditd — forming a strong security baseline for real-world environments.

---

## 🎯 Objectives  
- Harden Ubuntu Server to minimize attack surfaces  
- Enforce least-privilege access (IAM-like management)  
- Disable root SSH login and password-based authentication  
- Configure UFW firewall to allow only essential traffic  
- Prevent brute-force SSH attacks using Fail2Ban  
- Enable Auditd for detailed system logging & auditing  

---

## 🧰 Prerequisites  
- Ubuntu Server 20.04 or 22.04 (Cloud VM / VMware / VirtualBox)  
- SSH Client (Terminal / PowerShell)  
- Local machine for generating SSH keys  
- Internet access  

---

## ⚙️ Step-by-Step Implementation  

### **1️⃣ Update System & Install Essential Packages**
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y wget curl git vim
```

---

### **2️⃣ Install & Start SSH Server**
```bash
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
hostname -I
```

---

### **3️⃣ Create a New Administrative User**
```bash
sudo adduser projectuser01
```

Grant sudo access:
```bash
echo "projectuser01 ALL=(ALL:ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/projectuser01
sudo chmod 440 /etc/sudoers.d/projectuser01
sudo cat /etc/sudoers.d/projectuser01
```

---

### **4️⃣ SSH Hardening (Disable Root Login & Password Auth)**
```bash
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo systemctl restart ssh
grep -Ei 'PermitRootLogin|PasswordAuthentication' /etc/ssh/sshd_config
```

---

### **5️⃣ Generate SSH Key & Configure Authentication**

Generate SSH key (local machine):
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/project_key_$(date +%Y%m%d) -C "project_submission"
```

Copy to server:
```bash
ssh-copy-id -i ~/.ssh/project_key_$(date +%Y%m%d).pub projectuser01@<SERVER_IP>
```

Manual method:
```bash
sudo mkdir -p /home/projectuser01/.ssh
sudo nano /home/projectuser01/.ssh/authorized_keys
sudo chown -R projectuser01:projectuser01 /home/projectuser01/.ssh
sudo chmod 700 /home/projectuser01/.ssh
sudo chmod 600 /home/projectuser01/.ssh/authorized_keys
```

---

### **6️⃣ Configure Firewall (UFW)**
```bash
sudo apt install -y ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status verbose
```

---

### **7️⃣ Install & Configure Fail2Ban**
```bash
sudo apt install -y fail2ban
sudo systemctl enable --now fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Edit `/etc/fail2ban/jail.local`:
```
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
```

Restart Fail2Ban:
```bash
sudo systemctl restart fail2ban
sudo fail2ban-client status sshd
sudo tail -n 200 /var/log/fail2ban.log
```

---

### **8️⃣ Install & Enable Auditd**
```bash
sudo apt install -y auditd audispd-plugins
sudo systemctl enable --now auditd
```

Add log monitoring rules:
```bash
sudo auditctl -w /usr/bin/sudo -p x -k sudo_exec
sudo auditctl -w /var/log/auth.log -p wa -k authlog
```

Check logs:
```bash
sudo ausearch -k sudo_exec -ts today
sudo ausearch -k authlog -ts today
```

---

## 🧩 Optional (Advanced Monitoring)  
Integrate with:  
- Wazuh  
- Splunk  
- Graylog  
- Any SIEM  

For centralized logging, dashboarding, and alerts.

---

## 🧠 Key Learnings  
- Secure user administration with sudo privileges  
- SSH hardening using key-based authentication  
- Traffic restriction using UFW firewall  
- Detecting & preventing brute-force attacks with Fail2Ban  
- Tracking user actions & auth logs using Auditd  
- Hands-on experience in securing a cloud server  

---

## ✅ Conclusion  
This project successfully demonstrates secure cloud server configuration using open-source security tools. By combining SSH hardening, firewalling, intrusion prevention, and auditing, the server is protected against unauthorized access while maintaining clear visibility into all critical system events.

---

## 📁 Project File  
**Cloud Server Hardening Project (Niraj Kumar Sharma) CY-5A.pdf**  
Full documentation with screenshots and implementation steps included.

---

## 📌 License  
This project is intended strictly for academic and ethical cybersecurity use.  
Unauthorized or malicious usage of these configurations is strictly prohibited.
