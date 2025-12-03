# RealWorld-SOC-Deployment-Kit (Simplified & Beginner-Friendly)

A clean, professional, beginner-friendly SOC project designed to be **easy to understand and easy to run on Kali Linux**.

This repository includes:

* Step-by-step commands
* Clear explanations
* Simple deployment flow
* Safe SOC tools only (Security Onion, Wazuh, Zeek, Suricata, Cowrie honeypot)
* No harmful or offensive instructions
* Beginner Quick Start + Full Setup Guide

---

# Repository Structure

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

---

# README.md (Contents)

## 1. What is this project?

A simplified SOC (Security Operations Center) environment you can run inside **VirtualBox on Kali Linux**.
It simulates a real monitoring environment using **safe test data**, not real attacks.

Tools included:

* **Security Onion** (Manager + Sensor)
* **Wazuh agent** (Linux + Windows)
* **Zeek** (Network visibility)
* **Suricata** (Alerting)
* **Cowrie Honeypot** (Low-interaction, safe)

---

## 2. Who is it for?

* Students
* Beginners
* SOC analysts
* Cybersecurity learners

No prior experience required.

---

## 3. Beginner Quick Start

Follow these steps on **Kali Linux**:

### Step 1 — Install VirtualBox

```
sudo apt update
sudo apt install virtualbox virtualbox-ext-pack -y
```

### Step 2 — Create project folder

```
mkdir soc-project
cd soc-project
```

### Step 3 — Download required ISO files (manual)

* Security Onion ISO
* Ubuntu Server ISO
* Windows ISO (optional)

### Step 4 — Run setup script

```
bash scripts/setup_virtualbox.sh
```

### Step 5 — Create VMs

```
bash scripts/create_vms.sh
```

### Step 6 — Install OS on each VM

Follow docs/05_full_setup_kali.md

### Step 7 — Install Agents

```
bash scripts/install_agents.sh
```

### Step 8 — Run safe event generator

```
bash scripts/run_safe_simulator.sh
```

### Step 9 — View alerts

Open browser:

```
http://<security-onion-ip>
```

---

## 4. Architecture Diagram (Simplified)

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

## 5. Safe Event Simulator

Generates harmless logs:

* login attempts
* file creation
* normal network beacons

Used for:

* Testing dashboards
* Testing log flow
* Beginner learning

---

## 6. Troubleshooting

Common fixes are documented in docs/07_troubleshooting.md

---
