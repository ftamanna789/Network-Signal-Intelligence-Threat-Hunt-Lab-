# HTTPS/TLS Investigation — Complete Offline Training-Capture Analysis

## Dataset and provenance
- Source: Wireshark SampleCaptures, public historical TLS test capture, `dump.pcapng`, supplied in this chat.
- **SHA-256:** `cfda7dab293023a3136b2eb3fd8ef375c8e2e060c2eb55c6501e4607cceb494c`
- **Captured packets:** 1,095. **Wireshark TLS display-filter matches:** 511 (46.7%), as independently shown by the provided screenshot.
- The uploaded capture was parsed offline to verify IPv4/TCP conversations, TLS record and handshake headers, cipher identifiers, and embedded X.509 certificates. No production network or real user was monitored.

## Packet and conversation analysis

| Indicator | Verified result |
| --- | --- |
| TCP conversations | **73** distinct 4-tuples (each exactly **15 packets**) |
| Network endpoints | Every IPv4 packet has source and destination **127.0.0.1** (loopback) |
| Server-side ports in test | 4430, 4431, 4432, 4433 |
| Server Hello records | **73**, all reporting TLS **1.2 (0x0303)** |
| Server-selected cipher identifiers | **73 different suites**, apparently a systematic cipher-compatibility test |
| Certificate handshake messages | **73** |
| TLS encrypted application-data records | Present in the capture |
| Demonstrated external communication | **None**; traffic is localhost only |

### Detailed handshake: Frames 4–14
1. **Frame 4 — Client Hello:** TLS handshake-version **1.2 (0x0303)**, two cipher IDs offered: **0xC030** and **0x00FF**. The second ID is the TLS_EMPTY_RENEGOTIATION_INFO_SCSV signalling value, **not** a deployable encryption cipher. Extensions include supported groups, EC point formats, session ticket, and signature algorithms. Its outer record-version field is **0x0301**, used for compatibility; this does **not** mean the handshake selected TLS 1.0.
2. **Frame 6 — Server Hello:** negotiated TLS **1.2** and selected **0xC030 = TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384**. The Server Hello reports compression method **1 (DEFLATE)**; TLS-level compression is a historical configuration and should be disabled in modern deployments. The same frame carries Certificate, Server Key Exchange, and Server Hello Done messages.
3. **Frame 8 — Client Key Exchange and Change Cipher Spec:** the client proceeds into protected traffic.
4. **Frame 9 — New Session Ticket and Change Cipher Spec:** server continues the handshake.
5. **Frames 10–11 — Application Data:** encrypted application records are visible, but their plaintext contents have **not** been decrypted or interpreted.
6. **Frame 14 — Encrypted Alert:** encrypted alert record, whose specific reason cannot be read from the screenshot or undeciphered payload.

### Certificate inspection (parsed directly from the uploaded PCAP)
The dataset contains four deliberately constructed test-certificate families:

| Subject | Number of certificate handshakes | Expiry (UTC) | Signature scheme |
| --- | ---: | --- | --- |
| CN=Test Certificate RSA | 37 | 13 Sep 2023, 21:51:10 | RSA with SHA-1 |
| CN=Test Certificate ECDSA | 16 | 13 Sep 2023, 21:51:10 | ECDSA with SHA-1 |
| CN=Test Certificate DSS | 12 | 13 Sep 2023, 21:51:10 | DSA with SHA-1 |
| CN=Test Certificate ECDH | 8 | 13 Sep 2023, 21:51:10 | RSA with SHA-1 |

**Security significance:** These are expired historical **test certificates**, with legacy SHA-1 signature algorithms. The first certificate is self-issued (subject and issuer both CN=Test Certificate RSA); this does not establish modern public trust. No claim is made that a real internet website is using these certificates.

### Cipher security assessment
The dataset selects one different cipher in each of 73 test conversations. The first is the AEAD suite `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384` (0xC030); others include outdated candidates such as `TLS_RSA_WITH_RC4_128_SHA` (0x0005), `TLS_RSA_WITH_3DES_EDE_CBC_SHA` (0x000A), and export-grade identifiers. **These are compatibility-test results, not a recommended TLS configuration.** The presence of legacy suites in a controlled sample demonstrates why modern services should restrict cipher policies.

## SOC threat-hunting assessment
**Hypothesis:** repeated connections to nonstandard ports or unusual TLS suites might indicate suspicious command-and-control traffic.

**Evidence against that interpretation:** all 73 conversations are on the loopback interface, each follows the same 15-packet test shape, and each negotiates a distinct suite. This is strongly consistent with a scripted cryptographic test harness rather than independent periodic outbound contacts. The sample does not substantiate internet beaconing, data exfiltration or external command-and-control.

**Findings:** historical test certificates and legacy cipher capabilities are present. They are **training examples**, not verified active vulnerabilities of the user's network. No malicious compromise is established from this capture.

## Evidence
- Chat screenshot: `tls` filter displaying 511/1,095 matches and Client Hello/Server Hello/Application Data.
- Chat screenshot: expanded frame-4 TLS Client Hello (version, cipher list count, extensions).
- Both images exist in the conversation, but **have not been committed to GitHub**. This report deliberately avoids broken image links.
- [Verified packet-by-packet evidence and capture hash](../evidence/http-https-analysis/tls-packet-evidence.md) is committed as a reproducible textual evidence record.
- Offline packet parsing used the supplied `dump.pcapng` and its SHA-256 above; no secrets or private live PCAP were uploaded to GitHub.

## Recommendations
1. For real systems, enforce TLS 1.2 with vetted AEAD suites or TLS 1.3; disable RC4, 3DES, export ciphers, legacy SHA-1 certificate signatures and TLS-level compression.
2. Use trusted, unexpired certificates and validate certificate chains and hostnames.
3. Monitor actual external endpoint telemetry before concluding that beaconing or exfiltration exists; localhost test data cannot establish that.
4. Reproduce test analyses using the public training PCAP, keeping private traffic and credentials out of the repository.

## Scope and conclusion
**TLS training-capture investigation and scoped threat-hunting assessment completed.** Full payload decryption, cryptographic implementation testing, and assessment of a production network are outside this lab's scope.
