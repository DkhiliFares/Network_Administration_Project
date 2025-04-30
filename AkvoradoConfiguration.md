# 🛰️ Akvorado Network Flow Monitoring Setup in EVE-NG

## 📌 Overview

This guide details how to **install and configure Akvorado** in an EVE-NG virtual lab for **network flow monitoring**. Akvorado is an open-source tool to collect, store, and visualize **NetFlow/IPFIX/sFlow** data.

It provides real-time insights into:
- Bandwidth usage
- Top talkers
- Protocol breakdowns
- Communication between departments

---

## 🧠 What is Akvorado?

**Akvorado** is a NetFlow/IPFIX/sFlow collector with a web UI. It uses **ClickHouse** to store flow data and allows detailed traffic analysis.

📎 Learn more: [https://github.com/akvorado/akvorado](https://github.com/akvorado/akvorado)

---

## 🖥️ Topology Context

Akvorado is installed on the `Ubuntu_SRV` node in the Core (192.168.10.0/24). Cisco switches in departments (Dep-Math, Dep-Info) are configured to export **NetFlow** or **sFlow** to it.

![Capture d'écran 2025-04-30 190320](https://github.com/user-attachments/assets/9eabecfb-81ad-47c3-ba92-d328ffb3e9a7)


---

## ⚙️ Deployment Steps

### 1. Prerequisites

- EVE-NG installed with a running lab
- A VM (e.g., Ubuntu_SRV) with internet access
- Docker installed

### 2. Install Docker and Docker Compose

```bash
sudo apt update && sudo apt install -y docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker
```

### 3. Create Akvorado Docker Setup

```bash
mkdir ~/akvorado && cd ~/akvorado
nano docker-compose.yml
```

Paste the following:

```yaml
version: "3.5"

services:
  akvorado:
    image: akvorado/akvorado:latest
    container_name: akvorado
    ports:
      - "8080:8080"       # Web UI
      - "2055:2055/udp"   # NetFlow/IPFIX
      - "6343:6343/udp"   # sFlow
    volumes:
      - akvorado-data:/var/lib/akvorado
    restart: always

volumes:
  akvorado-data:
```

Start Akvorado:

```bash
docker-compose up -d
```

---

## 🌐 Access the Web UI

From your browser on the Linux Desktop VM or host:

```
http://192.168.10.X:8080
```

Replace `X` with the IP of your `Ubuntu_SRV`.

---

## 🧑‍🔧 Cisco NetFlow Configuration Example

On switches (e.g., SW1, SW2, SW3), run:

```bash
conf t
ip flow-export destination 192.168.10.X 2055
ip flow-export version 9
ip flow-export source <interface>
ip flow-cache timeout active 1
ip flow-cache timeout inactive 15

interface <interface>
 ip flow ingress
 ip flow egress
end
wr
```

🔁 Replace `<interface>` with the monitored interface.

### OR (for sFlow):

```bash
conf t
sflow 1 destination 192.168.10.X
sflow 1 source <interface>
sflow 1 polling-interval 30

interface <interface>
 sflow 1 enable
end
wr
```

---

## 📊 What You’ll See in Akvorado

- Real-time traffic flow summaries
- Top talkers by IP, port, protocol
- Inter-department traffic analysis (Math ↔ Info)
- Flow duration and volume

---

## 🛡️ Optional: Secure Access with HTTPS

```bash
sudo apt install nginx certbot python3-certbot-nginx -y
sudo certbot --nginx
```

---

## 📦 Components Summary

| Node        | Role                         |
|-------------|------------------------------|
| Ubuntu_SRV  | Akvorado + Zabbix Host       |
| Cisco SWs   | NetFlow/sFlow Exporters      |
| Linux VM    | Web UI Access (Monitoring)   |

---

## 📚 References

- Akvorado Docs: [https://github.com/akvorado/akvorado](https://github.com/akvorado/akvorado)
- NetFlow Cisco Config: [Cisco NetFlow Guide](https://www.cisco.com/en/US/products/ps6601/products_configuration_example09186a00800f3db9.shtml)

---
