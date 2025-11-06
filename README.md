# Linux IAM & Hardening (User, group, permissions)

A hands-on project demonstrating Identity & Access Management (IAM) and system hardening on Ubuntu Linux. Design secure user/group models, identify misconfigurations, and implement remediation strategies.

## 📋 Project Overview

This project provides a complete lifecycle for Linux IAM security:
- *Secure Baseline*: Design and implement least-privilege access controls
- *Vulnerability Assessment*: Identify deliberate misconfigurations in a lab environment
- *Remediation*: Fix security issues and harden the system
- *Documentation*: Produce security policies and checklists

## 🎯 Learning Objectives

After completing this lab, I will be able to:
- Design secure user and group structures following principle of least privilege
- Configure granular sudo access controls
- Implement proper file permissions and ACLs
- Set up auditing and monitoring for critical system files
- Identify and remediate common IAM misconfigurations
- Produce professional security documentation

## 🏗 Lab Architecture

### Secure Baseline Environment
- *Ubuntu Server VM* - Primary lab environment
- *User Groups*: admin, dev, auditor
- *Users*: yesh-admin, yesh1-dev, yesh2-dev , yesh-auditor
- *Secure Sudo Rules*: Least-privilege commands per group
- *File Permissions*: Proper ownership and access controls

### Vulnerable Lab Components
- World-readable backup files (/etc/passwd.backup, /etc/shadow.backup)
- Insecure home directory permissions
- World-writable log directories
- Dangerous sudo rules (for demonstration)

### 🔍 Key Features
*1. User & Group Management*
   
- Role-based group structure (admin, dev, auditors)
- Secure user creation with proper primary groups
- Regular access reviews and audits

*2. Sudo Privilege Management*
   
- Least-privilege sudo rules
- Restricted command-specific access
- No unnecessary NOPASSWD directives

 *3. File System Security*

- Proper POSIX permissions for sensitive files
- Setgid bits for shared directories
- ACLs for granular access control

*4. Auditing & Monitoring*

- auditd configuration for critical files
- Real-time monitoring of /etc/sudoers, /etc/passwd, /etc/shadow
- Comprehensive logging and alerting

## 🛠 Technical Components
  *Sudoers Configuration*
- *Admin* - Full system access
- %admin ALL=(ALL:ALL) ALL

- *Developers* - Application-specific commands only
                   *%dev ALL=(root) /usr/bin/systemctl restart nginx,   /usr/bin/systemctl status nginx*
- *Auditors* - No sudo access
## POSIX + ACL commands
- Full access for the owner and group on /opt/myapp and /var/log/myapp
 1. *sudo chmod -R u=rwx,g=rwx,o= /opt/myapp* \
    *sudo chmod -R g+s /opt/myapp*
 2. *sudo chmod -R u=rwx,g=rwx,o= /var/log/myapp* \
    *sudo chmod -R g+s /var/log/myapp* 
- Now with ACL we will give auditors group read+execute permission in /opt/myapp
3. *sudo setfacl -R -m g:auditors:rx  /opt/myapp*   
- Now we will give auditors group to read access for log file 
4. *sudo setfacl -R -m g:auditors:r /var/log/myapp/*
- Now we will verify ACL permissions through getfacl
5. *getfacl /opt/myapp*
6. *getfacl /var/log/myapp*
    
## Audit Rules
- Now we will use auditing to monitor the changes done by users in in passwd or sudoers.d file by using auditd.
  1. *sudo apt install auditd -y*
- Now we will make simple rules for auditing in my-monitoring.rules file. \
  2. *sudo nano /etc/audit/rules.d/my-monitoring.rules*
- First for auditing sudoers file \
*-w /etc/sudoers -p wa -k sudoers_watch* \
*-w /etc/sudoers.d/ -p wa -k sudoers_watch*  
- Now for passwd \
*-w /etc/passwd -p wa -k user_watch*
- Now we will enable auditing \
*systemctl restart auditd* \
*systemctl enable auditd*    

## 📊 Sample Findings & Remediation
- I can rarely find any misconfiguration in the files so now i will intentionaly misconfigure them.
  ## World-readable Backup Files
   *Create backup files with weak permissions* 
1. *sudo touch /etc/passwd.backup*
2. *sudo touch /etc/shadow.backup*
3. *sudo chmod 644 /etc/passwd.backup /etc/shadow.backup*
  ## Insecure Home Directory
  *Create a test user with world-readable home* \
4. *sudo useradd -m testuser* \
5. *sudo chmod 755 /home/testuser*  # Should be 700 \
6. *sudo touch /home/testuser/secret.txt* \
7. *sudo chmod 644 /home/testuser/secret.txt*
 ## World-writable Log Directory
  *Make log directory world-writable* \
8. *sudo mkdir -p /var/log/insecure-app* \
9. *sudo chmod 777 /var/log/insecure-app*  # Very bad privileges for security.

 ## Now we will fix those misconfigurations.
   *BEFORE: World-readable Backup Files* \
     1. *ls -la /etc/.backup** \
  *Remediation Steps*
  - Secure the backup files
- *sudo chmod 600 /etc/.backup** \
   *AFTER: Secure Backup Files* \
   2. *ls -la /etc/.backup**

## 🎓 Learning Outcomes
  *Technical Skills*

- Linux user and group management
- Sudoers file configuration and security
- POSIX permissions and ACLs
- Auditd configuration and monitoring
- Security vulnerability assessment
- System hardening techniques

## 🙏 Acknowledgments
- Ubuntu Security Team
- Linux Audit Framework

<div align="center">
</div>
