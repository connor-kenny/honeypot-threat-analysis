# Honeypot Deployment & Threat Analysis Report

**Author:** Kenny Connor  
**Date:** April 2026  
**Classification:** Portfolio Project  

---

## Executive Summary

A cloud-based honeypot was deployed on Oracle Cloud Infrastructure to observe real-world attack patterns targeting internet-facing systems. Over a 3-day observation period (23–26 April 2026), the honeypot recorded over **74,000 attack attempts** from **multiple threat actors across 5 continents**. The majority of activity consisted of automated credential stuffing against SSH services, with a smaller proportion targeting network infrastructure vulnerabilities. This report details the methodology, key findings, and security recommendations derived from the collected data.

---

## Methodology

**Platform:** Oracle Cloud Infrastructure (UK South – London region)  
**Honeypot Framework:** T-Pot 24.04 (Telekom Security) — Standard/Hive installation  
**VM Specifications:** VM.Standard.E5.Flex, 1 OCPU, 12GB RAM, Ubuntu 22.04 LTS  
**Active Honeypots:** Cowrie (SSH), Ciscoasa, Dionaea, Honeytrap, and others  
**Analysis Tool:** Kibana / Elasticsearch (built into T-Pot)  
**Observation Period:** 3 days  

The VM was intentionally exposed to the public internet with no defensive filtering in order to collect authentic attack telemetry. All data was captured and analysed through the T-Pot Kibana dashboard.

---

## Key Findings

### 1. Attack Volume

| Metric | Value |
|---|---|
| Total attack attempts | 74,000+ |
| Primary honeypot targeted | Cowrie (SSH) |
| Secondary honeypot targeted | Ciscoasa |
| Observation period | 3 days |

The volume of attacks demonstrates the speed at which internet-facing systems are discovered and targeted by automated scanning tools — the honeypot began receiving attacks within minutes of going live.

### 2. Geographic Origin of Attacks

| Rank | Country | Notes |
|---|---|---|
| 1 | Russia | Highest volume, primarily via MTS PJSC (ASN 8359) |
| 2 | United States | Likely compromised infrastructure |
| 3 | Bulgaria | Consistent scanning activity |
| 4 | Germany | Mixed automated and targeted traffic |
| 5 | China | Consistent low-volume scanning |

The top autonomous system (AS) by attack volume was **MTS PJSC (Russia)** with 12,492 attempts, followed by **PVimpelCom** with 7,347 attempts — both Russian telecommunications providers commonly associated with botnet infrastructure.

Notably, **Oracle Corporation** appeared as the third largest source (5,813 attempts), indicating that compromised cloud VMs are actively being weaponised to attack other systems.

### 3. Most Targeted Ports

| Port | Protocol/Service | Attack Count |
|---|---|---|
| 3478 | STUN (VoIP/WebRTC) | Highest |
| 80 | HTTP | High |
| 22 | SSH | High |
| 443 | HTTPS | Medium |
| 587 | SMTP (Email) | Medium |

Port 3478 (STUN protocol) was the most targeted, commonly probed by attackers seeking misconfigured VoIP systems for toll fraud or DDoS amplification. Port 22 (SSH) was heavily targeted via the Cowrie honeypot.

### 4. Credential Stuffing Analysis (Cowrie SSH Honeypot)

**Most attempted usernames:**

| Rank | Username | Significance |
|---|---|---|
| 1 | root | Default superuser on Linux systems |
| 2 | admin | Common default on network devices |
| 3 | ubuntu | Default user on Ubuntu cloud instances |

**Most attempted passwords:**

| Rank | Password | Significance |
|---|---|---|
| 1 | 123456 | Most common weak password globally |
| 2 | LeitboGi0ro | Known default password for specific router models |
| 3 | password | Generic default credential |

The presence of `LeitboGi0ro` — a known default password for certain consumer routers — indicates attackers are using curated credential lists targeting specific device types, not just generic password spraying.

### 5. Attacker IP Reputation

| Category | Count |
|---|---|
| Known attacker | 2,209 |
| Mass scanner | 32 |
| Bot/Crawler | 10 |
| Tor exit node | 6 |
| Form spammer | 4 |

**2,209 attacking IPs** were already listed on threat intelligence blacklists, demonstrating the value of threat intelligence feeds in a SOC environment — the majority of threats could have been blocked proactively using existing IOC databases.

The presence of **6 Tor exit nodes** indicates a subset of attackers were actively attempting to anonymise their activity, suggesting more deliberate and sophisticated targeting behaviour.

### 6. Post-Exploitation Commands (Cowrie)

Attackers who successfully authenticated to the Cowrie honeypot (believing it to be a real server) attempted the following commands:

| Command | Purpose |
|---|---|
| `python3 /tmp/bendi.py` | Execute malicious Python payload |
| `cat /tmp/bendi.py \| python` | Alternative payload execution method |
| `command -v apt-get` | Check for package manager (assess OS) |

This post-exploitation behaviour is consistent with **cryptojacking or botnet recruitment** — attackers were attempting to run scripts likely designed to install cryptocurrency mining software or establish persistence on the compromised host.

---

## Threat Analysis

The data collected indicates the majority of attack activity originates from **automated botnets** rather than human-operated campaigns. Key indicators include:

- High-volume, low-variation credential attempts (same passwords repeated across thousands of IPs)
- Attacks originating from known botnet infrastructure (MTS PJSC, PVimpelCom)
- Consistent targeting of default credentials and well-known vulnerable services
- Post-exploitation scripts pre-staged in `/tmp/` directory indicating pre-prepared attack tooling

The targeting of port 3478 (STUN) alongside SSH suggests a multi-vector automated campaign designed to identify and exploit any available attack surface on internet-facing systems.

---

## Recommendations

Based on the observed attack patterns, the following security controls would significantly reduce risk exposure:

1. **Disable root SSH login** — the most targeted username; enforce key-based authentication only
2. **Implement fail2ban or similar** — automatically block IPs after repeated failed authentication attempts
3. **Change default SSH port** — reduces automated scanning noise (though not a security control in isolation)
4. **Subscribe to threat intelligence feeds** — 2,209 attacking IPs were already known bad actors; blocking these proactively would have prevented the majority of attempts
5. **Patch VoIP/STUN services** — the most targeted port; ensure services are not publicly exposed unless required
6. **Monitor for cryptomining indicators** — unusual CPU usage, outbound connections to mining pools, and presence of Python scripts in `/tmp/` are key indicators of compromise

---

## Conclusion

This honeypot deployment provided direct, hands-on exposure to real-world attack telemetry in a controlled environment. Over just 3 days, 74,000+ attack attempts were recorded, demonstrating the persistent and automated nature of threats targeting internet-facing infrastructure. Analysis of the data provided practical experience with SIEM tooling (Kibana/Elasticsearch), threat intelligence concepts, credential stuffing patterns, and post-exploitation behaviour — all directly relevant to SOC Analyst operations.

---

*This report was produced as part of an independent cybersecurity home lab project. All data was collected in a controlled honeypot environment with no sensitive systems at risk.*
