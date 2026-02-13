# Java Application Deployment Lab on DigitalOcean

## Overview

This lab demonstrates how to securely deploy and operate a Java Spring Boot application on a cloud server using **DigitalOcean**, following real-world **Application Manager / DevOps** practices.

The focus is not just running an application, but:
- Hardening the server
- Enforcing secure access
- Building and packaging a Java application
- Deploying and validating the application in production-like conditions

---

## Architecture Summary

- **Client:** Developer workstation (Windows + Git Bash + IntelliJ IDEA)
- **Cloud Provider:** DigitalOcean
- **Compute:** Ubuntu 22.04 Droplet
- **Runtime:** OpenJDK 17
- **Build Tool:** Gradle
- **Application:** Spring Boot (embedded Tomcat, port 8080)
- **Access:** SSH key-based authentication
- **Security:** UFW firewall, non-root user, SSH hardening

---

## Project Structure

java-app/
├── src/main/java/com/example/
│   ├── App.java
│   └── HelloController.java
├── build/libs/
│   ├── java-app-1.0-SNAPSHOT.jar
│   └── java-app-1.0-SNAPSHOT-plain.jar
├── build.gradle
└── gradlew

---

## Step 1 
- Created Ubuntu 22.04 Droplet on DigitalOcean
- Selected region and size suitable for Java workloads
- Added SSH key during creation

![Droplet Creation](screenshots/01-droplet-created.png)

## Step 2
- Logged in using SSH as root for initial configuration

  - Root Login (Initial Access)
  - ssh root@209.38.238.74

![Secure SSH Access](screenshots/02-ssh-root-login.png)

## Step 3
- Created a dedicated application user
- Added user to sudoers group

  - adduser ali
  - usermod -aG sudo ali

![Created Non-Root User](screenshots/03-create-user-ali.png)

## Step 4 
- Copied SSH keys from root to non-root user
  
  - rsync --archive --chown=ali:ali ~/.ssh /home/ali

📸 screenshots/04-copy-ssh-keys.png
![Copy SSH Keys to Non-Root User](screenshots/04-copy-ssh-keys.png)

## Step 5
- Edited SSH configuration to improve security
- Disabled root login
- Enforced key-based authentication

  - sudo nano /etc/ssh/sshd_config
  - sudo systemctl restart ssh

![SSH Hardening](screenshots/05-ssh-hardened.png)

## Step 6
- Allowed SSH traffic
- Enabled firewall protection

  - sudo ufw allow OpenSSH
  - sudo ufw enable
  - sudo ufw status  

![Firewall Configuration (UFW)](screenshots/06-ufw-enabled.png)

## Step 7
- Rebooted server (droplet)
- Verified SSH login using non-root user

  - sudo reboot
  - ssh ali@<209.38.238.74>

![Reboot and Access Verification](screenshots/07-post-reboot-login.png)

---




