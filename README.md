# RealWorld-SOC-Deployment-Kit

A clean, professional, beginner-friendly SOC project designed to be **easy to understand and easy to run on Kali Linux**.

This repository includes:

* Step-by-step commands
* Clear explanations
* Simple deployment flow
* Safe SOC tools only (Security Onion, Wazuh, Zeek, Suricata, Cowrie honeypot)
* No harmful or offensive instructions
* Beginner Quick Start + Full Setup Guide

---

# 1. Repository Structure

```
RealWorld-SOC-Deployment-Kit/
│
├── README.md
├── docs/
│   ├── 01_overview.md
│   ├── 02_prerequisites.md
│   ├── 03_architecture_diagram.md
│   ├── 04_beginner_quickstart.md
│   ├── 05_full_setup_kali.md
│   ├── 06_safe_event_simulation.md
│   └── 07_troubleshooting.md
│
├── scripts/
│   ├── setup_virtualbox.sh
│   ├── create_vms.sh
│   ├── install_agents.sh
│   └── run_safe_simulator.sh
│
├── configs/
│   ├── wazuh_agent.conf
│   ├── suricata_rules_local.yaml
│   ├── zeek_local.zeek
│   └── cowrie.cfg
│
└── dashboards/
    ├── kibana_overview.json
    ├── wazuh_alerts.json
    └── network_security.json
```



## What is this project?

A simplified SOC (Security Operations Center) environment you can run inside **VirtualBox on Kali Linux**.
It simulates a real monitoring environment using **safe test data**, not real attacks.

Tools included:

* **Security Onion** (Manager + Sensor)
* **Wazuh agent** (Linux + Windows)
* **Zeek** (Network visibility)
* **Suricata** (Alerting)
* **Cowrie Honeypot** (Low-interaction, safe)

---

## Who is it for?

* Students
* Beginners
* SOC analysts
* Cybersecurity learners

No prior experience required.

## 2. Quick checklist (before you start)

- Host OS: Linux (Ubuntu/Debian recommended)
- RAM ≥ 32 GB recommended (16 GB minimum but the lab will be slow)
- CPU: 4+ cores (8+ recommended)
- Disk: ≥ 300 GB free (SSD recommended)
- VirtualBox installed (latest)
- VirtualBox Extension Pack (for host-only + USB if needed)
- Download the following ISOs:
- Security Onion ISO (official site)
- Windows 10/11 ISO (MS/your license)
- Ubuntu Server 22.04 LTS ISO (endpoints/honeypot)
- Unzip the project ZIP to a working directory (example ~/soc-lab/)
- If you don’t have one of these ISOs yet, download them now and place them in ```~/isos/```.


## 1. Setup: Unzip project and prepare directories

```
cd ~
mkdir -p ~/soc-lab
unzip /path/to/advanced-security-onion-soc-plus.zip -d ~/soc-lab/project
ls ~/soc-lab/project
```

You should now see folders like:

```
deploy/
configs/
simulation/
ansible/
```

## 2. Install VirtualBox (on Kali Linux)

```
sudo apt update
sudo apt install -y virtualbox virtualbox-ext-pack
```

Verify:

```
VBoxManage --version
```

## 3. Create VirtualBox networks

### Host-only network:

```
VBoxManage hostonlyif create
sudo VBoxManage hostonlyif ipconfig vboxnet0 --ip 192.168.56.1 --netmask 255.255.255.0
```

### Internal monitoring network:

No command needed. It is created when assigning NICs:

```
intnet "monitor-net"
```

## 4. Prepare virtual disks

```
mkdir -p ~/soc-lab/vms
VBoxManage createmedium disk --filename ~/soc-lab/vms/so-manager.vdi --size 200000
VBoxManage createmedium disk --filename ~/soc-lab/vms/so-sensor.vdi  --size 100000
VBoxManage createmedium disk --filename ~/soc-lab/vms/win-client.vdi --size 60000
VBoxManage createmedium disk --filename ~/soc-lab/vms/linux-server.vdi --size 40000
VBoxManage createmedium disk --filename ~/soc-lab/vms/honeypot.vdi --size 40000
```

## 5. Create Virtual Machines (CLI)

Below are all VM creation commands exactly as required.

## Security Onion Manager

```
VBoxManage createvm --name "SO-MANAGER" --register
VBoxManage modifyvm "SO-MANAGER" --memory 16384 --cpus 4 --ostype Ubuntu_64
VBoxManage storagectl "SO-MANAGER" --name "SATA" --add sata --controller IntelAhci
VBoxManage storageattach "SO-MANAGER" --storagectl "SATA" --port 0 --device 0 --type hdd --medium ~/soc-lab/vms/so-manager.vdi
VBoxManage storageattach "SO-MANAGER" --storagectl "SATA" --port 1 --device 0 --type dvddrive --medium ~/isos/securityonion.iso
VBoxManage modifyvm "SO-MANAGER" --nic1 hostonly --hostonlyadapter1 vboxnet0
VBoxManage modifyvm "SO-MANAGER" --nic2 intnet --intnet2 "monitor-net"
```

## Security Onion Sensor

```
VBoxManage createvm --name "SO-SENSOR" --register
VBoxManage modifyvm "SO-SENSOR" --memory 8192 --cpus 2 --ostype Ubuntu_64
VBoxManage storagectl "SO-SENSOR" --name "SATA" --add sata --controller IntelAhci
VBoxManage storageattach "SO-SENSOR" --storagectl "SATA" --port 0 --device 0 --type hdd --medium ~/soc-lab/vms/so-sensor.vdi
VBoxManage storageattach "SO-SENSOR" --storagectl "SATA" --port 1 --device 0 --type dvddrive --medium ~/isos/securityonion.iso
VBoxManage modifyvm "SO-SENSOR" --nic1 hostonly --hostonlyadapter1 vboxnet0
VBoxManage modifyvm "SO-SENSOR" --nic2 intnet --intnet2 "monitor-net"
```

## Windows Client

```
VBoxManage createvm --name "WIN-CLIENT" --register
VBoxManage modifyvm "WIN-CLIENT" --memory 8192 --cpus 2 --ostype Windows10_64
VBoxManage storagectl "WIN-CLIENT" --name "SATA" --add sata --controller IntelAhci
VBoxManage storageattach "WIN-CLIENT" --storagectl "SATA" --port 0 --device 0 --type hdd --medium ~/soc-lab/vms/win-client.vdi
VBoxManage storageattach "WIN-CLIENT" --storagectl "SATA" --port 1 --device 0 --type dvddrive --medium ~/isos/windows10.iso
VBoxManage modifyvm "WIN-CLIENT" --nic1 hostonly --hostonlyadapter1 vboxnet0
VBoxManage modifyvm "WIN-CLIENT" --nic2 intnet --intnet2 "monitor-net"
```

## Ubuntu Linux Server

```
VBoxManage createvm --name "LINUX-SERVER" --register
VBoxManage modifyvm "LINUX-SERVER" --memory 4096 --cpus 2 --ostype Ubuntu_64
VBoxManage storagectl "LINUX-SERVER" --name "SATA" --add sata --controller IntelAhci
VBoxManage storageattach "LINUX-SERVER" --storagectl "SATA" --port 0 --device 0 --type hdd --medium ~/soc-lab/vms/linux-server.vdi
VBoxManage storageattach "LINUX-SERVER" --storagectl "SATA" --port 1 --device 0 --type dvddrive --medium ~/isos/ubuntu-22.04.iso
VBoxManage modifyvm "LINUX-SERVER" --nic1 hostonly --hostonlyadapter1 vboxnet0
VBoxManage modifyvm "LINUX-SERVER" --nic2 intnet --intnet2 "monitor-net"
```

##  Honeypot (Cowrie)

```
VBoxManage createvm --name "HONEYPOT" --register
VBoxManage modifyvm "HONEYPOT" --memory 4096 --cpus 2 --ostype Ubuntu_64
VBoxManage storagectl "HONEYPOT" --name "SATA" --add sata --controller IntelAhci
VBoxManage storageattach "HONEYPOT" --storagectl "SATA" --port 0 --device 0 --type hdd --medium ~/soc-lab/vms/honeypot.vdi
VBoxManage storageattach "HONEYPOT" --storagectl "SATA" --port 1 --device 0 --type dvddrive --medium ~/isos/ubuntu-22.04.iso
VBoxManage modifyvm "HONEYPOT" --nic1 hostonly --hostonlyadapter1 vboxnet0
VBoxManage modifyvm "HONEYPOT" --nic2 intnet --intnet2 "monitor-net"
```

## 6. Install Operating Systems

Start each VM:

```
VBoxManage startvm "SO-MANAGER" --type gui
```

Repeat for all.

Install Security Onion / Ubuntu / Windows normally.

**Take snapshots after each OS install.**

## 7. Post‑install: Security Onion setup

### On SO-MANAGER

```
sudo so-setup
```

Choose:

* Manager + Search Node
* Set passwords

### On SO-SENSOR

```
sudo so-setup
```

Choose:

* Sensor Node
* Register using manager IP: **192.168.56.10**

Open SO dashboard:

```
http://192.168.56.10:5601
```

## 8. Configure Wazuh Agents

### Linux agent (manual):

```
sudo apt update
sudo apt install -y curl
# install agent from provided script
bash configs/wazuh/install_wazuh_agent.sh
```

### Windows agent:

Install MSI → point Manager IP = **192.168.56.10**

Install Sysmon:

```
.
Sysmon64.exe -accepteula -i sysmon-config.xml
```

## 9. Install Cowrie Honeypot

On HONEYPOT VM:

```
sudo apt update
sudo apt install -y git python3-virtualenv python3-dev libssl-dev libffi-dev build-essential
cd /opt
git clone https://github.com/cowrie/cowrie.git
cd cowrie
virtualenv -p python3 cowrie-env
source cowrie-env/bin/activate
pip install -r requirements.txt
bin/cowrie start
```
## 10. Deploy Suricata custom rules (sensor)

```
sudo cp configs/suricata/custom.rules /etc/suricata/rules/local.rules
sudo systemctl restart suricata
```

## 11. Deploy Zeek scripts (sensor)

```
sudo cp configs/zeek/long_dns.zeek /nsm/zeek/share/zeek/site/
sudo systemctl restart zeek
```

## 12. Import dashboards + rules

In Kibana → **Stack Management → Saved Objects → Import**
Upload dashboard JSON files.

Copy Wazuh custom rules:

```
sudo cp detections/waz

```

## Architecture Diagram (Simplified)

```
      [ Kali Linux Host ]
               │
      ┌──────────────────┐
      │ VirtualBox       │
      └──────────────────┘
               │
 ┌─────────────┼──────────────────────┐
 │             │                      │
[Sensor]   [Manager]         [Honeypot]
 Security     SOC UI            Cowrie
 Onion        Wazuh             Logs
 Suricata     Dashboards        Events
 Zeek
```

---

## Safe Event Simulator

Generates harmless logs:

* login attempts
* file creation
* normal network beacons

Used for:

* Testing dashboards
* Testing log flow
* Beginner learning

---

## Troubleshooting

Common fixes are documented in docs/07_troubleshooting.md

---
