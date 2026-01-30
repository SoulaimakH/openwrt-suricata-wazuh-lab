# OpenWRT – Suricata – Wazuh Security Monitoring Lab
##  Architecture

![Network Diagram](archi.png)

([demo video](https://1drv.ms/v/c/1e4f43606a99e40f/IQCUqWb0N___SYkuLzg0T-M3AWgWBU3B8llT6WEnyervUZU?e=08njB3))
[![Watch the demo](wazuh.png)](https://1drv.ms/v/c/1e4f43606a99e40f/IQCUqWb0N___SYkuLzg0T-M3AWgWBU3B8llT6WEnyervUZU?e=08njB3)

##  About This Project

**Network Security Monitoring Lab** is a hands-on, end-to-end lab environment designed to simulate real-world cybersecurity operations.  
It demonstrates the integration of **network traffic analysis, intrusion detection, and security information and event management (SIEM)** in a controlled environment.

The lab uses:

- **OpenWRT VM** as a router and traffic mirroring device
- **Ubuntu VM** hosting **Suricata IDS** and **Wazuh SIEM**
- **Kali Linux VM** for penetration testing and attack simulation

Key features:

- Traffic mirroring from OpenWRT to Suricata for passive network monitoring
- Detection of real-world attacks such as port scans, brute-force attempts, and web vulnerability scans
- Centralized alert analysis and visualization via Wazuh Dashboard
- JSON-based integration of Suricata EVE logs for rich context and accurate detection
- Fully virtualized setup with reproducible VirtualBox VMs

This project serves as:

- A **learning platform** for SOC analysts and cybersecurity students
- A **demonstration environment** for intrusion detection and SIEM integration
  
The lab environment is hosted on VirtualBox, including three VMs: OpenWRT (router and traffic mirroring), Ubuntu 22.04 (Suricata IDS + Wazuh SIEM), and Kali Linux (attack simulation) with network interfaces configured for public LAN and private monitoring networks.



### Components
- **OpenWRT Router**
  - Routes internal traffic
  - Mirrors traffic only to Suricata through a private network
- **Ubuntu Server**
  - Dockerized Suricata IDS
  - Dockerized Wazuh Manager & Dashboard
- **Kali Linux**
  - Vulnerability scanning & attack simulation



## Traffic Flow

1. Internal devices communicate through OpenWRT
2. OpenWRT mirrors traffic (port mirroring)
3. Mirrored traffic is sent to Suricata (private network only)
4. Suricata analyzes packets and generates alerts
5. Alerts are forwarded to Wazuh SIEM
6. Wazuh visualizes alerts on the dashboard



## 🛠 Technologies Used

- OpenWRT
- Suricata IDS
- Wazuh SIEM
- Docker & Docker Compose
- Kali Linux
- Ubuntu Server
- Nmap, Nikto, Hydra


OpenWRT is installed as a virtual router using an official OpenWRT disk image converted to VirtualBox format.

## OpenWRT Image Source

The OpenWRT image was downloaded from the official archive:[vdi](https://archive.openwrt.org/releases/19.07.10/targets/x86/64/)

```
openwrt-19.07.10-x86-64-combined-ext4.img
```

### Convert OpenWRT Image to VirtualBox VDI

Since VirtualBox does not directly support .img files, the image was converted to .vdi format using VBoxManage.

Conversion Command
```
"VBoxManage.exe" convertdd \
"Lab\openwrt-19.07.10-x86-64-combined-ext4.img" \
"lab\openwrt.vdi"
```
convertdd: converts raw disk images to VirtualBox disk format

### Resize OpenWRT Virtual Disk

After conversion, the virtual disk size was increased to ensure sufficient storage.
```
"VBoxManage.exe" modifyhd --resize 512 \
"lab\openwrt.vdi"
```
Resizes the virtual disk to 512 MB

Required for package installation and logging

Must be done before booting the VM

#### Virtual Machine Configuration (Summary)
+ Hypervisor-> Oracle VirtualBox
+ OpenWRT Version->19.07.10
+ Disk Format->VDI
+ Disk Size->512 MB
+ Network Interfaces->Public LAN + Private Monitoring
  
## ⚙️ Configuration Steps

### 1 OpenWRT – Traffic Mirroring config
See:
```text
openwrt/port-mirroring.md
```

### 2 Ubuntu – config
+  Install Docker 
+ | enp0s10 | Private monitoring network | 10.0.3.15/24 |


### 3 suricata
See:
```text
suricata/README.md
```

### 3 wazuh
See:
```text
wazuh/README.md
```

### 3 Kali
See:
```text
kali-tests/README.md
```

## 🛠️ Maintenance & Troubleshooting Notes
### Disk Space Cleanup (Ubuntu VM)

If the Ubuntu virtual machine disk grows too large or cannot be compacted properly, use the following procedure.

#### 1. Zero free space inside the Ubuntu VM

This helps VirtualBox detect unused disk space.
```
sudo dd if=/dev/zero of=/zerofile bs=10M status=progress
sudo rm /zerofile
sync
```

⚠️ This operation may take some time depending on disk size.

#### 2. Compact the VirtualBox disk (from Windows host)

After shutting down the VM, run the following command on the host machine:

"VBoxManage.exe" modifymedium disk "lab\Ubuntu_22.04_VB_LinuxVMImages.COM.vdi" --compact

* The VM must be powered off before running the --compact command.
* This operation reduces the physical size of the .vdi file on disk.
* Useful when Docker, logs, or IDS data (Suricata / Wazuh) significantly increase disk usage.
