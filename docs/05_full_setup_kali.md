## docs/05_full_setup_kali.md

### Full Setup Guide for Kali Linux

This guide explains how to install each OS on the VirtualBox VMs created by the scripts.

### 1. Install Security Onion (Manager & Sensor)

1. Start the VM in VirtualBox.
2. When asked, select the **Security Onion ISO**.
3. Choose **Install Security Onion**.
4. Select:

   * Manager = first VM
   * Sensor = second VM
5. Configure network as `Host-Only` + `NAT`.
6. Set passwords + enable only safe default services.

After reboot:

```
sudo so-setup
```

Follow prompts to link Manager <-> Sensor.

---

### 2. Install Ubuntu Server (Linux Endpoint + Honeypot)

1. Start the VM.
2. Select Ubuntu ISO.
3. Choose minimal installation.
4. Create a user.
5. Reboot.

Install Wazuh agent:

```
sudo bash install_agents.sh
```

Install Cowrie honeypot:

```
sudo apt install cowrie -y
```

---

### 3. Install Windows (Optional Endpoint)

1. Start VM with Windows ISO.
2. Install normally.
3. Install Wazuh Windows agent.

---
