
# 📚 Install Cisco IOL on EVE-NG (using WinSCP)

---

## 🛠 Requirements

- An **EVE-NG** virtual machine (VMware/VirtualBox/Proxmox...)
- Access to EVE-NG via **SSH** (WinSCP, PuTTY)
- Cisco IOL images (`*.bin`) that you legally own
- Basic Linux skills

---

## 🧩 Steps
---

| Step | Action                                     |
|:----:|:------------------------------------------:|
| 1    | Connect to EVE-NG with WinSCP               |
| 2    | Upload IOL images to `/opt/unetlab/addons/iol/bin/` |
| 3    | Fix permissions with `unl_wrapper -a fixpermissions` |
| 4    | Create or edit `iourc` license file         |
| 5    | Refresh EVE-NG Web GUI and use IOL nodes    |


---

### 1. Prepare EVE-NG VM

First, ensure EVE-NG is running and you know its IP address.

On EVE-NG Console (or SSH):

```bash
# Update system (optional but recommended)
apt update && apt upgrade -y
```

Make sure SSH is **enabled** (default it is).

---

### 2. Connect to EVE-NG using WinSCP

- Open **WinSCP** on your Windows
- Protocol: **SFTP**
- Hostname: (EVE-NG IP address)
- Username: **root**
- Password: **eve** (default)
- Port: **22**
- Click **Login**

✅ Now you’re inside EVE-NG filesystem.

---

### 3. Upload Cisco IOL Files to the Correct Folder

- Go to this path on the EVE-NG side:  
  `/opt/unetlab/addons/iol/bin/`

(If the folder doesn't exist, **create it** manually.)

> In WinSCP: right-click > New Directory ➔ `/opt/unetlab/addons/iol/bin/`

---

- Then **drag and drop** your IOL images (`*.bin`) into this folder.

Example images:
- `i86bi-linux-l2-adventerprisek9-15.1g.bin` (Switch)
- `i86bi-linux-l3-adventerprisek9-15.1g.bin` (Router)

---

### 4. Fix Permissions

VERY IMPORTANT! After uploading, permissions must be corrected.

In your EVE-NG console (via SSH or using WinSCP terminal):

```bash
# Fix ownership and permissions
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```

✅ This command automatically sets the right owner (`root:root`) and permissions (`755`).

---

### 5. Add IOL License

You need a **license** for Cisco IOL to work.

On EVE-NG:

- Edit (or create) this file:  
  `/opt/unetlab/addons/iol/bin/iourc`

Example content:
```
[license]
gns3vm = abcdefghijklmnopqrstuvwxyz1234567890abcdef
```

Or use the classic line:

```bash
echo -e '[license]\n eve-ng = abcdefghijklmnopqrstuvwxyz1234567890abcdef' > /opt/unetlab/addons/iol/bin/iourc
```



---

### 6. Refresh EVE-NG Web GUI

- Go to your EVE-NG Web interface (browser).
- Create a **new lab**.
- Add a **new node** ➔ Now you will see Cisco **IOL Router** and **IOL Switch** available!
  
✅ Drag and drop to your topology.

---
