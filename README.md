# Network Signal Intelligence Threat Hunt Lab

**Network Traffic Analysis · Threat Hunting · DNS/HTTP Investigation · SOC Portfolio**

> **Status:** Active portfolio project. Two DNS investigations, an HTTP training-capture investigation, a complete offline TLS training-capture analysis, and a final SOC case-study summary are documented.

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

## Repository Structure

```text
.
├── README.md
├── docs/
│   ├── methodology.md
│   ├── investigation-report.md
│   ├── live-network-dns-investigation.md
│   ├── http-traffic-investigation.md
│   ├── https-tls-handshake-investigation.md
│   └── final-soc-investigation-summary.md
├── evidence/
│   ├── README.md
│   ├── live-dns/
│   └── http-https-analysis/
└── data/
    └── README.md
```

Raw packet captures and extracted sensitive data will not be published without checking the dataset's usage permissions and privacy implications.

## Findings

**Case Study 01 — Training Capture:** [DNS TXT and MX Investigation](docs/investigation-report.md) covers the `dns.cap` capture, request/response analysis, SPF and MX records, with six embedded evidence screenshots in `evidence/`.

**Case Study 02 — Live Traffic:** [Live Network DNS Traffic Investigation](docs/live-network-dns-investigation.md) covers authorised Wi-Fi capture, response timings, NXDOMAIN and TXT query checks, and carefully scoped conclusions. The original live capture and identifiable screenshots are kept private.

**Case Study 03 — HTTP Training Capture:** [HTTP Traffic Investigation](docs/http-traffic-investigation.md) documents 43 captured packets, two GET requests, response correlation including TCP reassembly, and security observations. Screenshot evidence is pending addition to `evidence/http-https-analysis/`.

**Case Study 04 — TLS Training Capture:** [HTTPS/TLS Investigation](docs/https-tls-handshake-investigation.md) directly analyses 1,095 captured packets across 73 localhost sessions, negotiated ciphers, X.509 certificates and relevant security limitations.

**Final cross-case report:** [SOC Investigation Summary](docs/final-soc-investigation-summary.md) consolidates the four scoped case studies, threat-hunting hypotheses, findings and recommendations.

## Roadmap

- [x] Create GitHub repository
- [x] Publish initial README
- [x] Set up Wireshark on macOS (Apple Silicon)
- [x] Select and document a legal training PCAP
- [x] Complete initial DNS TXT/MX analysis
- [x] Complete initial HTTP request/response analysis and publish case report
- [x] Publish training PCAP case report with screenshots
- [x] Publish privacy-safe live network DNS case report
- [x] Embed four HTTP evidence screenshots in the HTTP case report
- [x] Verify TLS negotiated cipher suites and test certificates from the supplied PCAP
- [x] Complete scoped traffic-pattern threat-hunting assessment and final SOC summary
- [x] Upload and embed two TLS screenshots in the TLS report
- [ ] Optional: extend to a labelled malicious training capture for attack detection practice
- [ ] Summarise verified findings for the portfolio

## Ethical Use

This repository is for defensive education and authorised analysis only. Use traffic that you own, have permission to inspect, or that is provided for training. Never publish private traffic, credentials, or personal data.

## Author

[ftamanna789](https://github.com/ftamanna789)
