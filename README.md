# Proxmox VE Flash

![Version](https://img.shields.io/badge/version-9.1--1-blue.svg) ![Platform](https://img.shields.io/badge/platform-x86__64-lightgrey.svg)

**Proxmox VE Flash** is a modified build of the Proxmox Virtual Environment installer. It allows the user to install Proxmox Virtual Environment on practically any storage media.

## Features
* **eMMC Support:** Allows for installation on eMMC on Mini PCs and Single Board Computers (NUC, ZimaBoard, Wyse Thin Clients etc).
* **SD Card Support:** Enables installation on SD card readers. Great for server motherboards with internal SD slots and handhelds like the Steam Deck. (Please someone make a SteamDeck homelab).
* **USB Drives:** Lets you install Proxmox on USB Flash drives and external USB/SATA/nVME drives.

## ⚠️ Critical Steps ⚠️
Running a Hypervisor on flash storage is absolutely not recommended. Flash storage has terrible write endurance. To prevent rapid drive failure follow these guidelines:
1.  **Filesystem:** NEVER use ZFS on flash media. Always select **ext4** during installation.
2.  **Swap:** Disable swap on the drive and monitor memory closely. Regular swapping will kill an SD/eMMC drive very quickly.
3.  **VM's and LXC's:** You should not install your VM's and containers on the USB/SD/eMMC as it will cause rapid drive failure. External SSD's are best if you have no other storage interfaces available.
4.  **High Availability:** Disable HA services to stop constant disk polling by running the PVE post-install script to correct repositories and disable HA.
    Run this command in the shell of the node you installed:
    ```bash
    bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"
    ```
    Or simply paste this block to directly disable High Availability:
    ```bash
    systemctl disable --now pve-ha-lrm
    systemctl disable --now pve-ha-crm
    ```

5. **Ceph:** Disable Ceph if its installed.
    ```bash
    systemctl disable --now ceph-mgr.target
    systemctl disable --now ceph-mon.target
    systemctl disable --now ceph-osd.target
    ```

## Optional Steps
**Reduce Writes to Drive:**
These steps are recommended to help reduce writes to the disk, but are completely optional:
* This will prevent temporary writes to the disk and force the kernal to use RAM only for VM's.
   ```bash
   sysctl vm.swappiness=0
   ```
  If the system keeps running out of memory and crashes, put swappiness back to 10
  ```bash
  sysctl vm.swappiness=10
  ```
  
**Prevent time/date writes on every file read:**
You will need to edit /etc/fstab:
* Locate the line related to your root partition. eg /dev/pve/root / ext4 errors=remount-ro 0 1
* Add the ,noatime flag to that line. eg /dev/pve/root / ext4 errors=remount-ro,noatime 0 1
  

## Installation
1.  **Download:** Go to the [Releases Page](../../releases) and download the latest `.iso`.
2.  **Flash:** Use [Rufus](https://rufus.ie) or [BalenaEtcher](https://www.balena.io/etcher/) to burn the ISO to a USB stick.
3.  **Ventoy** ALternativley you can use a Ventoy drive ([https://www.ventoy.net](https://www.ventoy.net)) and just copy the iso to the image partition.
4.  **Tip** You should take note of the name of your USB Installer drive so you dont install Proxmox over the top of it and crash the device.
5.  **Tip** If you're installing on a USB Flash Drive it helps if you use a different brand or size USB Drive to help differentiate between the target drive and the installer drive.
6.  **Boot:** Insert the USB stick into your target machine and boot from USB. Select Proxmox from the list if using Ventoy.
7.  **Install:** Follow the standard Proxmox wizard. Your USB/eMMC/SD devices should now appear in the target disk dropdown.
