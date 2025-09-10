+++ 
draft = false
date = 2025-09-10T18:10:18+01:00
title = "Day 4: SOC Analyst Challenge "
description = "SOC Analyst Challenge, Day 4 "
slug = "SOC Analyst Challenge, Day 4 "
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["cybersecurity"]
series = []
+++

## Day 4: Installing Kibana

1. Download the Package

	- Go to the official [Kibana](https://www.elastic.co/downloads/kibana) Download page.
	- Under "Linux x86_64", right-click on the DEB package link and select Copy Link Address.

	The version number in the link might be different (e.g., 9.1.3).

2. Download on Your Ubuntu Server

	Open a terminal on your Ubuntu server and use wget with the copied link to download the package.

	```bash
	sudo wget https://artifacts.elastic.co/downloads/kibana/kibana-9.1.3-amd64.deb
	```

3. Install the Downloaded Package

	Use the dpkg command to install the package. This will set up Elasticsearch and its required directories.

	```bash
	sudo dpkg -i kibana-9.1.3-amd64.deb 
	```

4. Configure Kibana

	Edit the Kibana configuration file to allow external connections and point to your Elasticsearch instance.

	```bash
	sudo nano /etc/kibana/kibana.yml
	```
	```bash
	# =================== System: Kibana Server ===================
	# Kibana is served by a back end server. This setting specifies the port to use.
	server.port: 5601

	# Specifies the address to which the Kibana server will bind. IP addresses and host names are both valid values.
	# The default is 'localhost', which usually means remote machines will not be able to connect.
	# To allow connections from remote users, set this parameter to a non-loopback address.
	server.host: your ip address
	```
	Save the file and exit (Ctrl+X, then Y, then Enter).


5. Enable and Start the Kibana Service

	After installation, you must reload the systemd manager configuration to recognize the new service, then start it.

	```bash
	sudo systemctl daemon-reload
	sudo systemctl enable kibana.service
	sudo systemctl start kibana.service
	```

6. Verify the Installation

	Check if the Elasticsearch service is running correctly.

	```bash
	sudo systemctl status elasticsearch.service
	```

7. Generate the Kibana Enrollment Token

	Kibana needs a token to enroll itself with Elasticsearch's security features. Generate this token on the Elasticsearch server.

	```bash
	# Navigate to the Elasticsearch bin directory and generate the token
	cd /usr/share/elasticsearch/bin
	sudo ./elasticsearch-create-enrollment-token --scope kibana
	```

	Copy the long token string that is output. You will need it for the next step.

8. Complete Kibana Setup in the Web Browser

		- Open a web browser on your host machine (e.g., your Mac).
		- Navigate to http://<your-ubuntu-server-ip>:5601 (e.g., http://192.168.1.100:5601).
		- You will be prompted for the Enrollment Token. Paste the token you copied in the previous step and click Enroll.

	The setup process will now ask for a verification code. This code is generated on the Kibana server.

	```bash
	# Navigate to the Kibana bin directory and generate the code
	cd /usr/share/kibana/bin
	sudo ./kibana-verification-code
	```

	Copy the 6-digit code that is displayed. Enter it in the browser window.

9. Log in to Kibana

	 You will now be prompted to log in.

		- Username: elastic
		- Password: Use the password you saved during the Elasticsearch installation. If you did not save it, you can reset it using the elasticsearch-reset-password tool.

10. Generate Encryption Keys

	For production-like environments, you should generate encryption keys for Kibana's saved objects and store them in its keystore.


	```bash
	# Navigate to the Kibana bin directory
	cd /usr/share/kibana/bin

	# Generate the encryption keys. Copy the output.
	sudo ./kibana-encryption-keys generate

	# Add each generated key to the Kibana keystore
	sudo ./kibana-keystore add xpack.encryptedSavedObjects.encryptionKey
	# Enter the value for xpack.encryptedSavedObjects.encryptionKey when prompted.

	sudo ./kibana-keystore add xpack.reporting.encryptionKey
	# Enter the value for xpack.reporting.encryptionKey when prompted.

	sudo ./kibana-keystore add xpack.security.encryptionKey
	# Enter the value for xpack.security.encryptionKey when prompted.
	```

	After all configuration, restart both services to ensure all changes take effect.

	```bash
	sudo systemctl restart kibana.service
	sudo systemctl restart elasticsearch.service
	```

	You have now successfully installed Kibana and connected it to your Elasticsearch node. You can access the Kibana dashboard at http://<your-ubuntu-server-ip>:5601 to begin visualizing your data.





