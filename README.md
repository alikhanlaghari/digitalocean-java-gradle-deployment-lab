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

## Phase 1 — Droplet Creation

- Created Ubuntu 22.04 Droplet on DigitalOcean
- Selected region and size suitable for Java workloads
- Added SSH key during creation

📸 screenshots/01-droplet-created.png

---

## Phase 2 — Secure SSH Access

-  Root Login (Initial Access)
-  ssh root@209.38.238.74

📸 screenshots/02-ssh-root-login.png


## Create Non-Root User
-  adduser ali
-  usermod -aG sudo ali

📸 screenshots/03-create-user-ali.png


## Copy SSH Keys to Non-Root User
-  rsync --archive --chown=ali:ali ~/.ssh /home/ali

📸 screenshots/04-copy-ssh-keys.png

## SSH Hardening

### Edited SSH configuration:
-  sudo nano /etc/ssh/sshd_config

### Applied:

- Disabled root login
- Enforced key-based authentication
-  **sudo systemctl restart ssh**

📸 screenshots/05-ssh-hardened.png



