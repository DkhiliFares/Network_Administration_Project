To recreate this network topology in **EVE-NG**, follow these detailed steps. This topology includes multiple VLANs across departments and a core network. Here's how to do it step-by-step:


![image](https://github.com/user-attachments/assets/d82c4025-4732-4bf7-b623-6f27b0e1936e)

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

## 🔌 **Step 3: Connect the Devices**

Match the connections shown in your diagram:

- `ubuntu-SRV` → `sw1` (e0/3)
- `sw1`:
  - e0/0 → `sw2`
  - e0/1 → `R1` (f0/0)
  - e0/2 → `sw3`
- `sw2`:
  - e1/0 → `sw21`
  - e1/1 → `sw22`
- `sw3`:
  - e1/0 → `sw31`
  - e1/1 → `sw32`

Use **right-click > Add Link** and choose interfaces accordingly.

---

## 🛠️ **Step 4: Configure VLANs on Access Switches**

You’ll configure VLANs on `sw21`, `sw22`, `sw31`, `sw32`:

### 📘 **Math Department VLANs on sw2 side:**
- VLAN 20 – `192.168.20.0/24`
- VLAN 21 – `192.168.21.0/24`

### 🟧 **Info Department VLANs on sw3 side:**
- VLAN 30 – `192.168.30.0/24`
- VLAN 31 – `192.168.31.0/24`

You can do this using IOU/IOL CLI:

```bash
conf t
vlan 20
name MATH_VLAN20
vlan 21
name MATH_VLAN21
exit
int e0
switchport mode access
switchport access vlan 20
exit
```
Repeat with the appropriate VLAN numbers and interfaces.

---

## 🧠 **Step 5: Configure Trunks**

Configure trunks between:
- `sw1` ↔ `sw2`
- `sw1` ↔ `sw3`

Example (on `sw1`):

```bash
conf t
int e0/0
switchport trunk encapsulation dot1q
switchport mode trunk
exit
int e0/2
switchport trunk encapsulation dot1q
switchport mode trunk
exit
```

Also configure `sw2`'s e0/0 and `sw3`'s e0/0 as trunks.

---

## 🌐 **Step 6: Configure the Core Router (`R1`)**

Configure subinterfaces for inter-VLAN routing:

```bash
conf t
int f0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0

int f0/0.21
encapsulation dot1Q 21
ip address 192.168.21.1 255.255.255.0

int f0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0

int f0/0.31
encapsulation dot1Q 31
ip address 192.168.31.1 255.255.255.0

int f0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0

int f0/0.11
encapsulation dot1Q 11
ip address 192.168.11.1 255.255.255.0

int f0/0.12
encapsulation dot1Q 12
ip address 192.168.12.1 255.255.255.0

int f0/0.13
encapsulation dot1Q 13
ip address 192.168.13.1 255.255.255.0
```

Make sure to enable routing with:
```bash
ip routing
```

---

## 💻 **Step 7: Configure Ubuntu Server**

In Ubuntu, set static IP:
```bash
sudo nano /etc/netplan/*.yaml
```

Example config:
```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.10.2/24
      gateway4: 192.168.10.1
      nameservers:
        addresses: [8.8.8.8]
```

Then:
```bash
sudo netplan apply
```

---

## ✅ **Step 8: Testing**

- Use `ping` from Ubuntu to different VLANs (e.g., `ping 192.168.20.1`).
- Test inter-VLAN communication.
- Use `show vlan`, `show ip route`, `show int trunk` to verify.

---
