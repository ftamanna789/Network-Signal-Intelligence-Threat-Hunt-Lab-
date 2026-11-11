# Investigation Methodology

## 1. Scope and provenance
- Capture filename:
- Source URL:
- Source licence / permissions:
- SHA-256:
- Capture period / timezone:
- Hypothesis:
- Known constraints:

## 2. Initial triage
Record: packet count, duration, protocol hierarchy, top conversations, top talkers, noteworthy endpoints.

## 3. DNS investigation
Apply `dns` and `dns.flags.response == 0`. Examine frequency, subdomain length, record types, NXDOMAIN rates, timing, and repeat destinations. Correlate with source hosts and subsequent connections.

## 4. HTTP investigation
Apply `http` and `http.request.method == "POST"`. Review request methods, hosts, paths, User-Agent strings, response codes and payload sizes when available. Encrypted HTTPS content normally requires authorised decryption or endpoint data.

## 5. Validate findings
Document packet numbers, timestamps, 5-tuples, screenshots, benign explanations and evidence quality. Avoid treating an anomaly as confirmed malware without corroboration.

## 6. Publish responsibly
Redact private identifiers, credentials and confidential payloads. Publish only where source terms allow it. Reference source and SHA-256 rather than committing unreviewed raw PCAPs.
