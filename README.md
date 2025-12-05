# Proxmox VE Flash

![Version](https://img.shields.io/badge/version-9.1--1-blue.svg) ![Platform](https://img.shields.io/badge/platform-x86__64-lightgrey.svg) ![License](https://img.shields.io/badge/license-AGPLv3-green.svg)

**Proxmox VE Flash** is a modified build of the Proxmox Virtual Environment installer. It allows the user to install Proxmox Virtual Environment on practically any storage media. Including eMMC, USB Flash Drives, External Drives, SD Cards etc.

## Features
* **eMMC Support:** Allows for installation on eMMC on Mini PCs and Single Board Computers (Intel NUC, ZimaBoard, Wyse Thin Clients etc).
* **SD Card Support:** Enables installation on internal SD card readers. Great for server motherboards with internal SD slots and handhelds like the Steam Deck.
* **USB Drives:** Lets you install Proxmox on USB Flash drives, external USB/SATA/nVME drives.

## ⚠️ Critical Warnings ⚠️
Running a Hypervisor on flash memory requires multiple considerations and is absolutely not recommended by Proxmox. To prevent rapid drive failure:
1.  **Filesystem:** NEVER use ZFS on flash media. Always select **ext4** during installation.
2.  **Swap:** Disable swap an monitor memory closely. Heavy swapping will kill an SD/eMMC card quickly.
3.  **High Availability:** Disable HA services post-install to stop constant disk polling:
    ```bash
    systemctl disable --now pve-ha-lrm
    systemctl disable --now pve-ha-crm
    ```
4. **VM's and LXC's** You should not install your VM's and containers on the USB/SD/eMMC as it will cause rapid drive failure.

## Installation
1.  **Download:** Go to the [Releases Page](../../releases) and download the latest `.iso`.
2.  **Flash:** Use [Rufus](https://rufus.ie) or [BalenaEtcher](https://www.balena.io/etcher/) to burn the ISO to a USB stick.
3.  **Ventoy** ALternativley you can use a Ventoy drive ([https://www.ventoy.net](https://www.ventoy.net)) and just copy the iso to the correct partition.
4.  **Tip** You should take note of the name of your USB Installer drive so you dont install Proxmox over the top of it and crash the device.
5.  **Tip** If you're installing on a USB Flash Drive it helps if you use a different brand or size USB Drive to help differentiate between the target drive and the installer drive.
6.  **Boot:** Insert the USB stick into your target machine and boot from USB. Select Proxmox from the list if using Ventoy.
7.  **Install:** Follow the standard Proxmox wizard. Your USB/eMMC/SD devices should now appear in the target disk dropdown.
