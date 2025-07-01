# 🛡️ SOC Task 2 – Security Alert Monitoring & Incident Response (Future Interns)

## 👨‍💻 Submitted by: Ayushman  
## 📅 Date: July 1, 2025

---

## 📌 Objective

Simulate real-world SOC operations by setting up a SIEM tool (ELK Stack), ingesting authentication logs, identifying suspicious events (e.g., brute-force attempts), classifying alerts, and generating an incident response report.

---

## 🧰 Tools & Technologies Used

| Tool/Tech        | Purpose                                 |
|------------------|------------------------------------------|
| Ubuntu 24.04     | Base OS for deployment                   |
| OpenJDK 11       | Java dependency for Elasticsearch        |
| Elasticsearch 7.17.29 | Log storage & indexing backend        |
| Kibana 7.17.29   | Log visualization & dashboarding         |
| Logstash 7.17.29 | Log parsing and ingestion engine         |

---

## 💻 System Requirements

- RAM: 2–4 GB minimum
- OS: Ubuntu 20.04 or later (used Ubuntu 24.04)
- Disk: 10+ GB free
- Internet access

---

## 🏗️ Step-by-Step Setup

### 1️⃣ Install Java (OpenJDK 11)

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install openjdk-11-jdk -y
java -version
