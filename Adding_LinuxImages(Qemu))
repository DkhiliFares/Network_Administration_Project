
# 📚 Install Linux Images into EVE-NG (QEMU images)

---

## 🛠 Requirements

- **EVE-NG** virtual machine (working)
- **Linux QEMU image** files (from your Mega link — usually `.qcow2` or `.img`)
- **WinSCP** installed
- **SSH access** to EVE-NG (`root/eve`)

---

## 🧩 Steps

---

| Step | Action                                  |
|:----:|:---------------------------------------:|
| 1    | Download Linux QEMU images              |
| 2    | Connect to EVE-NG with WinSCP            |
| 3    | Create a folder under `/opt/unetlab/addons/qemu/` |
| 4    | Upload qcow2 image and rename to `hda.qcow2` |
| 5    | Fix permissions with `unl_wrapper -a fixpermissions` |
| 6    | Refresh EVE-NG Web and use Linux nodes   |

---

### 1. Download Linux QEMU Images

- From your link (`https://mega.nz/folder/30p3TKob#42_S__9wwPVO0zHIfC4xow`), download the Linux images you want.
- Example files:  
  `ubuntu-20.04.qcow2`, `kali-linux-2022.qcow2`, etc.

✅ Save them on your Windows PC first.

---

### 2. Connect to EVE-NG Using WinSCP

- Open **WinSCP**
- Host: (EVE-NG IP address)
- Username: **root**
- Password: **eve**
- Port: **22**
- Protocol: **SFTP**

✅ Click **Login**.

---

### 3. Create Folder for Linux Image

Now, inside EVE-NG, go to:

```bash
/opt/unetlab/addons/qemu/
```

Each QEMU image **must be in its own folder**.

The folder naming rule:  
**[image-name]-[version]**

Example:

| Linux OS | Folder name |
|:--------:|:-----------:|
| Ubuntu 20.04 | `ubuntu-20.04` |
| Kali 2022 | `kali-2022` |

---

In WinSCP:
- **Right click** ➔ **New Directory**
- Example folder:  
  `/opt/unetlab/addons/qemu/ubuntu-20.04`

---

### 4. Upload Linux Image

Inside the newly created folder (`ubuntu-20.04`), upload the **qcow2** file you downloaded.

✅ **VERY IMPORTANT:**  
After uploading, **rename** the file exactly to:

```
hda.qcow2
```

**Example:**

```
/opt/unetlab/addons/qemu/ubuntu-20.04/hda.qcow2
```

✅ EVE-NG **requires** this name format.

---

### 5. Fix Permissions

After upload, on the EVE-NG terminal (SSH/Console), run:

```bash
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```

✅ This command is mandatory to make sure the EVE-NG platform recognizes the new Linux image.

---

### 6. Refresh Web GUI

- Open your EVE-NG web browser
- Create a new Lab
- Add New Node
- Now you will see **ubuntu-20.04** (or whatever Linux you uploaded) available!

✅ Drag and drop Linux machines like routers!

---




