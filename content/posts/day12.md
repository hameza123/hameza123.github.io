+++ 
draft = false
date = 2025-09-11T09:35:13+01:00
title = "Day 12: SOC Analyst Challenge "
description = "SOC Analyst Challenge, Day 12 "
slug = "SOC Analyst Challenge, Day 12 "
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge",""]
categories = ["SOC",""]
series = []
+++


## Day 12: Ubuntu Server 24.02 Installation (SSH-server)
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)


### **Prerequisites**
1.  **VMware Fusion:** 
2.  **Ubuntu Server ISO:** Download the latest **24.04 LTS** version from the official [Ubuntu Download page](https://ubuntu.com/download/server).


### **Step 1: Create a New Virtual Machine**
1.  Open **VMware Fusion**.
2.  From the menu bar, click **File > New**.
3.  Select **Create a custom virtual machine**. Click **Continue**.
4.  **Choose Operating System:**
    *   Select **Linux**.
    *   From the Version dropdown, choose **Ubuntu 64-bit**.
    *   Click **Continue**.
5.  **Choose Firmware:** Select **UEFI** (recommended). Click **Continue**.
6.  **Virtual Disk:** Choose **SCSI** (recommended). Click **Continue**.
7.  **Create Disk:** Select **Create a new virtual disk**. Set the size (e.g., **25 GB** is a good minimum). Choose **Split into multiple files**. Click **Continue**.


### **Step 2: Configure Hardware *Before* Installation**
**Crucial Step:** *Before* clicking Finish, you must configure the VM to boot from the ISO.
1.  **Uncheck** the box for **"Power on this virtual machine after creation"**.
2.  Click **Customize Settings**.
3.  Name your VM (e.g., `ubuntu-server-24.04`) and choose a save location. Click **Save**.

A settings window will open. Configure the following:

*   **Processors & Memory:** Assign at least **2 CPU cores** and **2048 MB (2 GB)** of RAM. 4 GB is better if available.
*   **CD/DVD (IDE):** Change from "Auto detect" to **"Choose a disk or image..."**. Navigate to and select the downloaded Ubuntu Server 24.04 ISO file. Ensure **"Connect CD/DVD Drive"** is checked.
*   **Network Adapter:** Ensure it is connected (e.g., **Bridged Networking** or **Share with my Mac (NAT)**).

**Close the settings window** to save.


### **Step 3: Install Ubuntu Server 24.04**
1.  In the Virtual Machine Library, select your new VM and click the **Play** button.
2.  The VM will boot into the Ubuntu Server installer.
3.  **Language:** Select your language and press `Enter`.
4.  **Keyboard Configuration:** Choose your keyboard layout and variant.
5.  **Installer Version:** Choose the default **"Ubuntu Server"** and press `Enter`.
6.  **Network Connections:** The installer should automatically detect your network. You can configure a static IP later if needed. Select "Done".
7.  **Proxy:** If you use a network proxy, enter it here. Otherwise, leave blank. Select "Done".
8.  **Ubuntu Archive Mirror:** Use the default mirror. Select "Done".
9.  **Storage Configuration:** 
    *   The installer will likely present your virtual disk. Select it and press `Enter`.
    *   Choose the default **"Use an entire disk"** layout.
    *   Confirm the storage configuration and select **"Done"**.
10. **Profile Setup:**
    *   **Your name:** Enter your full name.
    *   **Your server's name:** Choose a hostname (e.g., `ubuntu-server`).
    *   **Pick a username:** Create your username (e.g., `admin`).
    *   **Choose a password:** Set a strong password.
    *   **Confirm your password:** Re-enter the password.
    *   Select "Done".
11. **SSH Setup:** 
    *   **Important:** Check the box for **"Install OpenSSH server"**. This allows you to connect to the server remotely.
    *   You can also import SSH keys from GitHub or Launchpad for more secure authentication.
    *   Select "Done".
12. **Featured Server Snaps:** You can browse and select optional software to install (e.g., Docker, Kubernetes). For a minimal setup, just select "Done".
13. **Wait for Installation:** The system will now install the base system and all selected packages. This will take a few minutes.
14. **Reboot:** When prompted, choose **"Reboot Now"**.


### **Step 4: Post-Installation Setup**
1.  After rebooting, the VM will prompt you to remove the installation media.
    *   Go to the VMware Fusion menu: **Virtual Machine > CD/DVD > Disconnect ISO image**.
    *   Press `Enter` in the VM window to continue booting.
2.  **Login:** You will see the console login prompt. Log in with the username and password you created.


### **Step 5: Install VMware Tools (Open-VM-Tools)**
VMware Tools provides essential drivers for better performance, graphics, and mouse integration.
1.  Update the package list:
    ```bash
    sudo apt update
    ```
2.  Install the open-source VMware Tools package:
    ```bash
    sudo apt install open-vm-tools
    ```
   
3.  Reboot the server to activate the tools:
    ```bash
    sudo reboot
    ```

### **Step 6: First Steps on Your New Server**
1.  **Update the System:** Always update after a fresh install:
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
2.  **Find Your IP Address:** To connect via SSH from your Mac.
    ```bash
    ip a
    ```
    Look for the `inet` address under `ens160` or `eth0` (e.g., `192.168.1.150`).
3.  **Connect via SSH (from your Mac Terminal):**
    ```bash
    ssh your_username@your_server_ip
    ```
    Example: `ssh admin@192.168.1.150`

Your Ubuntu Server 24.04 LTS installation on VMware Fusion is now complete and ready for use. 