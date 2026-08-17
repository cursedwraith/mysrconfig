# v2rayN Iran profile for macOS

This profile is designed for v2rayN with the Xray core on macOS:

- Iranian domains and Iranian destination IPs: `DIRECT`
- Private and LAN traffic: `DIRECT`
- Zoom: always `PROXY`
- Everything else: `PROXY`
- No ad, malware, phishing, cryptominer, rewrite, or content-blocking rules
- No BitTorrent bypass
- No UDP/443 blocking rule

Provider nodes and subscriptions are not included. Import the provider configuration unchanged.

## Files

- `iran-routing.json`: importable v2rayN custom-routing rule array
- `template.json`: optional v2rayN routing-template source with `domainStrategy = AsIs`

Raw routing URL:

```text
https://raw.githubusercontent.com/cursedwraith/mysrconfig/main/v2rayn/iran-routing.json
```

Raw template URL:

```text
https://raw.githubusercontent.com/cursedwraith/mysrconfig/main/v2rayn/template.json
```

## 1. Software and provider node

Use v2rayN 7.24.4 or newer and update the Xray core. Import the provider node or subscription without changing its server address, port, UUID/password, transport, SNI, REALITY fields, flow, path, fingerprint, or other protocol fields.

Preferred general-purpose nodes are provider-supplied VLESS/REALITY, VLESS/XHTTP, or Trojan/TLS nodes on TCP 443. For Zoom, the selected node must also carry UDP reliably.

Recommended basic settings:

| Setting | Value |
|---|---|
| Core | Xray |
| Local inbound UDP | On |
| Sniffing | On |
| `routeOnly` | On |
| Allow LAN connections | Off |
| Mux | Off |
| Fragmentation | Off initially |
| Skip TLS verification | Off |
| Root certificate provider | Mozilla or Chrome |
| Normal log level | Warning or Info |
| Save logs to files | Off except while diagnosing |

## 2. Install the Iran Geo files

In v2rayN, open the regional preset settings and select **Iran**. Then update the Geo files while connected through a working proxy.

The Iran preset should use this Geo source pattern:

```text
https://github.com/Chocolate4U/Iran-v2ray-rules/releases/latest/download/{0}.dat
```

The required tags are:

```text
geosite:category-ir
geoip:ir
geoip:private
geosite:private
```

Do not import the upstream Iran routing or DNS templates unchanged. At the time this profile was created, those templates also included ad blocking, BitTorrent direct routing, UDP/443 blocking, and public-DNS choices that do not match this profile.

## 3. Import and activate the routing profile

Open **Routing Settings** and set:

```text
Routing domain strategy: AsIs
```

Then:

1. Create a routing profile named `Iran direct - rest proxy - Zoom proxy`.
2. Import the rule array from `iran-routing.json`, or paste its JSON into the rule importer.
3. Set this profile as the active/default routing profile.
4. Confirm that the rule order is unchanged.

Rule order:

```text
1. Zoom domains                     -> PROXY
2. Private/LAN IPs                  -> DIRECT
3. Private/LAN domains              -> DIRECT
4. Iranian domains                  -> DIRECT
5. Iranian destination IPs          -> DIRECT
6. Everything else, TCP and UDP     -> PROXY
```

`AsIs` is intentional. An unknown hostname is not locally resolved merely so an IP-country rule can classify it. This reduces dependence on poisoned local DNS. Iranian domains are identified by domain rules, while `geoip:ir` handles destinations that are already known as IP addresses.

## 4. DNS settings

Use v2rayN's **simple DNS settings**, not the upstream custom Iran DNS template.

### Copy/paste values

Direct DNS:

```text
185.55.226.26,185.55.225.25,185.55.224.24,178.22.122.100,185.51.200.2,localhost
```

Remote DNS:

```text
https://cloudflare-dns.com/dns-query,https://dns.google/dns-query,8.8.8.8
```

Bootstrap DNS:

```text
185.55.226.26,178.22.122.100
```

Other DNS settings:

| Setting | Value |
|---|---|
| Direct-target resolution strategy | `UseIPv4` |
| Proxy-target resolution strategy | `AsIs` |
| Proxy-server dial strategy | `AsIs` |
| Parallel query | On |
| Optimistic cache / serve stale | On |
| Add common DNS Hosts | On |
| Use system hosts | Off unless deliberately maintained |
| FakeIP | Off |
| Direct expected IPs | Empty initially |

How this is intended to work:

```text
Iranian domain
    -> Begzar + Shecan + macOS/ISP resolver
    -> DIRECT

Foreign or Zoom domain
    -> remote DNS through the selected proxy, or remote resolution by the proxy
    -> PROXY
```

`localhost` represents the DNS resolver supplied to macOS by the current network. Because it is in the Direct DNS list, it is intended for domains already classified as direct/Iranian, not as the foreign Internet resolver.

The remote list contains multiple choices. A locally blocked Cloudflare or Google DNS endpoint can still work because the DNS request is sent through the established proxy. The final `8.8.8.8` entry provides a non-DoH fallback through a UDP-capable proxy.

Optional strict DNS-poisoning check:

```text
Direct expected IPs: geoip:ir
```

Leave this empty for maximum compatibility. Enabling it can reject a poisoned non-Iranian answer for a listed Iranian domain, but it can also reject a legitimate Iranian service that uses a foreign CDN.

### Proxy-node bootstrap

The proxy server itself must be reached before foreign DNS can use the tunnel. Best choices, in order:

1. Provider node whose server address is a literal IP.
2. Provider-confirmed hostname-to-IP entry in v2rayN DNS Hosts or the macOS hosts file.
3. A provider hostname that resolves correctly through the current local/bootstrap path.

Do not replace a provider hostname with an arbitrary IP. CDN, TLS, XHTTP, and REALITY configurations can depend on the provider's exact address and SNI design.

## 5. TUN mode on macOS

First verify one node with System Proxy mode. Once it works, switch to TUN for Zoom and other applications that may ignore the macOS proxy settings.

Recommended TUN settings:

| Setting | Value |
|---|---|
| Enable TUN | On |
| Auto Route | On |
| Strict Route | On |
| Stack | `gvisor` |
| MTU | `1500` initially |
| Enable IPv6 TUN address | Off initially |
| ICMP routing | `rule` |
| Legacy/proxy-route protection | On |
| System Proxy while TUN is active | Clear/Off |

Suggested route exclusions:

```text
10.0.0.0/8
100.64.0.0/10
127.0.0.0/8
169.254.0.0/16
172.16.0.0/12
192.168.0.0/16
224.0.0.0/4
255.255.255.255/32
fc00::/7
fe80::/10
ff00::/8
```

macOS will request administrator authorization when v2rayN creates the TUN interface and routes.

MTU tuning:

```text
Start:                 1500
Large transfers stall: 1408
Still unstable:        1280
Stable fast network:   test 9000
```

Do not use synthetic TUN ping results as the only connectivity test. Test HTTPS, a real download, DNS, and Zoom media.

## 6. Zoom meetings

The first routing rule pins these domains to the proxy:

```text
zoom.us
zoom.com
zoomapp.cloud
```

Keep UDP enabled. Do not add a global UDP/443 block to this profile. Use a node that passes v2rayN's UDP test, preferably both DNS and STUN tests.

Before an important meeting:

1. Select one proven, stable, UDP-capable node manually.
2. Avoid automatic node switching during the meeting.
3. Run a Zoom test call with camera, microphone, and screen sharing.
4. Test on the same Wi-Fi or hotspot that will be used for the meeting.
5. If audio/video falls back to TCP or becomes unstable, try another provider node before changing routing rules.

Mux should remain off. A low web latency number alone does not prove good Zoom quality; jitter, packet loss, and UDP stability matter more.

## 7. Verification

Use this sequence:

```text
1. TUN off; System Proxy on.
2. Activate the imported Iran routing profile.
3. Confirm an Iranian site is DIRECT in the v2rayN log.
4. Confirm a foreign IP-check site shows the proxy egress IP.
5. Confirm zoom.us is PROXY in the log.
6. Clear System Proxy.
7. Enable TUN with gVisor.
8. Repeat the tests.
9. Run a v2rayN UDP DNS/STUN test.
10. Run a Zoom test meeting.
```

Expected results:

| Test | Expected route |
|---|---|
| Private/LAN destination | DIRECT |
| `.ir` or listed Iranian domain | DIRECT |
| Literal Iranian destination IP | DIRECT |
| Zoom | PROXY |
| Other foreign destination | PROXY |

## 8. Troubleshooting

| Symptom | Check |
|---|---|
| `geosite:category-ir` or `geoip:ir` is missing | Re-select Iran regional preset and update Geo files |
| Iranian domains go through the proxy | Confirm this routing profile is active and rule order is intact |
| Foreign domains go direct | Confirm the final TCP/UDP catch-all rule is `proxy` |
| Browser works but Zoom does not | Enable TUN and verify local inbound/node UDP support |
| Zoom connects but media is poor | Change to a lower-jitter, lower-loss UDP-capable node |
| System Proxy works but TUN fails | Check sudo authorization, gVisor, Auto Route, Strict Route, route protection, DNS, and MTU |
| Small pages work but downloads/video stall | Reduce MTU to 1408, then 1280 |
| Provider hostname resolves incorrectly | Use a provider-supplied IP or trusted static Hosts mapping |
| Cloudflare DNS is blocked locally | It should be used through the proxy; Google and `8.8.8.8` remain alternatives |

## Design notes

This profile intentionally differs from the standard Chocolate4U v2rayN routing preset:

- no `geosite:category-ads-all` block
- no malware/phishing rules
- no BitTorrent direct rule
- no direct rule for `8.8.8.8`
- no global UDP/443 block
- Zoom has an explicit top-priority proxy rule
- routing strategy is `AsIs`, not `IPOnDemand`

The Iran Geo data is still sourced from Chocolate4U because v2rayN 7.24.4 includes it as the built-in Iran regional source.
