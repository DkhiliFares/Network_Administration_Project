# 🌐 Network Supervision with Zabbix – EVE-NG Project

This project demonstrates how to deploy and configure **Zabbix** for supervising a segmented network topology with multiple VLANs and Cisco switches, simulated in **EVE-NG**.

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

