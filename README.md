# Java Application Deployment Lab on DigitalOcean

## Overview

This lab demonstrates how to securely deploy, operate, and validate a Java Spring Boot application on a cloud server using **DigitalOcean**, following real-world **Application Manager / DevOps** practices.

The focus is not just “running an app”, but:
- Hardening the server
- Enforcing secure access (SSH keys, non-root user)
- Packaging a Java application with **Gradle**
- Deploying and validating the application in production-like conditions

---

## Architecture Summary

- **Client:** Windows workstation (Git Bash + IntelliJ IDEA)
- **Cloud Provider:** DigitalOcean
- **Server:** Ubuntu 22.04 Droplet
- **Runtime:** OpenJDK 17
- **Build Tool:** Gradle (Gradle Wrapper)
- **Application:** Spring Boot (embedded Tomcat, port **8080**)
- **Access:** SSH key-based authentication
- **Security:** UFW firewall, non-root user, SSH hardening

---

## Project Structure

In this lab, the Java application follows a standard Gradle project layout:

java-app/ → contains application source code under `src/main/java/com/example/` (App.java + HelloController.java), and build outputs under `build/libs/` (executable JAR and `-plain` JAR). The build is driven by `build.gradle`, with `gradlew` ensuring consistent builds across environments.


---
## Deployment Phases


- **Phase 1–7:** Server hardening (SSH + firewall + non-root user)
- **Phase 8–12:** Application build & transfer
- **Phase 13–16:** Run, validate, and confirm access

---

## Phase 1 — Droplet Creation
- Created an Ubuntu 22.04 Droplet on DigitalOcean
- Selected region + size appropriate for a small Java workload
- Added SSH key during creation

![Droplet Creation](screenshots/01-droplet-created.png)

## Phase 2 — Initial Root SSH Login
- Initial login as root (first-time setup only):

  - Root Login (Initial Access)
  - ssh root@209.38.238.74

![Secure SSH Access](screenshots/02-ssh-root-login.png)

## Phase 3 — Create Non-Root User
- Create a dedicated user and grant sudo:

  - adduser ali
  - usermod -aG sudo ali

![Created Non-Root User](screenshots/03-create-user-ali.png)

## Phase 4 — Copy SSH Keys to Non-Root User
- Copy authorized SSH keys from root to the new user:
  
  - rsync --archive --chown=ali:ali ~/.ssh /home/ali

![Copy SSH Keys to Non-Root User](screenshots/04-copy-ssh-keys.png)

## Phase 5 — SSH Hardening
- Edit SSH config and enforce secure settings:

  - sudo nano /etc/ssh/sshd_config
  - sudo systemctl restart ssh

- Hardening goals:
  -  Disable root login
  -  Enforce SSH key authentication

![SSH Hardening](screenshots/05-ssh-hardened.png)

## Phase 6 — Firewall Configuration (UFW)
- Allowed SSH traffic
- Enabled firewall protection

  - sudo ufw allow OpenSSH
  - sudo ufw enable
  - sudo ufw status  

![Firewall Configuration (UFW)](screenshots/06-ufw-enabled.png)

## Phase 7 — Reboot and Access Verification
- Rebooted server (droplet)
- Verified SSH login using non-root user

  - sudo reboot
  - ssh ali@209.38.238.74

![Reboot and Access Verification](screenshots/07-post-reboot-login.png)

---

## Phase 8 — Grant Sudo Access Validation
- Verified sudo access for non-root user

  - sudo whoami

![Reboot and Access Verification 1](screenshots/08-ali-added-to-sudo.png)
![Reboot and Access Verification 2](screenshots/09-sudo-working.png)

## Phase 9 — Build Java Application Locally
- Built Spring Boot application using Gradle Wrapper

  - ./gradlew clean build

- **Artifacts expected:**
  
  -  build/libs/java-app-1.0-SNAPSHOT.jar
  -  build/libs/java-app-1.0-SNAPSHOT-plain.jar

![Build Java Application Locally](screenshots/10-gradle-build-success.png)

## Phase 10 — Install Java on Droplet
- Installed OpenJDK 17
- Verified Java runtime

  - sudo apt update
  - sudo apt install -y openjdk-17-jdk
  - java -version 

![Install Java on Droplet](screenshots/11-java-installed-on-droplet.png)

## Phase 11 — Create Application Directory
- Created a dedicated directory for application deployment

  - mkdir -p ~/apps/java-app

![Create Application Directory](screenshots/12-fix-wrong-directory.png)

## Phase 12 — Transfer Application Artifact
- Copied built JAR file to the Droplet

  - scp build/libs/java-app-1.0-SNAPSHOT.jar ali@<droplet-ip>:~/apps/java-app/

![Transfer Application Artifact](screenshots/13-jar-copied-to-droplet.png)

## Phase 13 — Verify Artifact on Server (Droplet)
- Confirmed JAR file presence and permissions

  - ls -la ~/apps/java-app

![Verify Artifact on Server](screenshots/14-jar-visible-on-server(droplet).png)

## Phase 14 — Start Application
- Start the Spring Boot app in background

  - cd ~/apps/java-app
  - nohup java -jar java-app-1.0-SNAPSHOT.jar > app.log 2>&1 &

![Start Applicationy](screenshots/15-app-started.png)

## Phase 15 — Port and Process Validation
- Copied built JAR file to the Droplet

  - ss -lntp | grep 8080

![Port Check](screenshots/16-port-check.png)
![UFW Allow 8080](screenshots/17-ufw-allow-8080.png)
![Application Running (nohup)](screenshots/18-app-running-nohup.png)

## Phase 16 — Application Access & Validation
- Verified application endpoint (Local on droplet)
  - curl -i http://localhost:8080/
  - curl -i http://localhost:8080/health
    
- Validated via browser:
  - http://209.38.238.74:8080/
  - http://209.38.238.74:8080/health

![Browser Home Endpoint](screenshots/23-browser-home.png)
![Browser Health Endpoint](screenshots/24-browser-health.png)

---

## Phase 17 — Application Shutdown
- Stopped the application

  - ps -ef | grep java
  - kill <pid>

![Application Shutdown](screenshots/25-app-stopped.png)

## Phase 18 — Droplet Cleanup
- Verified application endpoints via browser

  - /
  - /health

![Phase 16 — Droplet Cleanup](screenshots/26-droplet-powered-off.png)



---

## Operational Learning Outcomes (Public Sector & Enterprise)

- **Stable and predictable operations:**  
  Demonstrated the ability to provision, deploy, and operate a cloud-based application in a controlled and repeatable manner, aligned with public-sector expectations for system stability and reliability.

- **Security and access control:**  
  Applied the principle of least privilege through non-root user access, SSH key-based authentication, and firewall configuration, reflecting common security requirements in municipal and enterprise environments.

- **Operational readiness and verification:**  
  Verified system behavior using process inspection, port monitoring, log review, and HTTP endpoint testing to ensure services function correctly before and after deployment.

- **Clear separation of responsibilities:**  
  Maintained a structured separation between development, deployment, and runtime operations, supporting maintainability, incident handling, and operational handover.

- **Documentation and traceability:**  
  Produced step-by-step documentation with commands and screenshots, suitable for audits, onboarding, and long-term system ownership.

- **Alignment with enterprise IT practices:**  
  The lab reflects workflows commonly used in municipalities and larger organizations, where controlled change, validation, and documentation are essential.

---

## Outcome Summary

- Secure Ubuntu server provisioned and hardened  
- Java application built and packaged in a reproducible manner using Gradle  
- Application deployed and operated as a non-root user  
- Network access restricted and explicitly controlled via firewall rules  
- Application functionality validated through multiple verification steps  
- System shut down cleanly to ensure cost control and responsible resource usage  

---



