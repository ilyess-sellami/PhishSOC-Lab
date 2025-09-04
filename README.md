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
