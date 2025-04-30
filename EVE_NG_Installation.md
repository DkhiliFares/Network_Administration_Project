# **EVE-NG Community Edition Installation Guide for VMware Workstation**  

This guide explains how to install **EVE-NG Community Edition** (free version) on **VMware Workstation Pro/Player** for network emulation and labbing.  

---

## **Prerequisites**  
Before installing EVE-NG, ensure you have:  
- **VMware Workstation Pro/Player 16+** (or ESXi if using a server)  
- **Minimum 8GB RAM** (16GB+ recommended for multiple devices)  
- **100GB+ free disk space** (SSD recommended for better performance)  
- **CPU with VT-x/AMD-V enabled** (required for nested virtualization)  

---

## **Step 1: Download EVE-NG OVA File**  
1. Go to the official EVE-NG website: [https://www.eve-ng.net](https://www.eve-ng.net)  
2. Navigate to **Download → EVE Community**  
3. Download the latest **OVA file** (e.g., `EVE-Community-5.0.1-20.ova`)  

---

## **Step 2: Import OVA into VMware Workstation**  
1. Open **VMware Workstation**  
2. Click **File → Open** and select the downloaded `.ova` file  
3. Choose a name for the VM (e.g., "EVE-NG-Lab")  
4. Select a storage location (ensure enough disk space)  
5. Click **Import** and wait for the process to complete  

---

## **Step 3: Configure VM Settings**  
After importing, adjust the VM settings for better performance:  
1. **Right-click the VM → Settings**  
2. **Memory**: Increase to **8GB+** (required for running multiple devices)  
3. **Processors**: Set to **2+ cores** (enable "Virtualize Intel VT-x/EPT")  
4. **Network Adapter**: Set to **Bridged** (recommended for external access)  
5. **Hard Disk**: Expand if needed (default is 100GB)  

---

## **Step 4: Power On EVE-NG VM**  
1. Start the VM  
2. Wait for the boot process (~1-2 minutes)  
3. The login screen will appear with default credentials:  
   - **Username**: `root`  
   - **Password**: `eve`  

---

## **Step 5: Access EVE-NG Web Interface**  
1. Check the VM’s IP address by running:  
   ```bash
   ifconfig
   ```  
   (or `ip a` if `ifconfig` is not available)  
2. Open a web browser and go to:  
   ```  
   http://<EVE-NG_VM_IP>  
   ```  
3. Login with:  
   - **Username**: `admin`  
   - **Password**: `eve`  

---

## **Step 6: Upload Network Images (IOS, vRouter, etc.)**  
EVE-NG requires device images (e.g., Cisco IOS, Juniper vMX, Arista vEOS).  
1. **Via SCP/WinSCP**:  
   - Use an SCP client (like WinSCP) to transfer images to:  
     - `/opt/unetlab/addons/qemu/` (for QEMU devices)  
     - `/opt/unetlab/addons/iol/bin/` (for Cisco IOL)  
2. **Via Web UI**:  
   - Go to **Lab → Manage Files**  
   - Upload images directly (slower but GUI-based)  

---

## **Step 7: Fix Common Issues**  
### **1. Nested Virtualization Not Working?**  
- Ensure **VT-x/AMD-V** is enabled in BIOS  
- In VMware, go to **VM Settings → Processors → Enable Virtualization**  

### **2. Web Interface Not Loading?**  
- Check if the VM has an IP (`ifconfig`)  
- Try restarting Apache:  
  ```bash
  service apache2 restart
  ```  

### **3. Slow Performance?**  
- Increase **RAM & CPU cores**  
- Use **SSD storage**  
- Disable unnecessary background services  

---

## **Next Steps**  
✅ Start creating labs with Cisco, Juniper, Linux, and other devices  
✅ Explore EVE-NG’s CLI for advanced configurations  
✅ Join the [EVE-NG Community Forum](https://www.eve-ng.net/forum/) for support  

---

## **Final Notes**  
- EVE-NG **Community Edition is free** but lacks some Pro features  
- Some network images (Cisco IOS, Juniper) require **legal licensing**  
- For production use, consider **EVE-NG Professional**  
