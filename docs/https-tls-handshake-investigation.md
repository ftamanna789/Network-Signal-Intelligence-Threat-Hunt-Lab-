# HTTPS/TLS Handshake Investigation — Training Capture

## Scope
**Dataset:** Wireshark SampleCaptures `dump.pcapng` (a TLS 1.2 OpenSSL test capture). This report documents the **handshake and traffic patterns visible in screenshots shared during the exercise**. The `dump.pcapng` bytes were not uploaded into this workspace, so cipher-suite identifiers, certificate contents, whole-capture stream breakdown, and key-log decryption have **not** been independently validated. <https://wiki.wireshark.org/SampleCaptures>

## Evidence observed in Wireshark screenshots

| Observation | Evidence |
|---|---|
| Total captured packets | 1,095 |
| Packets displayed with `tls` filter | 511 (46.7%) |
| Source/destination (visible example) | `127.0.0.1` → `127.0.0.1` (loopback) |
| TLS example session | TCP source port `38964`, destination port `4430` |
| TLS Client Hello | Frame 4, handshake version **TLS 1.2 (0x0303)** |
| Offered cipher suites | **2**; identifiers not expanded in provided screenshot |
| Compression methods | **2** entries, not individually inspected |
| Extension examples | `ec_point_formats`, `supported_groups`, `session_ticket`, `signature_algorithms` |
| Response | Frame 6 shows Server Hello, Certificate and Server Key Exchange |
| Client key establishment | Frame 8 shows Client Key Exchange and Change Cipher Spec |
| Server follow-up | Frame 9 shows New Session Ticket and Change Cipher Spec |
| Post-handshake traffic | Frames 10–11: Application Data; frame 14: Encrypted Alert |
| Further handshakes | Frames 19, 21, 23, 24 and later show repeated TLS sessions |

**Important parsing detail:** The record-layer field `Version: TLS 1.0 (0x0301)` appears in the Client Hello record, but the **Client Hello handshake version** is `TLS 1.2 (0x0303)`. The former is a compatibility record-layer value; it is not proof that the negotiated session is TLS 1.0. The selected Server Hello value should be inspected to establish negotiated version and cipher.

## Analysis and security interpretation
1. The sequence Client Hello → Server Hello/Certificate/Key Exchange → Client Key Exchange/Change Cipher Spec → Application Data is consistent with a TLS handshake and subsequent encrypted traffic.
2. Both endpoints in the observed packets are `127.0.0.1`, indicating a **loopback test** rather than traffic sent to a public website. Port `4430` is a non-default destination port for this sample, not evidence of malicious activity by itself.
3. Application Data is labelled but its HTTP payload cannot be read from these screenshots alone. The sample's separately provided `premaster.txt` key log can support authorised decryption if configured, but decryption has **not** been performed here.
4. Certificate details (issuer, validity, subject, verification), server-selected cipher suite, cryptographic strength, and any potential vulnerabilities remain **unverified**. A visible Certificate handshake message does not prove certificate trust.
5. There is **no demonstrated compromise or malicious indicator** in the inspected screenshots. Equally, a small visibility-limited inspection is not proof of safety.

## Outcome
**Scoped TLS handshake inspection: documented.** The available screenshots establish basic protocol flow, TLS 1.2 in the Client Hello, loopback source/destination, a TLS-filter packet count, and encrypted application-data records. This is **not** a completed certificate audit, cipher-strength assessment, decryption exercise, or full threat hunt.

## Evidence inventory and remaining work
- Wireshark screenshot: `tls` filter showing 511 / 1,095 packets, frames 4/6/8/9/10/11.
- Wireshark screenshot: expanded frame-4 TLS Client Hello showing TLS 1.2, two offered suites, extensions.
- The two screenshots were supplied in the chat but have **not** been committed to this repository; no image links are embedded to avoid broken links.
- Optional deeper verification: upload the public `dump.pcapng` file for direct packet analysis; inspect frame 6 for the selected cipher and certificate properties; inspect complete sessions and optionally apply an authorised TLS key log.

## Recommendations
- For modern deployments use current TLS versions (TLS 1.2 with strong suites or TLS 1.3), trustworthy server certificates, and endpoint logs for visibility into encrypted application behaviour.
- Treat loopback test captures as protocol examples, not evidence of threats on the user's network.
