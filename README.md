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

The Shadowrocket profile uses Begzar and Shecan for direct/domestic DNS, with the iOS/system resolver as fallback. It uses native Shadowrocket Iran domain rules plus current Iran CIDR/ASN data and `GEOIP,IR`.

Important Shadowrocket characteristics:

- IPv6 starts disabled
- Proxy-bound QUIC is blocked for TCP reliability
- Unsupported proxy UDP is rejected instead of leaking direct
- `GEOIP,IR` and IP rule sets use `no-resolve`

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

These are plain DNS server IP addresses. The profiles do not invent unsupported `https://.../dns-query` endpoints for Begzar or Shecan.

## Routing data

Shadowrocket uses:

- `sub-kek/shadowrocket-lists` for native Shadowrocket Iran domain rules
- `Chocolate4U/Iran-clash-rules` for compatible Iran CIDR and ASN rules
- Shadowrocket's built-in `GEOIP,IR` as an additional IP-data fallback

v2rayN uses:

- `Chocolate4U/Iran-v2ray-rules` for `geosite:category-ir`, `geoip:ir`, and private-network Geo tags
- this repository's custom rule array instead of the upstream Iran routing template, because the upstream template contains unrelated ad blocking, BitTorrent direct routing, and UDP/443 blocking
