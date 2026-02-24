# Comparative Evaluation of Open-Source SOAR Tools with FortiGate and Honeypot Integration

Master's Thesis in Cybersecurity (EXD630, 15 HE credits)  
Högskolan Väst    
Students: Filmon Mehari Gebrezghi & Alphonse Joseph  
Supervisor: Robert Andersson  
Start year: 2025

## Overview

This project compares automated response performance of two open-source SOAR stacks:

- Shuffle (simple visual workflows)
- Wazuh + Cortex + TheHive (detection + enrichment + case management)

using identical real-world attack data from T-Pot honeypot sensors and FortiGate network alerts.

Goal: Provide benchmarks for budget-limited environments and FortiGate-integrated setups.

## Key elements:

- T-Pot honeypot (sensors: Cowrie, Dionaea, Glastopf, Honeytrap, Suricata)
- FortiGate NGFW (syslog/IPFIX export)
- 30 days real exposure + generated attacks (Nmap, Hydra, Metasploit)
- Identical logs/alerts forwarded to both SOAR tools via Filebeat/syslog
- Manual ground truth labeling (50–100 alerts/triggers per tool)
- Metrics: playbook success rate, response latency, false positive rate in workflows, resource use


## Tools & Links

T-Pot honeypot: https://github.com/telekom-security/tpotce  
FortiGate VM evaluation: https://www.fortinet.com/products/next-generation-firewall/fortigate-vm  
Shuffle SOAR: https://shuffler.io / https://github.com/Shuffle/Shuffle  
Wazuh: https://wazuh.com / https://documentation.wazuh.com  
Cortex (enrichment): https://cortex.thehive-project.org / https://github.com/TheHive-Project/Cortex  
TheHive (case management): https://thehive-project.org / https://github.com/TheHive-Project/TheHive  
Filebeat (log forwarding): https://www.elastic.co/beats/filebeat    

## Setup Instructions (Initial)

Create 4 Ubuntu 22.04 VMs (8 GB RAM, 4 cores, 200 GB disk each):
VM 1: T-Pot honeypot (internet-facing)
VM 2: FortiGate evaluation VM
VM 3: Shuffle SOAR
VM 4: Wazuh + Cortex + TheHive

Install T-Pot on VM 1: git clone https://github.com/telekom-security/tpotce  
cd tpotce  
./install.shActivate sensors in config: Cowrie, Dionaea, Glastopf, Honeytrap, Suricata.  
Deploy FortiGate VM (evaluation license from Fortinet site after FortiCare registration).  
Configure Filebeat on T-Pot (multiple outputs):textsudo filebeat modules enable cowrie dionaea glastopf suricata honeytrap  
sudo filebeat setup  
sudo systemctl restart filebeat  
Forward FortiGate syslog/IPFIX to both SOAR tools.  
Test locally: Run Nmap/Hydra from separate VM → check alerts in Shuffle and Wazuh/TheHive.  
Expose T-Pot (ufw open only sensor ports) for 30 days. Supplement with generated attacks if needed.  
 
## Safety: VM snapshots, no outbound from honeypot, daily log check.
Metrics Planned

Playbook success rate (% successful automated actions)
Response latency (seconds from alert to completion)
False positive rate in workflows
Resource use (average CPU/RAM via htop)

## Thesis Structure (IMRAD)

Introduction: background, problem, purpose, aims  
Methods: testbed, deployment, data flow, validation, metrics  
Results: tables/charts for success rate, latency, false positives  
Discussion: tool comparison, differences, limitations, recommendations  

## License

MIT License 


## Contact

Alphonse Joseph – alphonse.joseph@student.hv.se  
Filmon Mehari Gebrezghi – filmon-mehari.gebrezghi@student.hv.se  

## Data Flow

Logs from T-Pot and FortiGate feed both SOAR tools identically:

  ```mermaid
    flowchart TD
    A[Cowrie SSH/Telnet<br>Dionaea Malware/SMB<br>Glastopf HTTP/Web<br>Honeytrap probes<br>Suricata IDS] --> B[T-Pot Aggregation<br>/data folder logs]
    B --> C[Filebeat reads logs directly<br>Multiple outputs]
    A2[FortiGate NGFW<br>Syslog/IPFIX alerts] --> C2[FortiGate Export]
    C --> D[Shuffle SOAR<br>Playbooks / Workflows]
    C --> E[Wazuh + Cortex + TheHive<br>Detection + Enrichment + Case Mgmt]
    C2 --> D
    C2 --> E
    D --> F[Playbook Execution<br>Automated Response Metrics]
    E --> F
    

