+++ 
draft = false
date = 2025-09-10T18:10:20+01:00
title = " Installing Kibana, Day 5 "
description = "SOC Analyst Challenge, Day 5 "
slug = "SOC Analyst Challenge, Day 5 "
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["cybersecurity"]
series = []
+++

## Day 5: Windows Server 2022 Installation
Ressources : [MyDFIR Channel](https://www.youtube.com/@MyDFIR/videos)
## Part 1: The Installation

Go to [Windows Server 2022 ISO](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022) and select the **ISO** option for your desired edition (e.g., **Standard**), 

### 1. Open VMware Fusion and Start Creation
- Launch **VMware Fusion**.
- From the menu bar, click **File** > **New**.
- Click **Create a custom virtual machine**. This gives you full control over the configuration. Click **Continue**.

### 2. Choose Operating System
- Select **Windows** as the guest operating system.
- From the Version dropdown, scroll down and select **Windows Server 2022** or **Windows Server 2022 (64-bit)**.
- Click **Continue**.

### 3. Choose Firmware Type
- Select **UEFI** (Highly recommended for modern Windows systems). Click **Continue**.

### 4. Choose Virtual Disk Type
- Select **NVMe** (recommended for performance) or **SCSI**. Click **Continue**.

### 5. Create a New Virtual Disk
- Ensure **Create a new virtual disk** is selected.
- Set the maximum disk size. **80 GB** is a good starting point for a server with a GUI.
- Choose **Split into multiple files** (recommended for portability).
- Click **Continue**.

### 6. Finalize Creation & Configure Settings
- **Before clicking Finish:** Uncheck the box for **Power on this virtual machine after creation**.
- Click **Customize Settings**.
- A save dialog will appear. Name your VM (e.g., `Win-Server-2022`) and choose a location. Click **Save**.

### 7. Configure Virtual Machine Hardware
A settings window will open. Configure the following key components:

#### **7.1. Processors & Memory**
- **Processors:** Assign at least **2** CPU cores. 4 or more is better for performance.
- **Memory:** Assign at least **4096 MB** (4 GB) of RAM. **8 GB (8192 MB) is strongly recommended** for a smooth experience, especially with the Desktop Experience GUI.

#### **7.2. Hard Disk (NVMe or SCSI)**
- You can adjust the size here if needed.

#### **7.3. CD/DVD (IDE) - MOST IMPORTANT STEP**
- Change the setting from **Auto detect** to **Choose a disk or image...**.
- Navigate to and select the downloaded **Windows Server 2022 ISO** file.
- Ensure **Connect CD/DVD Drive** is checked.

#### **7.4. Network Adapter**
- Ensure the adapter is connected to:
    - **Bridged Networking** (Recommended): Gives the VM its own IP on your network, like a physical machine.
    - **Share with my Mac** (NAT): Hides the VM behind your Mac's IP. Good if you don't need direct network access.

#### **7.5. (Optional) Add a Serial Port for Console Access**
- Click the **+** button at the bottom.
- Select **Serial Port**.
- Choose **Output to named pipe** and use the default path (e.g., `/tmp/win-server-serial`). Useful for headless management.

### 8. Save and Start the Installation
- Close the settings window to save the configuration.
- In the Virtual Machine Library, select your new **Windows Server 2022** VM.
- Click the **Play** button to power it on.

### 9. Install Windows Server 2022
The VM will boot from the ISO and start the Windows Server installer.

1.  **Language & Preferences:** Choose your language, time, and keyboard input. Click **Next**.
2.  **Install Now:** Click the **Install Now** button.
3.  **Select OS Edition:** Choose your edition (e.g., **Windows Server 2022 Standard Evaluation (Desktop Experience)** for a full GUI) and click **Next**.
4.  **License Terms:** Accept the license terms and click **Next**.
5.  **Installation Type:** Select **Custom: Install Windows only (advanced)**.
6.  **Disk Partitioning:** You will see your virtual disk. Select it and click **Next**. The installer will automatically create the necessary partitions.
7.  **Wait for Installation:** The OS files will copy and install. The VM will reboot several times automatically.

### 10. Post-Installation Setup
After the install, the VM will boot into the Out-of-Box Experience (OOBE).

1.  **Administrator Password:** You will be prompted to set a password for the built-in **Administrator** account. **Create a strong password and store it securely.**
2.  **Ctrl+Alt+Delete:** Press **Ctrl+Alt+Delete** to log in. In VMware Fusion, you can do this from the menu: **Virtual Machine** > **Send Ctrl-Alt-Del**.
3.  **Login:** Enter the password for the Administrator account.

### 11. Install VMware Tools (Essential)
VMware Tools provides crucial drivers for better graphics, mouse performance, network, and shared folders.

- After logging in, a VMware Tools installation dialog might appear automatically. If it does, follow the prompts.
- If it doesn't, go to the VMware Fusion menu bar: **Virtual Machine** > **Install VMware Tools**.
- Follow the on-screen instructions in the Windows VM to complete the installation. **Reboot when prompted.**

### 12. Configure Network (If Necessary)
- Open **Server Manager** > **Local Server**.
- Click on the Ethernet link (e.g., "Ethernet0") to configure network settings (static IP, DNS, etc.) if you are not using DHCP.

### 13. (Optional) Activate Windows
- If you have a product key, you can activate it via **Settings** > **Activation**.
- If using the evaluation, you have a 180-day trial period.

## Initial Configuration Checklist
Once logged in, perform these initial tasks:
-   **Windows Update:** Install all available updates.
-   **Computer Name:** Change the server's name in **System Properties**.
-   **Join Domain (Optional):** If you have an Active Directory domain, join the server to it.
-   **Add Roles & Features:** Use Server Manager to install roles like AD DS, DNS, DHCP, IIS, etc.

Your Windows Server 2022 virtual machine is now ready for use.	    

## Part 2: Activate RDP on Windows Server 2022

### 1. Enable Remote Desktop
1.  Log into your Windows Server 2022 VM.
2.  Open **Server Manager** (it usually opens automatically).
3.  In the left pane, click on **Local Server**.
4.  In the main properties pane, find the **Remote Desktop** property. It will likely say "Disabled".
5.  Click on the word **"Disabled"**.

### 2. Configure Remote Desktop Settings
A **System Properties** window will open to the "Remote" tab.

1.  Select the option: **Allow remote connections to this computer**.
2.  **Uncheck** the box that says **Allow connections only from computers running Remote Desktop with Network Level Authentication (recommended)**.
    *   *We uncheck this initially for simpler troubleshooting. You can re-enable it later for better security once you confirm it works.*
3.  A warning pop-up will appear. Click **OK**.
4.  Click **Apply** and then **OK** to close the window.

### 3. Configure the Windows Firewall (Crucial Step)
The Windows Firewall is on by default and will block RDP connections if not configured.

1.  Press the `Win` key, type **Windows Defender Firewall**, and open it.
2.  In the left pane, click **Advanced settings**.
3.  In the **Windows Defender Firewall with Advanced Security** window, click on **Inbound Rules** in the left pane.
4.  In the list of rules, find the rules named **Remote Desktop - User Mode (TCP-In)** and **Remote Desktop - User Mode (UDP-In)**.
5.  For both of these rules, right-click each one and select **Enable Rule**. Ensure they are enabled for the "Domain" and "Private" profiles (you can also enable "Public" if needed, though it's less secure).

### 4. Find Your Server's IP Address
You will need this IP address to connect from your Mac.

1.  Press `Win + R`, type `cmd`, and press Enter to open a Command Prompt.
2.  In the command prompt, type `ipconfig` and press Enter.
3.  Look for the **IPv4 Address** under your active network adapter (e.g., `Ethernet0`). It will look something like `192.168.1.150`.
4.  **Write this IP address down.**

## Part 3: Test RDP from Your Mac using Microsoft Remote Desktop

### 1. Install Microsoft Remote Desktop
The official Microsoft client is free on the Mac App Store.

1.  Open the **App Store** on your Mac.
2.  Search for **"Microsoft Remote Desktop"**.
3.  Click **Get** and then **Install**. 

### 2. Add a PC Connection
1.  Open the **Microsoft Remote Desktop** app.
2.  Click the **Add PC** button.

    

3.  In the window that appears:
    *   **PC name:** Enter the IP address of your Windows Server (e.g., `192.168.1.150`).
    *   **User account:** Click the dropdown and select **Add User Account...**.
        *   Enter the username: `Administrator`
        *   Enter the password you set for the Administrator account during Windows Server setup.
        *   Click **Add**.
4.  Leave other settings as default for now. Click **Add**.

### 3. Connect to Your Server
1.  Back in the main window of the Remote Desktop app, you will see an icon for your server under "My Desktops".
2.  Double-click on the icon to initiate the connection.
3.  You may see a certificate warning. Check the box **"Don't ask me again for connections to this computer"** and click **Continue**.

### 4. Troubleshooting Connection Issues
If you cannot connect, check the following on the Windows Server VM:

*   **Firewall:** Double-check that the **Remote Desktop - User Mode (TCP-In)** rule is enabled in the Windows Firewall's Inbound Rules.
*   **IP Address:** Ensure you are using the correct IPv4 address from `ipconfig`.
*   **Network Type:** Ensure your VM's network adapter in VMware Fusion is set to **Bridged** (not "NAT") for easiest connectivity. If using NAT, you may need to set up port forwarding.
*   **RDP Setting:** Go back to the System Properties > Remote tab and ensure **"Allow remote connections..."** is selected.

Your connection from the Mac should still work, as the Microsoft Remote Desktop client supports NLA.
