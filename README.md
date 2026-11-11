# Network Signal Intelligence Threat Hunt Lab

**Network Traffic Analysis · Threat Hunting · DNS/HTTP Investigation · SOC Portfolio**

> **Status:** In development. This repository documents a planned, hands-on network threat-hunting lab. Findings will be added only after PCAPs have been analysed.

## Overview

This defensive cybersecurity project investigates network packet captures (PCAPs) with Wireshark to identify unusual network behaviour, suspicious DNS queries, HTTP requests, and potential indicators of compromise (IOCs). The goal is to practise a reproducible SOC analyst investigation workflow, from initial triage to evidence-based reporting.

## Objectives

- Inspect recorded network traffic and establish a baseline.
- Examine DNS queries and responses for unusual domains, TXT-record use, and patterns that may warrant investigation.
- Examine HTTP requests and responses for anomalous destinations, methods, headers, and possible exfiltration indicators.
- Correlate hosts, timestamps, conversations, and protocol evidence.
- Document findings, competing explanations, limitations, and practical defensive recommendations.

## Tools and Skills

| Area | Tools and techniques |
| --- | --- |
| Packet analysis | Wireshark, PCAP/PCAPNG |
| Protocol investigation | TCP/IP, DNS, HTTP |
| Threat hunting | Hypothesis-driven triage, IOC validation, traffic correlation |
| Reporting | Screenshots, packet references, reproducible notes |

## Investigation Workflow

1. **Acquire an authorised dataset.** Select a public, legal training PCAP and record its original source, licence/usage notes, filename, and SHA-256 hash.
2. **Triage the capture.** Review protocol hierarchy, conversations, endpoints, time span, and packet statistics.
3. **Investigate DNS.** Identify query volume, response codes, unusual domain patterns, and suspicious record types.
4. **Investigate HTTP.** Review host headers, URIs, methods, User-Agent values, status codes, and suspicious traffic sequences.
5. **Validate leads.** Correlate evidence across packets and explain benign alternatives; a single anomaly is not proof of compromise.
6. **Report.** Record evidence, confidence levels, suspected IOCs, limitations, and remediation recommendations.

## Useful Wireshark Display Filters

| Purpose | Display filter |
| --- | --- |
| DNS traffic | `dns` |
| DNS queries | `dns.flags.response == 0` |
| DNS TXT questions | `dns.qry.type == 16` |
| HTTP traffic | `http` |
| HTTP POST requests | `http.request.method == "POST"` |
| Traffic involving an example host | `ip.addr == 192.0.2.10` |
| TCP retransmissions | `tcp.analysis.retransmission` |

> Filters are starting points, not threat detections. For HTTPS traffic, HTTP request content generally cannot be inspected without authorised decryption or suitable endpoint logs.

## Repository Structure (Planned)

```text
.
├── README.md
├── docs/
│   ├── methodology.md
│   └── investigation-report.md
├── evidence/
│   └── README.md
└── data/
    └── README.md
```

Raw packet captures and extracted sensitive data will not be published without checking the dataset's usage permissions and privacy implications.

## Findings

**Not yet available.** A training PCAP has not been selected or analysed. Future updates will include evidence-backed observations with packet numbers, timestamps, screenshots, and analysis limitations.

## Roadmap

- [x] Create GitHub repository
- [x] Publish initial README
- [ ] Set up Wireshark on macOS (Apple Silicon)
- [ ] Select and document a legal training PCAP
- [ ] Complete DNS/HTTP traffic investigation
- [ ] Publish a SOC-style case report with evidence
- [ ] Summarise verified findings for the portfolio

## Ethical Use

This repository is for defensive education and authorised analysis only. Use traffic that you own, have permission to inspect, or that is provided for training. Never publish private traffic, credentials, or personal data.

## Author

[ftamanna789](https://github.com/ftamanna789)
