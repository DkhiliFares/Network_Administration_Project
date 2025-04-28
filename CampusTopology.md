# *Campus Topology With Emulated Virtual Environnement - Next Generation (EVE-NG)*

![image](https://github.com/user-attachments/assets/e507686f-dfe9-4406-8e11-c0c14bca77cc)


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
| Ubuntu VM   | 1        | ubuntu-SRV   |

To add:
1. Right-click > **Add an object > Node**
2. Select:
   - **Switch** (e.g., IOL L2 for `sw1`, `sw2`, etc.)
   - **Router** (e.g., IOL L3 or IOSv for `R1`)
   - **Ubuntu (cloud or desktop)**

---

### 🟩 1. Core Network (Green)

#### 1.1. Server (Ubuntu_SRV)
- Interface `e0`
- IP Address: `192.168.10.2/24`
- Gateway: `192.168.10.1`

(You can configure it with static IP in `/etc/netplan/` if Ubuntu Server.)

#### 1.2. Core Switch (SW1)

This switch interconnects Core and Departments.  
**Configuration:**
- Set **VLANs** and **Trunks**:

```bash
# Create VLANs
vlan 10
name Core_SRV
vlan 11
name Core_Router
vlan 12
name Dep-Math
vlan 13
name Dep-Info

# Configure Interfaces
interface e0/1
 switchport mode access
 switchport access vlan 10

interface e0/0
 switchport mode access
 switchport access vlan 11

interface e0/2
 switchport mode trunk

interface e0/3
 switchport mode trunk
```

---
  
### 🟦 2. Dep-Math Network (Blue)

#### 2.1. Router (R4)

**Configuration:**
```bash
# Interface towards Core
interface e0/0
 ip address 192.168.12.2 255.255.255.0
 no shutdown

# Interface towards Switches (trunk)
interface e0/1
 no shutdown
interface e0/2
 no shutdown

# Enable VLAN interfaces (SVIs or Sub-Interfaces if Router-on-a-Stick)
interface e0/1.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface e0/1.21
 encapsulation dot1Q 21
 ip address 192.168.21.1 255.255.255.0
```

*Router-on-a-Stick* style.

---

#### 2.2. Switches (SW21 and SW22)

**Configuration:**

On both switches:
```bash
# Create VLANs
vlan 20
name Math_Dept1
vlan 21
name Math_Dept2

# Set Access Ports
interface e0/0
 switchport mode access
 switchport access vlan 20 or vlan 21
```
(Specify depending on client connection.)

**Link to R4**:
```bash
interface e0/0
 switchport mode trunk
```

---

### 🟥 3. Dep-Info Network (Red)

#### 3.1. Switch SW3

Connected to Core and two access switches.

**Configuration:**

```bash
# Create VLANs
vlan 30
name Info_Dept1
vlan 31
name Info_Dept2

# Trunk uplinks to SW31 and SW32
interface e0/1
 switchport mode trunk

interface e0/2
 switchport mode trunk

# Trunk to Core (SW1)
interface e0/0
 switchport mode trunk
```

---

#### 3.2. Switches SW31 and SW32

Both switches:

```bash
# VLANs
vlan 30
name Info_Dept1
vlan 31
name Info_Dept2

# Access Ports
interface e0/0
 switchport mode access
 switchport access vlan 30 or 31
```
(Choose VLAN based on connected clients.)

**Link to SW3:**
```bash
interface e0/0
 switchport mode trunk
```

---

### ⬜ 4. Router (R1 - Core Router)

Handles the Core subnet (192.168.11.0/24).

**Configuration:**
```bash
interface e0/0
 ip address 192.168.11.1 255.255.255.0
 no shutdown

# Static Routes to Departments
ip route 192.168.20.0 255.255.255.0 192.168.12.2
ip route 192.168.21.0 255.255.255.0 192.168.12.2
ip route 192.168.30.0 255.255.255.0 192.168.13.2
ip route 192.168.31.0 255.255.255.0 192.168.13.2
```

(Assuming R4 has IP 192.168.12.2 and SW3 has an IP 192.168.13.2 on VLAN interface.)

---

### 🌟 Summary of VLANs and Subnets

| VLAN | Subnet            | Department         |
|:----:|:-----------------:|:------------------:|
| 10   | 192.168.10.0/24    | Core - Server      |
| 11   | 192.168.11.0/24    | Core - Router      |
| 12   | 192.168.12.0/24    | Dep-Math connection |
| 13   | 192.168.13.0/24    | Dep-Info connection |
| 20   | 192.168.20.0/24    | Dep-Math Vlan20    |
| 21   | 192.168.21.0/24    | Dep-Math Vlan21    |
| 30   | 192.168.30.0/24    | Dep-Info Vlan30    |
| 31   | 192.168.31.0/24    | Dep-Info Vlan31    |

---


