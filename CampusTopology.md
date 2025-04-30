# *Campus Topology With Emulated Virtual Environnement - Next Generation (EVE-NG)*

![image](https://github.com/user-attachments/assets/39922c8a-d7db-4327-b72c-f30ece5fc3d2)



---

## 🧰 **Prerequisites**
Before starting in **EVE-NG**, ensure you have:
- Switch images (e.g., IOU or IOL L2 images)
- Router image (e.g., Cisco IOSv)
- A Linux VM image (e.g., Ubuntu cloud or desktop)
- EVE-NG is installed and running

---

## 🏗️ **Step 1: Create a New Lab**
1. Log into your EVE-NG Web UI.
2. Click on **"Add New Lab"**.
3. Name it (e.g., *VLAN Core Topology*) and click **Save**.
4. Open the lab by clicking the pencil icon.

---

## 🧱 **Step 2: Add Nodes**

You need to add the following devices:

| Device Type | Quantity | Example Name |
|-------------|----------|--------------|
| L2 Switch   | 6        | sw1, sw2, sw3, sw21, sw22, sw31, sw32 |
| Router      | 1        | R1           |
| Ubuntu server   | 1        | ubuntu-SRV   |
| Ubuntu Desktop  | 1        |Linux   |

To add:
1. Right-click > **Add an object > Node**
2. Select:
   - **Switch** (e.g., IOL L2 for `sw1`, `sw2`, etc.)
   - **Router** (e.g., IOL L3 or IOSv for `R1`)
   - **Ubuntu (cloud or desktop)**

---



## 🗺️ Network Overview

| **Network Name**     | **Subnet**          | **Purpose**                     |
|----------------------|---------------------|----------------------------------|
| Core Server Network  | 192.168.10.0/24     | Ubuntu Server                    |
| Core Router Segment  | 192.168.11.0/24     | Router R1 ↔ Core Switch (SW1)    |
| Math Transit         | 192.168.12.0/24     | Core ↔ Math Main Switch (SW2)    |
| Info Transit         | 192.168.13.0/24     | Core ↔ Info Main Switch (SW3)    |
| VLAN 20 (Math)       | 192.168.20.0/24     | Math Department                  |
| VLAN 21 (Math)       | 192.168.21.0/24     | Math Department                  |
| VLAN 30 (Info)       | 192.168.30.0/24     | Info Department                  |
| VLAN 31 (Info)       | 192.168.31.0/24     | Info Department                  |

---

## 🧩 Device Configuration Summary

| **Device**     | **Interfaces**                         | **IP Address/Subnet**        | **Role**                    |
|----------------|----------------------------------------|-------------------------------|-----------------------------|
| Ubuntu_SRV     | e0                                     | 192.168.10.2/24               | Server                      |
| Linux PC       | e0                                     | 192.168.11.10/24              | Test PC                     |
| R1             | e0/0, e0/1                             | e0/0: 192.168.11.1/24         | Gateway & Static Routing    |
| SW1 (Core)     | e0/0 – e0/3                            | VLAN10: .10.1, VLAN11: .11.2… | L3 Switch – Core            |
| SW2 (Math)     | e0/0 – e0/2                            | VLAN20: .20.1, VLAN21: .21.1  | L3 Switch – Math Dept       |
| SW21 (Math A)  | e0/0                                   | Access VLAN 20                | Access Switch               |
| SW22 (Math B)  | e0/0                                   | Access VLAN 21                | Access Switch               |
| SW3 (Info)     | e0/0 – e0/2                            | VLAN30: .30.1, VLAN31: .31.1  | L3 Switch – Info Dept       |
| SW31 (Info A)  | e0/0                                   | Access VLAN 30                | Access Switch               |
| SW32 (Info B)  | e0/0                                   | Access VLAN 31                | Access Switch               |

---

## 🖥️ Host Configuration (Linux)

### ✅ Ubuntu_SRV

```bash
ip addr add 192.168.10.2/24 dev eth0
ip route add default via 192.168.10.1
```

### ✅ Linux PC

```bash
ip addr add 192.168.11.10/24 dev eth0
ip route add default via 192.168.11.1
```

---

## 🔧 CLI Configuration for Each Network Device

### ⚙️ Router – **R1**
```bash
enable
conf t

interface e0/0
ip address 192.168.11.1 255.255.255.0
no shutdown

interface e0/1
ip address 192.168.11.254 255.255.255.0
no shutdown

! Static routes to internal VLANs
ip route 192.168.10.0 255.255.255.0 192.168.11.2
ip route 192.168.12.0 255.255.255.0 192.168.11.2
ip route 192.168.13.0 255.255.255.0 192.168.11.2
ip route 192.168.20.0 255.255.255.0 192.168.11.2
ip route 192.168.21.0 255.255.255.0 192.168.11.2
ip route 192.168.30.0 255.255.255.0 192.168.11.2
ip route 192.168.31.0 255.255.255.0 192.168.11.2

end
wr
```

---

### ⚙️ Core L3 Switch – **SW1**

```bash
enable
conf t

vlan 10
vlan 11
vlan 12
vlan 13

interface vlan 10
ip address 192.168.10.1 255.255.255.0
no shutdown

interface vlan 11
ip address 192.168.11.2 255.255.255.0
no shutdown

interface vlan 12
ip address 192.168.12.1 255.255.255.0
no shutdown

interface vlan 13
ip address 192.168.13.1 255.255.255.0
no shutdown

interface e0/0
switchport mode access
switchport access vlan 11

interface e0/1
switchport mode access
switchport access vlan 10

interface e0/2
switchport mode trunk
switchport trunk allowed vlan 12,20,21

interface e0/3
switchport mode trunk
switchport trunk allowed vlan 13,30,31

ip routing
end
wr
```

---

### ⚙️ Math L3 Switch – **SW2**

```bash
enable
conf t

vlan 20
vlan 21

interface vlan 20
ip address 192.168.20.1 255.255.255.0
no shutdown

interface vlan 21
ip address 192.168.21.1 255.255.255.0
no shutdown

interface e0/0
switchport mode trunk
switchport trunk allowed vlan 12,20,21

interface e0/1
switchport mode access
switchport access vlan 20

interface e0/2
switchport mode access
switchport access vlan 21

ip routing
end
wr
```

---

### ⚙️ Info L3 Switch – **SW3**

```bash
enable
conf t

vlan 30
vlan 31

interface vlan 30
ip address 192.168.30.1 255.255.255.0
no shutdown

interface vlan 31
ip address 192.168.31.1 255.255.255.0
no shutdown

interface e0/0
switchport mode trunk
switchport trunk allowed vlan 13,30,31

interface e0/1
switchport mode access
switchport access vlan 30

interface e0/2
switchport mode access
switchport access vlan 31

ip routing
end
wr
```

---

### ⚙️ Access Switches (SW21, SW22, SW31, SW32)

#### Example – **SW21 (VLAN 20)**

```bash
enable
conf t
vlan 20
interface e0/0
switchport mode access
switchport access vlan 20
end
wr
```

> Do the same for:
- SW22 (use VLAN 21)
- SW31 (use VLAN 30)
- SW32 (use VLAN 31)

---

## 🧪 Verification Commands

### On Layer 3 Switches / Routers:

```bash
show ip interface brief
show vlan brief
show ip route
ping 192.168.10.2
ping 192.168.20.10
```

### On Linux Hosts:

```bash
ip addr
ping 192.168.11.1
ping 192.168.10.2
```

---

## 🧾 Notes

- All trunk links must **explicitly allow proper VLANs**.
- Each department switch must have **SVIs configured** for inter-VLAN routing.
- Core switch (SW1) acts as the backbone for routing between departments.

---
