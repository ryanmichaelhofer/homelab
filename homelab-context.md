# Hofer Homelab — Context Document

Fetch this at the start of every Claude session from:
https://github.com/ryanmichaelhofer/homelab/blob/main/homelab-context.md

---

## Current Model: Claude Sonnet 4.6

---

## Session Protocol

**Open:** Claude fetches this file from GitHub, issues SSH verification commands,
Ryan pastes output, Claude draws 3-box server diagram (name + IP + OS only) + todo list.

**Close:** Claude produces updated homelab-context.md content, Ryan pastes block into Nomad terminal and pushes.

---

## Network

| Property | Value |
|---|---|
| LAN subnet | 10.10.10.0/24 |
| DNS domain | *.hofer.lan |
| DNS server | Pihole on Nomad :53 |
| Reverse proxy | Nginx Proxy Manager on Nomad :80/:443 |
| SSL | Let's Encrypt via NPM |

---

## Servers

### nomad — 10.10.10.203 (Ubuntu 24.04, Docker)
**Infrastructure:** npm, pihole, portainer :9443, uptime-kuma :3002, homepage, diun
**Productivity:** immich :2283, mealie :9925, memos :5230, vaultwarden, filebrowser :8083, kavita :5000, romm :8690
**Knowledge:** kiwix :8080, kolibri :8082
**Telemetry:** grafana :3000, influxdb :8086

### rogue — 10.10.10.209 (macOS, OrbStack)
**Media:** jellyfin :8096, epg-updater
**AI:** open-webui :3000 → ai.hofer.lan; ollama (native/Metal): llama3.2:3b, gemma3:12b

### butler — 10.10.10.157 (HA OS 17.2, Core 2026.4.3)
**Integrations:** eufy (10 door sensors), garmin (x2), meshtastic (D1L serial), govee purifiers, ha-mcp
**Add-ons:** mosquitto :1883, matter server, eufy-security-ws, terminal & SSH, file editor

---

## Peripheral Devices

| Device | IP | Role | Data flow |
|---|---|---|---|
| lookout | 10.10.10.32 | Weather station | → butler |
| herald | 10.10.10.231 | TV tuner | → jellyfin on rogue |
| scout | 10.10.10.161 | macOS workstation | SSH origin |
| command | 10.10.10.68 | Windows workstation | — |

---

## UniFi

| Device | IP | Role |
|---|---|---|
| sentinel | — | Network controller |
| sentry | 10.10.10.1 | Gateway/router (DR7) |
| overwatch | 10.10.10.130 | UNVR Instant (6 cameras) |

Planned: UCG Fiber replacing DR7. DR7 → managed WiFi 7 AP.

---

## Meshtastic

7 nodes: Mesh-Butler (D1L, Den, HA serial gateway), Mesh-CatDad (RAK 4631, Garage),
Mesh-KittyMom (RAK 4631, Master), Mesh-Ford (T1000-E), Mesh-Lincoln (T1000-E),
Mesh-Drive, Mesh-House.
Channels: Ch0 PubBroadcast, Ch1 Meow, Ch2 Purr, Ch3 Bark
MQTT → local Mosquitto, user=ryanhofer, topic=msh/US, JSON+encryption, uplink+downlink on.

---

## TODO

### Infrastructure
- [ ] Confirm IPs for Overwatch and Sentry in Uptime Kuma
- [ ] Confirm butler.hofer.lan NPM proxy target and port
- [ ] Shell alias on Scout: alias hlcontext='ssh nomad cat /srv/homelab/homelab-context.md'
- [ ] Set up SDR on Nomad (RTL-SDR or RSP1A, SDR++ Docker, ADS-B → HA dashboard)

### Home Assistant
- [ ] Egg production tracker (input_number, InfluxDB, Grafana)
- [ ] Meshtastic SNR/RSSI ApexCharts card in HA
- [ ] NWS severe weather alerts → Meow channel via HA automation

### AI
- [ ] Upload survival PDFs to Open WebUI knowledge base (RAG workspace)

### Documentation
- [ ] KB Emergency Playbook (ER310PRO, Meshtastic, evacuation — plain language)
- [ ] KB Infrastructure Runbook (network, cameras, HA, passwords — plain language)
- [ ] Ham radio licensing — Technician then General class

---

## Session Log

### 2026-04-18 — Session 2
- GitHub repo made public, session open/close protocol locked in
- Verified full stack: Nomad 22 containers, Rogue (jellyfin, epg-updater, open-webui, ollama), Butler (HA 2026.4.3)
- Added Lookout (.32, weather → butler) and Herald (.231, TV tuner → jellyfin)
- Confirmed Uptime Kuma healthy
- Added SDR task to backlog
- Simplified session-open diagram rule: 3 boxes, name + IP + OS only

### 2026-04-18 — Session 1
- Discovered full homelab topology
- Fixed all Uptime Kuma monitors (.home.arpa → .hofer.lan)
- Created GitHub repo github.com/ryanmichaelhofer/homelab
