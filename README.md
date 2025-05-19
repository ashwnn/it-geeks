# IT Geeks - Simulating SEIM w/ Wazuh

## Overview

This scenario simulates a small business, **IT Geeks**, with a lightweight Active Directory–backed network secured and monitored by Wazuh. The environment consists of:

* **Controller Server**: Windows Server 2025 running both AD Domain Services and the Wazuh Manager
* **Three Employee Workstations**: Windows 10/11 hosts with varying technical literacy
* **Vulnerable Web Server**: Ubuntu host running OWASP Juice Shop

All endpoints run the Wazuh Agent (plus XDR on certain machines) to feed logs and alerts back to the manager. Day‑to‑day usage is simulated via scheduled PowerShell/Bash scripts, and attacks are executed using Atomic Red Team on workstations and BlackArch tools against the web server.

> For full setup and configuration steps see [docs/installation.md](docs/installation.md) and [docs/monitoring.md](docs/monitoring.md).

---

## Environment

**Simulation Timeline**: \~3–6 days of traffic and attack activity, with ongoing analysis.

### Machines

**Controller Server** (`ITG-DC01`)

* **OS**: Windows Server 2025
* **Roles**:
  * Active Directory Domain Services (Domain: `itgeeks.ca`)
  * DNS, DHCP
  * Wazuh Manager
* **Specs**: 5 vCPU, 8 GB RAM, 50 GB Disk
* **Network**: Static IP `10.10.0.10/24`

**Employee Workstations**

| Host Name   | Employee     | Literacy     | OS             | Role                                             | Static IP       |
| ----------- | ------------ | ------------ | -------------- | ------------------------------------------------ | --------------- |
| ITG-ALGO01  | Al Gorithm   | Advanced     | Windows 11 Pro | Admin: AD, patching, Wazuh tuning                | `10.10.0.20/24` |
| IT-JSON01   | Json Bourne  | Intermediate | Windows 10 Pro | Dev/Sysadmin: scripting, Wazuh Agent             | `10.10.0.30/24` |
| ITG-ARRAY01 | Array Potter | Beginner     | Windows 10 Pro | Accountant: Office/email; triggers benign alerts | `10.10.0.40/24` |
| ITG-JUICE01 | —            | —            | Ubuntu 24.04   | OWASP Juice Shop; Wazuh Agent                    | `10.10.0.69/24` |

> See [docs/environment.md](docs/environment.md) for network diagrams and detailed host provisioning scripts.

**Vulnerable Web Server**

* **Hostname**: `ITG-JUICE01`
* **App**: OWASP Juice Shop (vulnerable version)
* **Purpose**: Practice detection of SQLi, XSS, insecure dependencies
* **Wazuh Agent Modules**:
  * FIM on `/opt/juice-shop`
  * Web server logs (NGINX/Apache)
  * Node.js process monitoring
  * Custom Hydra scripts

> Detailed hardening & log‑parser configs in [docs/monitoring.md](docs/monitoring.md).

**Attacker Machine**

* **Hostname**: `BLACKARCH-02`
* **OS**: BlackArch Linux (rolling)
* **Tools**: `nmap`, `sqlmap`, `wpscant`, `Metasploit`, custom Hydra
* **Objectives**:

  0. Recon via port/web scans
  1. Exploit Juice Shop flaws
  2. Escalate to shell and pivot laterally

> Detailed attack in [docs/attacks.md](docs/attacks.md).

---

### Employee Profiles

1. **Al Gorithm**

   * **Role**: Founder & Lead Technician
   * **Skills**: Advanced Windows administration, PowerShell, basic Linux
   * **Usage**: Daily AD management, patching, Wazuh rule tuning

2. **JSON Bourne**

   * **Role**: Systems Administrator & Junior Dev
   * **Skills**: Bash/Python scripting, moderate AD work
   * **Usage**: Automated maintenance, user provisioning

3. **Array Potter**

   * **Role**: Accountant
   * **Skills**: Beginner; follows guides
   * **Usage**: Office/email; often triggers benign alerts

## Configuration

1. **Wazuh Agent**
   * Windows via MSI; Linux via script; all pointed at `10.10.0.10:1514`.
2. **Rule Tuning**
   
3. **Alerts & Dashboards**

   * Panels for auth events, FIM changes, web exploit signatures
   * Email notifications for high‑severity matches

> Configuration snippets in [docs/configuration.md](docs/configuration.md).

---

## Simulation

* **Windows (`.ps1`)**
  * Simulate logins/logoffs, updates, Office usage, USB insert/remove, browsing suspicious sites, and software installation
  * Scheduled randomly via Task Scheduler
* **Linux (Juice Shop)**
  * Only log rotation and basic sysadmin tasks to keep attacker traffic clear

> Scripts located under `scripts/`; see `scripts/README.md` for details.

---

## Attack

1. **Reconnaissance**
   * Host discovery & port scanning
2. **Vulnerability Analysis**
   * OWASP ZAP, Nikto scans
3. **Exploitation**
   * SQL Injection on login
   * Command injection via REST API
4. **Post‑Exploitation**
   * Shell on `ITG-JUICE01`, SSH pivot to other machines on the network and/or lateral movement
5. **Detection & Response**
   * Anomalous process, File Integrity Monitoring, reverse shell alerts
   * Investigation in Kibana; host quarantine

---

## Documentation

* **Network Diagram**:
* **Configuration Guides**:
  * Wazuh Manager & Agent install ([docs/installation.md](docs/installation.md))
  * AD setup on Windows Server 2025 ([docs/ad-setup.md](docs/ad-setup.md))
  * Web Server on Ubuntu 24.04 ([docs/web-server.md](docs/web-server.md))
  * Wazuh rules & alerts ([docs/rules.md](docs/rules.md))

---

## Repository Structure

```
```

---

## License

This project is licensed under the [MIT License](LICENSE).

---

