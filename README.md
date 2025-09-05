# 🛡️ PhishSOC-Lab – SOC Phishing Detection & Analysis Sandbox

**PhishSOC-Lab** is a hands-on cybersecurity project designed to help analysts and engineers **detect, analyze, and respond to phishing threats** in a simulated Security Operations Center (SOC) environment.

This lab demonstrates how to:

- Deploy a Wazuh SIEM (single-node) using Docker.

- Simulate a mail server (Postfix/Dovecot) to generate phishing emails.

- Collect and forward mail server logs using Wazuh Agent.

- Write and test custom Wazuh rules to detect phishing patterns (subjects, domains, attachments).
 
- Visualize and monitor alerts in SOC dashboards.

- Practice IOC extraction and enrichment (domains, hashes, URLs).

---

## 🎯 Objectives

- Provide a **sandbox environment** for phishing analysis.

- Teach analysts how to **configure and extend SIEM rules**.

- Show how phishing activity can be detected using **real log sources**.

- Enable SOC teams to practice **threat hunting and incident response** in a controlled setup.

---

## 🏗️ Components

- **Wazuh SIEM (Docker)** – log collection, detection, and alerting.

- **Postfix/Dovecot Mail Server (Docker)** – phishing email simulation.

- **Wazuh Agent** – forwards mail logs to Wazuh.

- **Custom Wazuh Rules** – detect phishing indicators.

- **SOC Monitoring Dashboard** – visualize phishing attempts and alerts.

---

## ⚙️ System Requirements
***Minimum Requirements***

- **OS**: Linux (Ubuntu 20.04+ recommended) / macOS / Windows with Docker Desktop

- **CPU**: 2 cores minimum (4+ recommended)

- **RAM**: 4 GB minimum (8 GB recommended for smooth performance)

- **Disk Space**: 20 GB free

**Software Requirements**

- Docker **>= 20.x**

- Docker Compose **>= 1.29.x**

- Git **>= 2.x** (to clone the repository)

**Network Requirements**

- Wazuh Dashboard: `https://localhost:5601`

- Wazuh API: `https://localhost:55000`

- Mail Server (Postfix/Dovecot): exposed ports (25, 143, 993)

- Ensure ports are not blocked by firewall

---

## 🏛️ Architecture
**🔄 Flow Explanation**
1. **Phishing Email Simulation**
   - A **Postfix/Dovecot mail server** generates and receives simulated phishing emails.
   - These emails may contain suspicious subjects, domains, or attachments.
2. **Log Collection**
   - The **Wazuh Agent** installed on the mail server forwards log files (Postfix, Dovecot, syslog) to the **Wazuh Manager**.
3. **Detection & Rules**
   - The **Wazuh Manager** applies **custom phishing detection rules**.
   - Suspicious emails are flagged based on IOC matches (domains, hashes, URLs, attachment names).
4. **Indexing & Storage**
   - Alerts and logs are processed and stored by the **Wazuh Indexer (OpenSearch)**.
5. **Visualization**
   - The **Wazuh Dashboard** provides SOC analysts with real-time visualization of phishing attempts, alerts, and threat hunting dashboards.

## 🚀 Deployment Guide

### 1. Wazuh Deployment & Configuration

**1.1 Clone the official Wazuh Docker repo:**

```bash
git clone https://github.com/wazuh/wazuh-docker.git
cd wazuh-docker/single-node
```

**1.2 Run the following command to generate the desired certificates:**

```bash
docker-compose -f generate-indexer-certs.yml run --rm generator
```

**1.3 Start Wazuh single-node containers:**

```bash
docker-compose up -d
```

- This deploys Wazuh Manager, Indexer, and Dashboard containers.
- Wait for all containers to be fully up (docker ps to verify).

**1.4 Verify the deployment:**

```bash
docker logs wazuh.manager
```

- Ensure no errors and that Wazuh Manager started successfully.

### 2. Mail Server Deployment & Testing

**2.1 Clone PhishSOC-Lab Project**

```bash 
git clone https://github.com/ilyess-sellami/PhishSOC-Lab.git
cd PhishSOC-Lab/mailserver
```

**2.2 Run Mail Server**

The project already contains a minimal `docker-compose.yml` for a test mail server.

Start it with:

```bash
docker compose up -d
```

This will run a simple Postfix + Dovecot mail server exposing:

- SMTP: localhost:25

- IMAP: localhost:143

**2.3 Add a Mailbox**

Create a test mailbox inside the container:

```bash
docker exec -it mailserver setup email add test@phishsoc.lab 'Test1234'
```

**2.4 Verify mailbox creation**

```bash
docker exec -it mailserver setup email list
```

You should see your new mailbox.

**2.5 Send a Test Phishing Email**

Use `swaks` to simulate a phishing message

Install swaks (on Ubuntu/Mac):

```bash
sudo apt-get install swaks   # Linux
brew install swaks           # Mac
```

Send a fake phishing email:

```bash
swaks --server localhost --from attacker@evil.com --to test@phishsoc.lab \
  --header "Subject: URGENT: Verify Your Bank Account Now" \
  --body "Dear user, please click this link to verify your bank account: http://malicious-site.com/login"
```

### 3. Wazuh Rules Configuration

**3.1 Copy your phishing rules into the running Wazuh Manager container**

```bash
docker cp local_rules.xml single-node-wazuh.manager-1:/var/ossec/etc/rules/local_rules.xml
```

- Replace single-node-wazuh.manager-1 with the actual container name from docker ps.

- You can use your example file from /config/local_rules.xml on your GitHub project.

**3.2 Restart the Wazuh Manager container**

```bash
docker restart single-node-wazuh.manager-1
```

**3.3 Verify Wazuh Manager logs**

```bash
docker logs -f single-node-wazuh.manager-1
```

- Check that there are no XML errors and that your phishing rules are loaded successfully.


### 4. Install Wazuh Agent on the Mail Server (Docker)

**4.1 Open Wazuh Dashboard**

- Go to Wazuh dashboard
- Navigate to Agents → Add agent

**4.2 Select the Agent**

- Choose **Linux DEB amd64**
- Server address: `<your-wazuh-ip-address>`
- Agent name: `mail-server-agent`
- Group: `default`

**4.3 Run the installation commands inside the mail server container**

Enter the container:

```bash
docker exec -it mailserver bash
```

(where `mailserver` is your container name).

Update packages inside the container:

```bash
apt-get update
```

Then run:

```bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.12.0-1_amd64.deb && \
WAZUH_MANAGER='<your-wazuh-ip-address>' WAZUH_AGENT_NAME='mail-server-agent' dpkg -i ./wazuh-agent_4.12.0-1_amd64.deb
```

**4.4 Start the agent**

```bash
systemctl daemon-reload
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

**4.5 Verify agent is running**

```bash
systemctl status wazuh-agent
tail -f /var/ossec/logs/ossec.log
```

**4.6 Check Dashboard**

- Go back to **Wazuh Dashboard → Agents list**.
- You should see `mail-server-agent` with status **Active ✅**.