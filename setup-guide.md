# 🔧 Lab Setup Guide

Build time: ~3–4 hours | Cost: $0 (all free/trial software)

## 1. VirtualBox networking

Create a **host-only network** (`vboxnet0`, 192.168.56.0/24) so lab traffic never touches your real LAN:

| VM | OS | IP | RAM |
|----|----|----|-----|
| `kali-attacker` | Kali Linux | 192.168.56.10 | 2 GB |
| `win10-victim` | Windows 10 | 192.168.56.20 | 4 GB |
| `ubuntu-siem` | Ubuntu 22.04 | 192.168.56.30 | 4 GB |

## 2. Splunk SIEM (ubuntu-siem)

```bash
# Download Splunk Enterprise (free 60-day trial / 500MB/day free license)
wget -O splunk.deb "<download-url-from-splunk.com>"
sudo dpkg -i splunk.deb
sudo /opt/splunk/bin/splunk start --accept-license
# Create admin user when prompted, enable receiver on port 9997:
sudo /opt/splunk/bin/splunk enable listen 9997 -auth admin:changeme
```

## 3. Log forwarding (win10-victim)

1. Install **Sysmon** with the SwiftOnSecurity config for rich process telemetry:
   ```
   Sysmon64.exe -accepteula -i sysmonconfig-export.xml
   ```
2. Install the **Splunk Universal Forwarder** on Windows and forward `WinEventLog://Security` plus `XmlWinEventLog://Microsoft-Windows-Sysmon/Operational` to 192.168.56.30:9997.

## 4. Atomic Red Team (kali-attacker)

```bash
# Atomic Red Team on Linux via the Go runner:
wget https://github.com/redcanaryco/invoke-atomicredteam/releases/latest/download/atomic-redteam-linux.zip
unzip atomic-redteam-linux.zip && cd atomic-redteam
./atomic-redteam --help
```

## 5. Run your first simulation → detection loop

```bash
# 1. On Kali: brute-force simulation (T1110)
./atomic-redteam execute -t T1110 -e 1

# 2. On Splunk: paste detections/brute_force.spl into a search
# 3. Watch the alert fire, then follow the IR playbook workflow
```

## 6. Snapshots are your friend

Take a VirtualBox snapshot of each VM **before** running simulations — one click to reset the range after every exercise.
