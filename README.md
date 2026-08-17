# mysrconfig

Minimal Shadowrocket split-routing profile for Iran.

## Behavior

- Iranian domains and Iranian destination networks: `DIRECT`
- Everything else: `PROXY`
- No advertising, malware, phishing, rewrite, or content-blocking rules
- IPv6 disabled to avoid unplanned IPv6 bypasses on mixed networks
- Proxy-bound QUIC blocked so affected clients retry over proxyable TCP
- Unsupported proxy UDP is rejected rather than silently sent directly

## DNS

Direct traffic uses the free DNS addresses published by both Begzar and Shecan. Shadowrocket queries the configured addresses in parallel and uses the first successful response. The iOS/system resolver assigned by the ISP is configured only as the fallback.

Begzar:

- `185.55.226.26`
- `185.55.225.25`
- `185.55.224.24`

Shecan free:

- `178.22.122.100`
- `185.51.200.2`

These services publish plain DNS IP addresses; this profile does not invent an unsupported `https://.../dns-query` endpoint. Proxy-routed destination names are resolved remotely by the selected proxy.

## Routing data

- Native Shadowrocket domain rules: `sub-kek/shadowrocket-lists`
- Iran CIDR and ASN data: `Chocolate4U/Iran-clash-rules`
- Shadowrocket built-in `GEOIP,IR` is retained as a final IP-data fallback

The Chocolate4U domain payload is Clash-formatted (`+.domain`) and is not referenced as a Shadowrocket `RULE-SET`. The existing native Shadowrocket domain list is used instead, while the compatible CIDR and ASN sources supplement `GEOIP`.

## Import

```text
https://raw.githubusercontent.com/cursedwraith/mysrconfig/main/iran.conf
```
