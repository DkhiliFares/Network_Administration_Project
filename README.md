# 🧠 Network Monitoring & Supervision Lab using EVE-NG

## 📌 Project Overview

This project provides a **complete, step-by-step guide** to building a **virtual campus network lab** using **EVE-NG**, with integrated tools for:

- 🔍 **Network Performance Monitoring** using [Akvorado](https://github.com/akvorado/akvorado)
- 🛠️ **Application & Network Supervision** using [Zabbix](https://www.zabbix.com/)

It includes full setup instructions from scratch, including installing EVE-NG, adding Cisco and Linux images, designing the campus topology, and configuring the monitoring tools.

---

## 🧰 Requirements

- EVE-NG (installed on your host or VM)
- Linux ISO/QEMU images (e.g. Ubuntu)
- Cisco IOL images
- Internet connection for downloading tools like Akvorado & Zabbix

---

## 🗂️ Documentation Structure

You can follow each of the following guides in order to reproduce the full setup:

### 1️⃣ [EVE-NG Installation](./eve%20ng%20installation.md)

> Install and prepare the EVE-NG environment on your machine.

### 2️⃣ [Adding Cisco IOL Images](./adding%20cisco%20iol.md)

> Learn how to import and configure Cisco IOS (IOL) router and switch images for EVE-NG.

### 3️⃣ [Adding Linux Images (QEMU)](./adding%20linux%20images.md)

> Add Ubuntu Desktop and Server images for client and server simulation inside your virtual lab.

---

## 🏛️ Campus Topology Design

### 4️⃣ [Campus Topology](./campus%20topology.md)

> Build a real-world style multi-VLAN campus network composed of core, math, and info departments with inter-switch links and proper subnetting.

---

## 📈 Network Monitoring Tools Configuration

### 5️⃣ [Zabbix Configuration](./zabbixconfiguration.md)

> Deploy Zabbix on Ubuntu Server to monitor network devices, applications, services, and system performance.

### 6️⃣ [Akvorado Configuration](./akvoradoconfiguration.md)

> Collect and visualize NetFlow/sFlow traffic data using Akvorado to analyze flow patterns and bandwidth usage.

---

## 🎯 Project Objectives

- 🧑‍💻 Recreate a realistic multi-department network architecture
- 📡 Monitor network traffic flows (NetFlow/IPFIX/sFlow)
- ⚙️ Supervise applications and network devices with alerts and dashboards
- 💻 Visualize and analyze performance metrics through web UIs

---

## ✅ Outcome

At the end of this project, you’ll have:

- A working **campus network lab** in EVE-NG
- **Akvorado** showing live traffic stats from Cisco switches
- **Zabbix** monitoring servers and switches
- A clear understanding of using open-source tools for network operations

---

## 🛠️ Recommended Tools

- **EVE-NG Community/Pro**
- **Wireshark** (for verification and debugging)
- **VS Code / WinSCP / SSH clients** for editing configs

---

## 🧩 Contribution

Feel free to fork, improve, or reuse this project. If you found it useful, give it a ⭐!

---
