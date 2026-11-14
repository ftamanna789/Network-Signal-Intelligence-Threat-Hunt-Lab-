# TLS Training-Capture Packet Evidence (Verified From PCAP)

Dataset: `dump.pcapng` (Wireshark public training capture), SHA-256 `cfda7dab293023a3136b2eb3fd8ef375c8e2e060c2eb55c6501e4607cceb494c`.

- Frame 4: `127.0.0.1:38964 -> 127.0.0.1:4430`. TLS 1.2 Client Hello, offered suite `0xC030` plus TLS_EMPTY_RENEGOTIATION_INFO_SCSV `0x00FF`.
- Frame 6: Server Hello TLS 1.2, selected `0xC030 TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`, compression method 1 (DEFLATE); Certificate, Server Key Exchange, Server Hello Done.
- Frame 6 certificate: Subject `CN=Test Certificate RSA`; issuer `CN=Test Certificate RSA`; expires 2023-09-13 21:51:10 UTC; RSA-with-SHA1 signature.
- Frame 8: Client Key Exchange, Change Cipher Spec.
- Frame 9: New Session Ticket, Change Cipher Spec.
- Frames 10–11: Encrypted Application Data.
- Frame 14: Encrypted Alert; plaintext meaning not determined.
- Whole-file totals: 1,095 packets; 73 TCP conversations, each 15 packets; 73 Server Hello and certificate messages; all IPv4 source/destination pairs `127.0.0.1`.
- Four certificate families: RSA (37), ECDSA (16), DSS (12), ECDH (8), all expiring on 2023-09-13 21:51:10 UTC.
- Some other conversations explicitly test obsolete legacy cipher IDs, including RC4 and 3DES. These are lab results, **not** a production TLS configuration assessment.
- Provided Wireshark screenshot showed display filter `tls`: **511 / 1,095** packets.
- Screenshots themselves are not currently present in the GitHub folder; this textual evidence is the reproducible backup.

See [full TLS investigation](../../../docs/https-tls-handshake-investigation.md).
