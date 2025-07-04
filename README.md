# 🚨 FUTURE_CS_02 – SOC Monitoring & Incident Response

## 📄 Executive Summary

This project simulates a Security Operations Center (SOC) workflow as part of the Future Interns Cyber Security Task 2. It involves deploying a real-world SIEM (Security Information and Event Management) system using the ELK Stack, parsing authentication logs, detecting brute-force attacks, and preparing an incident response report.

---

## 🔧 Infrastructure Setup

- **OS Used**: Ubuntu 24.04  
- **SIEM Tool**: ELK Stack (Elasticsearch 7.17.29, Logstash, Kibana)  
- **Java**: OpenJDK 11  

### ✅ Step-by-Step ELK Setup

#### 1. Install Java
```bash
sudo apt update && sudo apt install openjdk-11-jdk -y
```
### Screenshots
![Image](https://github.com/user-attachments/assets/d69fd9a7-812f-4dd0-9b53-4c37ab8e3056)

#### 2. Add Elastic APT Repository

```bash
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | gpg --dearmor | sudo tee /usr/share/keyrings/elastic.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list

sudo apt update
```
### Screenshots
![image](https://github.com/user-attachments/assets/9df9a12a-2aa9-4541-b4cf-ff2280d05416)
![Image](https://github.com/user-attachments/assets/2fc55e47-1ca3-473c-8f01-f8a76181147d)

#### 3. Install ELK Stack Components

```bash
sudo apt install elasticsearch kibana logstash -y
```
### Screenshots
![Image](https://github.com/user-attachments/assets/127f3596-1418-4b69-b5e7-439e1c8e8416)

#### 4. Enable and Start ELK Services

```bash
sudo systemctl enable elasticsearch kibana logstash
sudo systemctl start elasticsearch kibana logstash
```
#### 5.Verify Elasticsearch is Running

```bash
curl -X GET "localhost:9200"
```
### Screenshots

![Image](https://github.com/user-attachments/assets/1f99b2f9-e641-4817-be67-892f5b64d26f)

## 📥 Log Ingestion via Logstash

### 🔹 Sample Log File (`/home/ayushman/sample.log`)

```text
Oct 11 22:14:15 localhost sshd[1999]: Failed password for root from 192.168.1.100 port 22 ssh2
Oct 11 22:14:17 localhost sshd[1999]: Accepted password for root from 192.168.1.100 port 22 ssh2
Oct 11 22:16:01 localhost sshd[1999]: Failed password for invalid user admin from 45.33.32.156 port 22 ssh2
Oct 11 22:17:05 localhost sshd[1999]: Failed password for user1 from 192.168.1.101 port 22 ssh2
Oct 11 22:18:08 localhost sshd[1999]: Accepted password for user1 from 192.168.1.101 port 22 ssh2
```
### 🔧 Logstash Config (`/etc/logstash/conf.d/sample-log.conf`)

```conf
input {
  file {
    path => "/home/ayushman/sample.log"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  grok {
    match => {
      "message" => "%{SYSLOGTIMESTAMP:timestamp} %{HOSTNAME:host} sshd\[%{NUMBER:pid}\]: %{WORD:action} password for (invalid user )?(%{USERNAME:user}) from %{IP:src_ip} port %{NUMBER:src_port} ssh2"
    }
  }
  date {
    match => ["timestamp", "MMM  d HH:mm:ss", "MMM dd HH:mm:ss"]
    target => "@timestamp"
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "auth-logs"
  }
  stdout { codec => rubydebug }
}
```
### Screenshots
![Image](https://github.com/user-attachments/assets/59668066-6669-4d7b-a6b1-7436af7ac58b)

![Image](https://github.com/user-attachments/assets/0cdc9871-da4e-4466-b452-4874de77cb70)

![Image](https://github.com/user-attachments/assets/bcae3f62-e116-45ef-b236-a5de6bd917e3)

## 🚨 Alert Classification

| Alert ID | Timestamp | User  | Source IP     | Action | Severity | Description                           |
| -------- | --------- | ----- | ------------- | ------ | -------- | ------------------------------------- |
| A1       | 22:14:15  | root  | 192.168.1.100 | Failed | High     | Brute-force attempt on root           |
| A2       | 22:16:01  | admin | 45.33.32.156  | Failed | High     | Invalid user login from public IP     |
| A3       | 22:17:05  | user1 | 192.168.1.101 | Failed | Medium   | Repeated failed login to user account |

## 🛡️ Incident Response Summary

- **Incident Type**: SSH brute-force attack attempt

### 🧨 Impact:
- Unauthorized access risk to `root` and `user1`
- External login attempts from suspicious public IP

### 🛠️ Recommendations:
- Block IP `45.33.32.156`
- Enforce strong password policies
- Enable 2FA for SSH accounts
- Implement SSH rate limiting or use `fail2ban`

***Note***:The Kibana dashboard was explored during the project. Due to timestamp differences in simulated logs, visualizations were not included but data indexing and parsing were verified via Discover and Logstash output.

