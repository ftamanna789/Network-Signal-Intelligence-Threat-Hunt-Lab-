# Final SOC Investigation Summary — Network Signal Intelligence Threat Hunt Lab

## Executive summary
This is a defensive educational portfolio project combining two DNS investigations, an HTTP training capture, and an HTTPS/TLS training capture. The research question was how to separate observable protocol anomalies from substantiated malicious activity while preserving reproducible evidence.

**Overall outcome:** Scoped investigations have been documented. No investigated evidence establishes a confirmed intrusion. The results must **not** be interpreted as proof that any real network is secure.

## Case studies

| Case study | Data | Evidence-based outcome | Report |
| --- | --- | --- | --- |
| 01 — DNS TXT/MX | Public `dns.cap` training capture | DNS requests, records, and responses were investigated | [DNS TXT/MX](investigation-report.md) |
| 02 — Live DNS | Authorised local capture; sensitive captures excluded from public repo | DNS requests/responses, timing, NXDOMAIN and TXT-related checks documented with limitations | [Live DNS](live-network-dns-investigation.md) |
| 03 — HTTP | Public `http.cap` (43 packets) | Two HTTP GET exchanges, 200 OK responses, and TCP reassembly documented; four evidence screenshots in GitHub | [HTTP](http-traffic-investigation.md) |
| 04 — HTTPS/TLS | Public `dump.pcapng` (1,095 packets) | 73 distinct localhost TLS 1.2 test conversations with different negotiated cipher suites; legacy certificate/cipher examples assessed | [TLS](https-tls-handshake-investigation.md) |

## Threat-hunting hypotheses and conclusions
1. **Potentially suspicious DNS behavior:** The scoped DNS exercises included record-type and response checks. No claim of confirmed tunnelling is supported by the evidence documented so far.
2. **Web request anomalies:** An advertising-related GET appears in `http.cap`. It is an ordinary-looking resource request in a historical sample; it is **not** proof of compromise.
3. **Repeated TLS connections / possible beaconing:** The `dump.pcapng` sample contains 73 localhost conversations of 15 packets each, selecting distinct cipher suites. This pattern is consistent with scripted TLS compatibility testing, not evidence of public internet C2 beaconing.
4. **Weak encryption configuration:** The historical TLS test explicitly negotiates some obsolete ciphers and uses test certificates that expired in September 2023 and contain SHA-1 signatures. These are valid educational security observations, **not evidence that the analyst's production systems are affected**.

## Reproducibility and preservation
- Use authorised training captures only; do not publish personal live traffic, credentials or raw sensitive packets.
- `http.cap` has 43 frames; inspect frames 4, 18, 27 and 38.
- `dump.pcapng` has SHA-256 `cfda7dab293023a3136b2eb3fd8ef375c8e2e060c2eb55c6501e4607cceb494c`; inspect frames 4, 6, 8, 9, 10, 11 and 14, and server-selected suites across the other handshakes.
- HTTP report includes four screenshots. TLS screenshots are available in the conversation but still require a GitHub image upload if visual evidence is desired.
- Results are bounded by these specific historic or local datasets. No general production network pentest, continuous monitoring, or comprehensive enterprise threat assessment was performed.

## SOC analyst workflow demonstrated
Capture triage → protocol display filters → packet detail review → request/response correlation → TLS certificate and cipher analysis → hypothesis testing and benign alternative explanation → findings, limitations, and actionable recommendations.

## Recommendations / portfolio next improvements
1. Add the two existing TLS screenshots to the evidence folder and embed them in the TLS report.
2. If desired, extend this lab with a **separate**, authorised sample that includes a true known attack label to demonstrate detection-versus-benign discrimination.
3. Keep all documented conclusions tied to observable packets and avoid claiming malicious behavior where only protocol test traffic is visible.

**Project milestone:** The original DNS/HTTP/TLS training-analysis case studies and a cross-case final written report are complete. Additional practical exercises and TLS screenshot publication are optional future enhancements, not prerequisites to understanding the present case studies.
