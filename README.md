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


