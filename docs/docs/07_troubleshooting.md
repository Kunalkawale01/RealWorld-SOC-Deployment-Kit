## docs/07_troubleshooting.md

### Common problems & fixes

#### 1. VM cannot start (VT-x missing)

Enable virtualization in BIOS.

#### 2. Security Onion web UI not loading

```
sudo so-status
sudo so-restart
```

#### 3. No logs arriving

* Check agent configuration
* Ensure correct Manager IP

#### 4. Honeypot not sending logs

```
systemctl status cowrie
```

#### 5. Sensor not linked to Manager

Re-run setup:

```
sudo so-setup
```
