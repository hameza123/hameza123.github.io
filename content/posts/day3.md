+++ 
draft = false
date = 2025-09-10T18:10:15+01:00
title = "Day 3: SOC Analyst Challenge "
description = "SOC Analyst Challenge, Day 3 "
slug = "SOC Analyst Challenge, Day 3 "
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["cybersecurity"]
series = []
+++



## Day 3: ELK Stack Istallation

### Create private network

1. Open VMware Fusion Preferences

	- Launch VMware Fusion.
	- From the menu bar, navigate to Window > Virtual Machine Library.
	- With the library open, go to the VMware Fusion menu in the menu bar and click Preferences....

2. Access the Network Settings

	In the Preferences window, click on the Network icon (it resembles a globe with a network cable).

3. Unlock the Settings for Changes

	- Click the lock icon in the bottom-left corner of the window.
	- Enter your Mac's administrator password when prompted to allow changes.

4. Create a New Custom Network

	- Click the + (plus) button located below the list of existing networks.
	- A new network entry (e.g., vmnet3) will appear in the list. Select it to configure its properties.

5. Configure the "Private Network"

 	With the new vmnet selected, configure the settings on the right-hand side:

	- Name: Change the default name to Private Network.
	- Subnet IP: You can keep the automatically generated address (e.g., 192.168.xxx.0) or define your own (e.g., 10.0.10.0).
	- Subnet Mask: The default 255.255.255.0 is recommended for a standard Class C network.
	- Ensure the checkbox for "Provide addresses on this network via DHCP" is selected. This is crucial for automatically assigning IP addresses to VMs.

6. Apply the New Configuration

	Click the Apply button to save and activate your new "Private Network".

### Create ELK server

1. Download Ubuntu Server ISO

	- Open a web browser and go to the [Ubuntu Server download page](https://ubuntu.com/download/server).
	- Download the latest LTS version (e.g., 64-bit PC (AMD64) server install image).

2. Open VMware Fusion and Start Creation

	- Launch VMware Fusion.
	- From the menu bar, click File > New.
	- Alternatively, in the Virtual Machine Library, click the + button and choose New.

3. Choose Installation Method

	- Select Create a custom virtual machine and click Continue.

4. Choose Operating System

	- Select Linux as the guest operating system.
	- From the Version dropdown, choose Ubuntu 64-bit.
	- Click Continue.

5. Select Firmware Type

	- Choose UEFI (recommended) or BIOS (legacy) and click Continue.

6. Choose Virtual Disk Type

	- Select SCSI (recommended) and click Continue.

7. Create a New Virtual Disk

	- Ensure Create a new virtual disk is selected.
	- Set the maximum disk size (e.g., 20 GB).
	- Choose Split into multiple files (recommended for portability).
	- Click Continue.

8. Customize Settings Before Installation

	Before clicking Finish, ensure the checkbox for Power on this virtual machine after creation is unchecked.
	Click Customize Settings.

9. Configure Virtual Machine Hardware

 	A new settings window will open. Configure the following:

	9.1. General

		- Name: Ubuntu Server (or your preferred name).

	9.2. Processors & Memory

		- Processors: Assign at least 2 CPU cores.
		- Memory: Assign at least 2048 MB (2 GB) of RAM.

	9.3. Hard Disk (SCSI)

		- You can adjust the disk size here if needed.

	9.4. CD/DVD (IDE)

		- Change the setting from Auto detect to Choose a disk or image....
		- Navigate to and select the downloaded Ubuntu Server ISO file.
		- Ensure Connect CD/DVD Drive is checked.

	9.5. Network Adapter

	For a network connection, ensure the adapter is connected to:

		- Bridged Network (to be like a separate device on your network), or
		- Share with my Mac (NAT) for internet access.

10. Save and Start the Virtual Machine

	- Close the settings window to save all configuration.
	- In the Virtual Machine Library, select your new Ubuntu Server VM.
	- Click the Play button to power it on.

11. Install Ubuntu Server

	The VM will boot from the ISO and start the Ubuntu Server installer.
	Follow the on-screen instructions to complete the installation:

	- Choose your language.
	- Configure keyboard layout.
	- Set up network connections (if needed).
	- Configure storage (use the entire virtual disk).
	- Set up your user profile and server name.
	- Select which software to install (e.g., SSH server).
	- When the installation is complete, the installer will prompt you to reboot.

12. Final Reboot and Login

 	The VM will reboot. Eject the installation media:

	- Go to the VMware Fusion menu bar: Virtual Machine > CD/DVD > Disconnect ISO image.
	- The VM will now boot from its new virtual hard disk.
	- Log in with the username and password you created during installation.

### Installing Elastic Search

1. Download the Package

	- Go to the official [Elasticsearch](https://www.elastic.co/downloads/elasticsearch) Download page.
	- Under "Linux x86_64", right-click on the DEB package link and select Copy Link Address.

	The version number in the link might be different (e.g., 9.1.3).

2. Download on Your Ubuntu Server

	Open a terminal on your Ubuntu server and use wget with the copied link to download the package.

	```bash
	sudo wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.1.3-amd64.deb
	```

3. Install the Downloaded Package

	Use the dpkg command to install the package. This will set up Elasticsearch and its required directories.

	```bash
	sudo dpkg -i elasticsearch-9.1.3-amd64.deb
	```

4. Modify the file elasticsearch.yml 

	Go to /etc/elasticsearch, then elasticsearch.yml and modify this two lines on the network section: 
	```bash
	---------------------------------- Network -----------------------------------
	#
	# By default Elasticsearch is only accessible on localhost. Set a different
	# address here to expose this node on the network:
	#
	network.host: your ip address
	#
	# By default Elasticsearch listens for HTTP traffic on the first free port it
	# finds starting at 9200. Set a specific HTTP port here:
	#
	http.port: 9200

	```


5. Reload systemd and Start Elasticsearch

	After installation, you must reload the systemd manager configuration to recognize the new service, then start it.

	```bash
	sudo systemctl daemon-reload
	sudo systemctl enable elasticsearch.service
	sudo systemctl start elasticsearch.service
	```


6. Verify the Installation

	Check if the Elasticsearch service is running correctly.

	```bash
	sudo systemctl status elasticsearch.service
	```