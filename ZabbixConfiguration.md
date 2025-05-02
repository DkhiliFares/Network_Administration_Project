# 🌐 Network Supervision with Zabbix – EVE-NG Project

This project demonstrates how to deploy and configure **Zabbix** for supervising a segmented network topology with multiple VLANs and Cisco switches, simulated in **EVE-NG**.

---
## 📖 What is Zabbix?

**Zabbix** is an open-source platform for monitoring:
- Networks (routers, switches, firewalls)
- Servers (Linux, Windows)
- Applications and services
- Cloud and containers

It supports agents, SNMP, ICMP, IPMI, and more protocols for data collection.

---
## 🧱 Architecture réseau
![image](https://github.com/user-attachments/assets/ad979aef-2144-42f4-8c8b-6afe75b384a1)




## 🗺️ Network Topology Overview

### Subnets and VLANs

| Zone         | Subnets/VLANs                             |
|--------------|--------------------------------------------|
| Core         | 192.168.10.0/24, 192.168.11.0/24, 192.168.12.0/24, 192.168.13.0/24 |
| Dep-Math     | VLAN 20: 192.168.20.0/24<br>VLAN 21: 192.168.21.0/24 |
| Dep-Info     | VLAN 30: 192.168.30.0/24<br>VLAN 31: 192.168.31.0/24 |

### Devices (from the image)

| Device       | Role                    | Zone        | Notes                                |
|--------------|-------------------------|-------------|---------------------------------------|
| Ubuntu_SRV   | Zabbix Server           | Core        | Ubuntu Server, IP: 192.168.10.X       |
| R1           | Main Router             | Core        | Connects 192.168.11.0/24 subnet       |
| Linux (Z)    | Client/Web access       | Core        | Ubuntu Desktop to access UI          |
| SW1          | Core Switch             | Core        | Connected to all main subnets        |
| SW2          | Distribution Switch     | Dep-Math    | Interfaces with SW21, SW22           |
| SW21, SW22   | Access Switches         | Dep-Math    | Devices connected to VLAN 20/21      |
| SW3          | Distribution Switch     | Dep-Info    | Interfaces with SW31, SW32           |
| SW31, SW32   | Access Switches         | Dep-Info    | Devices connected to VLAN 30/31      |

---

## ⚙️ Step-by-step Zabbix Server Installation (on `Ubuntu_SRV`)

### 🟢 1. System Update
```bash
sudo apt update && sudo apt upgrade -y
```
### 🟢 2. Add Zabbix Repository
---
```bash
wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo apt update
```

### 🟢 3. Install Required Packages
```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf \
zabbix-sql-scripts zabbix-agent mysql-server -y
```

### 🟢 4. Configure MySQL Database
```bash
sudo mysql -uroot
```
In MySQL shell:
```sql
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'zabbixpass';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 🟢 5. Import the Database Schema
```bash
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -uzabbix -pzabbixpass zabbix
```

### 🟢 6. Configure Zabbix Server
Edit the configuration file:
```bash
sudo nano /etc/zabbix/zabbix_server.conf
```
Find and set:
```
DBPassword=zabbixpass
```

### 🟢 7. Start and Enable Services
```bash
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2
```

---

## 🌐 Accessing Zabbix Web Interface

- Open a browser on the `Linux` VM (Ubuntu Desktop).
- Navigate to: `http://192.168.10.X/zabbix`
- Complete the setup wizard:
  - DB User: `zabbix`
  - DB Password: `zabbixpass`
  - Default login: `Admin` / Password: `zabbix`

---

## 🛰️ Adding Network Devices for Monitoring

### 🔧 Cisco Switches (SW1, SW2, SW3, etc.)

#### Enable SNMP (on each switch):
```bash
conf t
snmp-server community public RO
end
wr
```

#### Add Device in Zabbix:
- Go to **Configuration → Hosts → Create host**
- Enter hostname (e.g., `SW2`)
- Add **SNMP Interface**, set IP address
- Link Template: `Template Net Cisco SNMPv2`

---

### 🖥️ Ubuntu Machines (e.g., Linux VM or others)

#### Install Zabbix Agent:
```bash
sudo apt install zabbix-agent -y
```

Edit the agent configuration:
```bash
sudo nano /etc/zabbix/zabbix_agentd.conf
```
Set the Zabbix server IP:
```
Server=192.168.10.X
```

Restart and enable the agent:
```bash
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent
```

#### Add in Zabbix UI:
- Interface Type: Agent
- Template: `Template OS Linux by Zabbix agent`

---

## 📈 Advanced Monitoring

- **Triggers**: Create alerts (e.g., high CPU usage, down interfaces).
- **Graphs**: Bandwidth usage, latency trends.
- **Notifications**: Configure emails or Telegram alerts.
- **Dashboards**: Customize real-time dashboards for your network.

---



---

## 🧩 Result Expectations

- Real-time visibility of your network's health.
- Alert system for hardware or software issues.
- Centralized management of devices via a single dashboard.

---

## 📎 Credits

- Simulation: [EVE-NG Community Edition](https://www.eve-ng.net/)
- Supervision: [Zabbix Official Site](https://www.zabbix.com/)

---
