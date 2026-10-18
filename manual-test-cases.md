# Manual test cases

## URL validation
- `example.com` → accepted as a public domain.
- `https://example.com` → accepted and normalized to hostname.
- `http://example.com/path` → accepted; diagnostics currently operate on hostname only.
- Empty input, malformed URL, unsupported scheme → validation error.
- `https://user:pass@example.com` → rejected.
- `localhost`, `service.local`, `host.internal` → rejected.
- `127.0.0.1`, `192.168.1.1`, IPv6 literals → rejected.
- `example.com:8443` → rejected (custom ports unsupported).

## DNS and RDAP (requires deployed Worker)
- `example.com` → real resolver response; records and per-type query statuses reflect provider response.
- Query a record type with no answer → empty status, not a positive security finding.
- Nonexistent domain → NXDOMAIN or resolver response; never called secure.
- RDAP redirect → not supported; redirect is not followed.
- Provider timeout/failure → failed/not supported, never fabricated results.
- No API base → UI reports configuration limitation.

## Security boundary
- Requests to arbitrary target hosts are not implemented by the Worker.
- Do not expose a target-fetch endpoint until connection-time IP validation, DNS rebinding defense, redirect validation, response caps, timeouts and rate limiting are implemented and tested.

## UI/accessibility
- Narrow mobile viewport → no page-level horizontal overflow; tables scroll within wrapper.
- Keyboard-only → form, nav, tabs, tool shortcuts and export controls accessible.
- Reduced motion → network dash animation disabled and scrolling becomes immediate.
- Copy JSON and Export JSON → contain the current report only.
