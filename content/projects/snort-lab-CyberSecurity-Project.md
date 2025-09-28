+++
draft = false
date = '2025-09-28T11:21:55+01:00'
title = "How To Install an IDS (Snort)"
description = "This guide will walk you through installing and configuring Snort, a powerful open-source Intrusion Detection System"
tags = ["Snort", "SOC Analyst", "IDS", "challenge"]
slug = "projects"
categories = ["cybersecurity"]
series = []
+++




# Snort 3 Installation Guide - Complete Walkthrough

Great! You have a comprehensive Snort 3 installation script. Let me organize this into a complete, step-by-step guide with explanations and improvements.
## Prerequisites Setup

### System Preparation
```bash
# Update system first
sudo apt update && sudo apt upgrade -y

# Install all prerequisites in one command (fixed version)
sudo apt-get install -y build-essential autotools-dev libdumbnet-dev libluajit-5.1-dev \
libpcap-dev zlib1g-dev pkg-config libhwloc-dev cmake liblzma-dev openssl libssl-dev \
cpputest libsqlite3-dev libtool uuid-dev git autoconf bison flex libcmocka-dev \
libnetfilter-queue-dev libunwind-dev libmnl-dev ethtool libjemalloc-dev

# Create working directory
mkdir ~/snort
cd ~/snort
```

## Dependency Installation

### Step 1: Install PCRE
```bash
cd ~/snort
wget https://sourceforge.net/projects/pcre/files/pcre/8.45/pcre-8.45.tar.gz
tar -xzvf pcre-8.45.tar.gz
cd pcre-8.45
./configure
make
sudo make install
```

### Step 2: Install gperftools
```bash
cd ~/snort
wget https://github.com/gperftools/gperftools/releases/download/gperftools-2.9.1/gperftools-2.9.1.tar.gz
tar xzvf gperftools-2.9.1.tar.gz
cd gperftools-2.9.1
./configure
make
sudo make install
```

### Step 3: Install Ragel
```bash
cd ~/snort
wget http://www.colm.net/files/ragel/ragel-6.10.tar.gz
tar -xzvf ragel-6.10.tar.gz
cd ragel-6.10
./configure
make
sudo make install
```

### Step 4: Download Boost C++ Libraries
```bash
cd ~/snort
wget https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.tar.gz
tar -xvzf boost_1_77_0.tar.gz
```

### Step 5: Install Hyperscan
```bash
cd ~/snort
wget https://github.com/intel/hyperscan/archive/refs/tags/v5.4.2.tar.gz
tar -xvzf v5.4.2.tar.gz
mkdir ~/snort/hyperscan-5.4.2-build
cd hyperscan-5.4.2-build/
cmake -DCMAKE_INSTALL_PREFIX=/usr/local -DBOOST_ROOT=~/snort/boost_1_77_0/ ../hyperscan-5.4.2
make
sudo make install
```

### Step 6: Install FlatBuffers
```bash
cd ~/snort
wget https://github.com/google/flatbuffers/archive/refs/tags/v2.0.0.tar.gz -O flatbuffers-v2.0.0.tar.gz
tar -xzvf flatbuffers-v2.0.0.tar.gz
mkdir flatbuffers-build
cd flatbuffers-build
cmake ../flatbuffers-2.0.0
make
sudo make install
```

### Step 7: Install Data Acquisition (DAQ)
```bash
cd ~/snort
wget https://github.com/snort3/libdaq/archive/refs/tags/v3.0.13.tar.gz -O libdaq-3.0.13.tar.gz
tar -xzvf libdaq-3.0.13.tar.gz
cd libdaq-3.0.13
./bootstrap
./configure
make
sudo make install
```

### Step 8: Update Shared Libraries
```bash
sudo ldconfig
```

## Snort 3 Installation

### Step 9: Install Snort 3
```bash
cd ~/snort
wget https://github.com/snort3/snort3/archive/refs/tags/3.1.74.0.tar.gz -O snort3-3.1.74.0.tar.gz
tar -xzvf snort3-3.1.74.0.tar.gz
cd snort3-3.1.74.0
./configure_cmake.sh --prefix=/usr/local --enable-jemalloc
cd build
make -j$(nproc)  # Use all CPU cores for faster compilation
sudo make install
```

## test:

/usr/local/bin/snort -V
snort -c /usr/local/etc/snort/snort.lua 


## Network Configuration

### Step 10: Disable LRO/GRO (Important for packet capture)

ethtool -k ens32 | grep receive-offload

**Option 1: Manual (temporary)**
```bash
# Replace 'eth0' with your network interface
sudo ethtool -K eth0 gro off
sudo ethtool -K eth0 lro off
```

**Option 2: Create a systemd service (permanent)**
```bash
sudo nano /etc/systemd/system/ethtool.service
```

Add the following content (replace `eth0` with your interface):
```ini
[Unit]
Description=Ethtool Configuration for Network Interface
After=network.target

[Service]
Type=oneshot
ExecStart=/sbin/ethtool -K ens33 gro off
ExecStart=/sbin/ethtool -K ens33 lro off
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Enable the service:
```bash
sudo systemctl daemon-reload
sudo systemctl enable ethtool.service
sudo systemctl start ethtool.service
```

## create  rules:

mkdir /usr/local/etc/rules/
nano /usr/local/etc/rules/local.rules

alert icmp any any -> any any (msg :"icmp detected"; sid:1000001;)

snort -c /usr/local/etc/snort/snort.lua  -R /usr/local/etc/rules/local.rules

snort -c /usr/local/etc/snort/snort.lua  -R /usr/local/etc/rules/local.rules -i en33 -A alert_fast

nano /usr/local/etc/snort/snort.lua 
HOME_NET = 'any'
include = "/usr/local/etc/rules/local.rules",
snort -c /usr/local/etc/snort/snort.lua  -i en33 -A alert_fast


## Rule Management with PulledPork3

### Step 11: Install PulledPork3 for Rule Updates
```bash
cd ~/snort
git clone https://github.com/shirkdog/pulledpork3.git
sudo mkdir -p /usr/local/bin/pulledpork3
sudo cp pulledpork3/pulledpork.py /usr/local/bin/pulledpork3/
sudo cp -r pulledpork3/lib/ /usr/local/bin/pulledpork3/
sudo chmod +x /usr/local/bin/pulledpork3/pulledpork.py
sudo mkdir -p /usr/local/etc/pulledpork3
sudo cp pulledpork3/etc/pulledpork.conf /usr/local/etc/pulledpork3/
```
### . PulledPork3 Configuration


/usr/local/bin/pulledpork3/pulledpork.py -V
nano /usr/local/etc/pulledpork3/pulledpork.conf
registered_ruleset = true 
oinkcode = xxxxx
snort_path = /usr/local/bin/snort
local_rules = /usr/local/etc/rules/local.rules                             

/usr/local/bin/pulledpork3/pulledpork.py -c /usr/local/etc/pulledpork3/pulledpork.conf 


nano /usr/local/etc/snort/snort.lua 
include = "/usr/local/etc/rules/pulledpork.rules",

snort -c /usr/local/etc/snort/snort.lua --plugin-path "/usr/local/etc/so_rules/" -i en33 -A alert_fast

nano /usr/local/bin/pulledpork3/pulledpork.py 
RULESET_URL_SNORT_REGISTERED = 'https://snort.org/rules/snortrules-snapshot-31470.tar.gz'

/usr/local/bin/pulledpork3/pulledpork.py -c /usr/local/etc/pulledpork3/pulledpork.conf 

snort -c /usr/local/etc/snort/snort.lua --plugin-path "/usr/local/etc/so_rules/" 

wget https://www.malware-traffic-analysis.net/2022/02/23/2022-02-23-traffic-analysis-exercise.pcap.zip
unzip 2022-02-23-traffic-analysis-exercise.pcap.zip 
password infected_20220323


snort -c /usr/local/etc/snort/snort.lua --plugin-path "/usr/local/etc/so_rules/" -r  2022-02-23-traffic-analysis-exercise.pcap -A alert_fast -q > reslt


cat reslt | cut -d "]" -f 3 | cut -d "[" -f 1 | cut -d '"' -f 2 | sort | uniq -c |sort -nr

 grep -i malware reslt 





sudo mkdir -p /usr/local/etc/snort/appid
sudo mkdir -p /var/log/snort
sudo chmod 755 /usr/local/etc/snort/appid
sudo chmod 755 /var/log/snort


# Comprehensive monitoring with logging
sudo snort -c /usr/local/etc/snort/snort.lua \
           -i ens33 \
           -A alert_fast \
           -l /var/log/snort \
           --plugin-path /usr/local/etc/so_rules/ \
           --daq afpacket \
           --daq-mode passive

# Generate test traffic (in another terminal)
# ICMP ping (will trigger sid:1000001)
ping -c 3 google.com

# HTTP traffic (will trigger sid:1000002)
curl http://google.com

# DNS query (will trigger sid:1000004)
nslookup google.com








```
-- Advanced logging configuration
alert_csv = 
{
    file = true,
    fields = "action pkt_num gid sid rev msg service src_addr src_port dst_addr dst_port", separator = ","
}

alert_fast = 
{
    file = true,
    packet = true,  -- Include packet data
    limit = 10000   -- Max alerts per file
}

packet_log =
{
    file = true,
    limit = 100,    -- Max packets to log
    units = megs    -- Or 'bytes', 'pkts'
}

-- Rotate logs automatically
log_rotate =
{
    size = 100,     -- MB
    count = 10      -- Keep 10 files
}
```
































# Excellent! You've successfully installed and configured Snort 3. Let me organize your working setup and provide some enhancements.

## Your Current Snort 3 Setup - Complete Configuration

### ✅ What You've Successfully Installed:
- **Snort 3.1.74.0** - Latest version
- **All dependencies** (Hyperscan, FlatBuffers, DAQ, etc.)
- **Rule management** with PulledPork3
- **Network configuration** with LRO/GRO disabled
- **Working ruleset** with malware detection capabilities

## Organized Configuration Files

### Current snort.lua Configuration:
```lua
-- /usr/local/etc/snort/snort.lua
HOME_NET = 'any'

alert = 
{
    modules = 
    {
        log_pkts = true
    }
}

ips =
{
    mode = tap,
    variables = default_variables
}

include = "/usr/local/etc/rules/local.rules",
include = "/usr/local/etc/rules/pulledpork.rules",
```

### Enhanced snort.lua Configuration:
```lua
-- /usr/local/etc/snort/snort.lua (Enhanced version)
HOME_NET = '192.168.1.0/24'  -- Adjust to your network
EXTERNAL_NET = 'any'

alert = 
{
    modules = 
    {
        log_pkts = true
    }
}

ips =
{
    mode = tap,
    variables = default_variables
}

-- Rule includes
include = "/usr/local/etc/rules/local.rules"
include = "/usr/local/etc/rules/pulledpork.rules"

-- Performance tuning
profiler = { }
perf_monitor = { }
```

## Working Commands Summary

### Basic Snort Operations:
```bash
# Test configuration
snort -c /usr/local/etc/snort/snort.lua --warn-all

# Run on live traffic
snort -c /usr/local/etc/snort/snort.lua -i ens33 -A alert_fast

# Analyze PCAP file
snort -c /usr/local/etc/snort/snort.lua --plugin-path "/usr/local/etc/so_rules/" -r file.pcap -A alert_fast -q

# Daemon mode (for production)
snort -c /usr/local/etc/snort/snort.lua -i ens33 -D
```

### Your Malware Analysis Command (Improved):
```bash
# Analyze malware traffic
snort -c /usr/local/etc/snort/snort.lua --plugin-path "/usr/local/etc/so_rules/" \
  -r 2022-02-23-traffic-analysis-exercise.pcap -A alert_fast -q > results.txt

# Analyze results
cat results.txt | cut -d "]" -f 3 | cut -d "[" -f 1 | cut -d '"' -f 2 | sort | uniq -c | sort -nr

# Search for specific threats
grep -i "malware\|trojan\|virus\|exploit" results.txt
```

## Enhanced Rule Management

### Improved PulledPork3 Configuration:
```bash
# /usr/local/etc/pulledpork3/pulledpork.conf (enhanced)
registered_ruleset = true
oinkcode = xxxxx  # Your actual oinkcode
snort_path = /usr/local/bin/snort
local_rules = /usr/local/etc/rules/local.rules
rule_url = https://snort.org/rules/snortrules-snapshot-31470.tar.gz

# Additional settings
distro = Ubuntu-20-4
config_path = /usr/local/etc/snort/snort.lua
sid_msg = /usr/local/etc/snort/sid-msg.map
sid_msg_version = 2
```

### Automated Rule Updates (Cron Job):
```bash
# Create daily rule update script
sudo nano /usr/local/bin/update_snort_rules.sh
```

```bash
#!/bin/bash
# /usr/local/bin/update_snort_rules.sh
/usr/local/bin/pulledpork3/pulledpork.py -c /usr/local/etc/pulledpork3/pulledpork.conf
systemctl reload snort  # If running as service
```

```bash
sudo chmod +x /usr/local/bin/update_snort_rules.sh
# Add to crontab for daily updates at 2 AM
echo "0 2 * * * root /usr/local/bin/update_snort_rules.sh" | sudo tee -a /etc/crontab
```

## Production Service Setup

### Create Systemd Service:
```bash
sudo nano /etc/systemd/system/snort3.service
```

```ini
[Unit]
Description=Snort 3 IDS/IPS
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/snort -c /usr/local/etc/snort/snort.lua -i ens33 -A alert_fast
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable snort3
sudo systemctl start snort3
```

## Advanced Monitoring & Logging

### Real-time Alert Monitoring:
```bash
# Follow alerts in real-time
tail -f /var/log/snort/alert_fast.txt

# Comprehensive logging setup
sudo mkdir -p /var/log/snort/{alerts,archived,stats}
```

### Enhanced Local Rules:
```bash
# /usr/local/etc/rules/local.rules (enhanced)
alert icmp any any -> any any (msg:"ICMP Ping Detected"; sid:1000001;)
alert tcp any any -> any 22 (msg:"SSH Connection Attempt"; sid:1000002;)
alert tcp any any -> any 80 (msg:"HTTP Traffic"; sid:1000003;)
alert tcp any any -> any 443 (msg:"HTTPS Traffic"; sid:1000004;)
alert udp any any -> any 53 (msg:"DNS Query"; sid:1000005;)
```

## Performance Optimization

### Memory and CPU Optimization:
```bash
# Check Snort performance
snort -c /usr/local/etc/snort/snort.lua --mem-check

# Optimize based on your hardware
# Add to snort.lua:
max_pdu = 1460
max_rule_metadata = 32768
```

## Security Hardening

### File Permissions:
```bash
sudo chown -R root:root /usr/local/etc/snort/
sudo chmod -R 644 /usr/local/etc/snort/
sudo chmod 755 /usr/local/etc/snort/

sudo chown -R root:root /var/log/snort/
sudo chmod -R 644 /var/log/snort/
```

## Testing Your Setup

### Comprehensive Test Suite:
```bash
# 1. Configuration test
snort -c /usr/local/etc/snort/snort.lua --warn-all

# 2. Rule loading test
snort -c /usr/local/etc/snort/snort.lua --plugin-path "/usr/local/etc/so_rules/" --rule-test

# 3. Live traffic test (30 seconds)
timeout 30s snort -c /usr/local/etc/snort/snort.lua -i ens33 -A alert_fast

# 4. PCAP analysis test
snort -c /usr/local/etc/snort/snort.lua -r test.pcap -A alert_fast -q
```

## Next Steps for Your IDS

1. **Integrate with SIEM** (ELK Stack, Splunk)
2. **Set up automated responses** (block IPs with iptables)
3. **Implement log rotation** and long-term storage
4. **Create custom rules** for your specific environment
5. **Set up alert notifications** (email, Slack, etc.)

Your Snort 3 installation is now fully operational! The malware traffic analysis exercise demonstrates that your IDS is correctly detecting threats. You have a production-ready intrusion detection system.

