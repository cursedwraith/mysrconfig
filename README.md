# mysrconfig

Iran split-routing profiles for Shadowrocket and v2rayN.

## Common behavior

- Iranian domains and Iranian destination networks: `DIRECT`
- Private and LAN traffic: `DIRECT`
- Everything else: `PROXY`
- No advertising, malware, phishing, rewrite, or content-blocking rules
- Provider nodes and subscriptions are not included

## Shadowrocket for iPhone/iPad

Configuration:

```text
https://raw.githubusercontent.com/cursedwraith/mysrconfig/main/iran.conf
```

The Shadowrocket profile is optimized around a simple rule: keep domestic Iranian traffic direct and proxy everything else.

### Routing order

1. LAN/private traffic -> `DIRECT`
2. `.ir` and Persian IDN TLD -> `DIRECT`
3. Iranian non-`.ir` domain database -> `DIRECT`
4. Iranian CIDR and ASN data -> `DIRECT`
5. Shadowrocket `GEOIP,IR` fallback -> `DIRECT`
6. Everything unmatched -> `PROXY`

IP-based Iran rules use `no-resolve`, so unknown foreign hostnames are not locally resolved merely to classify them.

### DNS

- Begzar is the primary resolver for direct/domestic traffic.
- Shecan and then the iOS/system resolver are fallbacks.
- Plain DNS on port 53 is intercepted by Shadowrocket with `hijack-dns = :53`.
- Proxy-bound hostnames are left to the proxy path rather than intentionally pre-resolving every foreign domain locally.

### Generated Shadowrocket rule data

This repository maintains native Shadowrocket-compatible rule files:

```text
rules/iran-domains.list
rules/iran-cidr.list
rules/iran-asn.list
```

Sources:

- `bootmortis/iran-hosted-domains` -> non-`.ir` Iranian hosted domains
- `Chocolate4U/Iran-clash-rules` -> Iranian CIDR and ASN data

A GitHub Actions workflow validates and refreshes the files every day. The domain release hash is verified, malformed lines are discarded, minimum dataset sizes are enforced, IPv4 CIDRs become `IP-CIDR`, and IPv6 CIDRs become `IP-CIDR6`.

Important Shadowrocket characteristics:

- IPv6 starts disabled
- Proxy-bound QUIC is blocked for TCP reliability
- Unsupported proxy UDP is rejected instead of falling back to DIRECT
- Iran IP rules use `no-resolve`

## v2rayN for macOS

Full setup guide:

```text
https://github.com/cursedwraith/mysrconfig/blob/main/v2rayn/README.md
```

Importable routing rules:

```text
https://raw.githubusercontent.com/cursedwraith/mysrconfig/main/v2rayn/iran-routing.json
```

Optional routing-template source:

```text
https://raw.githubusercontent.com/cursedwraith/mysrconfig/main/v2rayn/template.json
```

The v2rayN profile is designed primarily for v2rayN 7.24.4 or newer with the Xray core and TUN on macOS. It adds an explicit top-priority Zoom proxy rule, leaves UDP available for meetings, uses `AsIs` routing to avoid unnecessary local DNS classification, and relies on v2rayN's built-in Iran regional Geo source.

## Domestic DNS addresses

Begzar:

- `185.55.226.26`
- `185.55.225.25`
- `185.55.224.24`

Shecan free:

- `178.22.122.100`
- `185.51.200.2`

These are plain DNS server IP addresses. The profiles do not invent unsupported HTTPS DNS endpoints for Begzar or Shecan.
