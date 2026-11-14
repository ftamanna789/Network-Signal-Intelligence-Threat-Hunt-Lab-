# HTTP Traffic Investigation — Wireshark Training Capture

## Scope and methodology
- Source: Wireshark SampleCaptures `http.cap`, supplied for this lab.
- Capture contains **43 packets**; HTTP display filter identifies **4 dissected HTTP packets** (frames 4, 18, 27 and 38). TCP reassembly is needed to interpret the responses.
- Offline analysis of a historical training trace, **not** live traffic and **not** an HTTPS/TLS examination.
- The HTTP sample has server dates in **May 2004**; addresses, browser fingerprint and domains belong to the training trace, not the analyst's current system.

## Findings

| Field | Webpage download | Advertising-related resource |
| --- | --- | --- |
| Request frame | 4 | 18 |
| Method/path | `GET /download.html` | `GET /pagead/ads?... ` |
| Host | `www.ethereal.com` | `pagead2.googlesyndication.com` |
| Source IP | `145.254.160.237` | `145.254.160.237` |
| Destination IP | `65.208.228.223` | `216.239.59.99` |
| Destination TCP port | 80 | 80 |
| Response | `HTTP/1.1 200 OK` | `HTTP/1.1 200 OK` |
| Response reassembly completed in | Frame 38 | Frame 27 |
| HTTP response headers first seen in | Frame 6 | Frame 26 |
| Content type | `text/html; charset=ISO-8859-1` | `text/html; charset=ISO-8859-1` |
| Body length reported in headers | 18,070 bytes | 1,272 bytes (gzip-encoded transfer representation) |
| Server header | `Apache` | `CAFE/1.0` |
| Time from request to complete response | ~3.936 s | ~0.971 s |

### Request–response interpretation
1. Frame 4 requests the Ethereal download page and identifies a referer of `http://www.ethereal.com/development.html`. The server returns an HTML document with a 200 success response, whose HTTP header begins in frame 6 and whose reassembled response is displayed at frame 38.
2. Frame 18 requests a `/pagead/ads` resource, with the original download page as its referer. It includes query parameters, including an advertising client identifier and formatting values. The 200 response begins at frame 26, reports `Content-Encoding: gzip`, and finishes reassembly at frame 27. **Frame 27 alone is the tail of TCP response data, not the start of an HTTP status line.**
3. Both exchanges use plaintext HTTP on TCP port 80, making request paths, headers and response content observable in the training trace. This observation illustrates exposure of unencrypted HTTP; it does not establish exploitation or compromise.

## Security assessment
- **Observed:** Ordinary webpage retrieval and an advertising-related HTTP request; no clearly malicious activity is demonstrated by these request/response pairs.
- **Limitations:** A 43-packet historic sample does not establish that an entire network is safe. No verified beaconing, tunnelling, exploit attempt, or TLS analysis is claimed.
- **Recommendations:** Prefer HTTPS/TLS for production web traffic; monitor destinations, repetitive request patterns, unexpected HTTP status codes, and sensitive information in unencrypted requests.

## Evidence
- Screenshots from Wireshark reviewed during the exercise: filtered HTTP list, frame 4 GET details, frame 38 response details, frame 18 GET details.
- These screenshots are **not yet committed** to this evidence folder. Do not upload sensitive live captures or raw PCAPs to the repository.
- For reproducibility, load the official training `http.cap` in Wireshark and apply the display filter `http`; inspect frames **4, 18, 27, and 38**. Expand reassembled HTTP in frames 27 and 38 to see response headers that start in earlier TCP segments.

## Next steps
Analyze an authorized HTTPS/TLS training capture (TLS handshake, SNI when present, versions, certificates where visible, and limitations of encrypted payload inspection), then assess connection periodicity and document results separately.
