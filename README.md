# Honeypot Threat Analysis Project

## Overview
Deployed a cloud-based honeypot using T-Pot on Oracle Cloud Infrastructure to capture 
and analyse real-world attack data targeting internet-facing systems.

## Tools Used
- **T-Pot 24.04** — Honeypot framework (Cowrie, Ciscoasa, Dionaea)
- **Oracle Cloud Infrastructure** — Cloud hosting
- **Kibana / Elasticsearch** — Log analysis and visualisation
- **Ubuntu 22.04** — Host OS

## Key Findings (3 Day Observation Period)
- 🔴 **74,000+** attack attempts recorded
- 🌍 Top attacking countries: Russia, USA, Bulgaria, Germany, China
- 🔑 Most targeted credentials: root/123456, admin/password
- 🤖 2,209 attacking IPs already on threat intelligence blacklists
- 💻 Post-exploitation scripts targeting cryptomining detected

## Screenshots
See the `/screenshots` folder for Kibana dashboard captures.

## Full Report
See `honeypot_analysis_report.md` for the complete threat analysis report.
