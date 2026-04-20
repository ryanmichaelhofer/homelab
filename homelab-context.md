# Hofer Homelab — Context Document

Fetch this at the start of every Claude session from:
https://github.com/ryanmichaelhofer/homelab/blob/main/homelab-context.md

---

## Current Model: Claude Sonnet 4.6

---

## Session Protocol

**Open:** Claude fetches this file from GitHub, issues SSH verification commands,
Ryan pastes output, Claude draws 3-box server diagram (name + IP + OS only) + todo list.

**Close:** Claude produces updated homelab-context.md content, Ryan uses Python to write and push.

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

### nomad -- 10.10.10.203 (Ubuntu 24.04, Docker)
**Infrastructure:** npm, pihole, portainer :9443, uptime-kuma :3002, homepage, diun, dozzle
**Productivity:** immich :2283, immich-kiosk :8085, mealie :9925, memos :5230, vaultwarden, filebrowser :8083, kavita :5000, romm :8690
**Knowledge:** kiwix :8080, kolibri :8082
**Telemetry:** grafana :3000, influxdb :8086

### rogue -- 10.10.10.209 (macOS, OrbStack)
**Media:** jellyfin :8096, epg-updater
**AI:** open-webui :3000 -> ai.hofer.lan; ollama (native/Metal): llama3.2:3b, gemma3:12b
**Note:** docker at /usr/local/bin/docker. PATH fixed via ~/.zshenv for non-interactive SSH.

### butler -- 10.10.10.157 (HA OS, Core 2026.4.3)
**Integrations:** eufy (10 door sensors), garmin (x2), meshtastic (D1L serial), govee purifiers, ha-mcp
**Add-ons:** mosquitto :1883, matter server, eufy-security-ws, terminal & SSH :2222, file editor

---

## Peripheral Devices

| Device | IP | Role | Data flow |
|---|---|---|---|
| lookout | 10.10.10.32 | Ecowitt weather station | -> butler |
| herald | 10.10.10.231 | HDHomeRun TV tuner | -> jellyfin on rogue |
| scout | 10.10.10.161 | macOS workstation | SSH origin |
| command | 10.10.10.68 | Windows workstation | passwordless SSH via administrators_authorized_keys |

---

## UniFi

| Device | IP | Role |
|---|---|---|
| sentinel | -- | Network controller |
| sentry | 10.10.10.1 | Gateway/router (DR7) |
| overwatch | 10.10.10.130 | UNVR Instant (6 cameras) |

Planned: UCG Fiber replacing DR7. DR7 -> managed WiFi 7 AP.

---

## Meshtastic

7 nodes: Mesh-Butler (D1L, Den, HA serial gateway), Mesh-CatDad (RAK 4631, Garage),
Mesh-KittyMom (RAK 4631, Master), Mesh-Ford (T1000-E), Mesh-Lincoln (T1000-E),
Mesh-Drive, Mesh-House.
Channels: Ch0 PubBroadcast, Ch1 Meow, Ch2 Purr, Ch3 Bark
MQTT -> local Mosquitto, user=ryanhofer, topic=msh/US, JSON+encryption, uplink+downlink on.

---

## Scout SSH Config (~/.ssh/config)

Host nomad -- 10.10.10.203, user ryanhofer
Host rogue -- 10.10.10.209, user ryanhofer
Host butler -- 10.10.10.157, user root, port 2222
Host command -- 10.10.10.68, user ryanhofer

All hosts: passwordless SSH via ed25519 key generated on scout.
GitHub: SSH auth configured, scout key added to github.com/ryanmichaelhofer.
Aliases: hlcontext='ssh nomad cat /srv/homelab/homelab-context.md', roguedk='ssh rogue /usr/local/bin/docker'
Repos: ~/homelab, ~/house-projects -- both on SSH remotes.

---

## TODO

### Infrastructure
- [ ] InfluxDB monitor in Uptime Kuma (port 8086, no NPM proxy)
- [ ] Gateway upgrade: UCG Fiber replacing DR7

### Home Assistant
- [ ] Meshtastic SNR/RSSI ApexCharts card in HA
- [ ] NWS severe weather alerts -> Meow channel via HA automation
- [ ] Renogy Rover telemetry -> HA via HACS renogy_rover

### AI
- [ ] Upload survival PDFs to Open WebUI knowledge base (RAG workspace)

### Documentation
- [ ] KB Emergency Playbook (ER310PRO, Meshtastic, evacuation -- plain language)
- [ ] KB Infrastructure Runbook (network, cameras, HA, passwords -- plain language)
- [ ] Ham radio licensing -- Technician then General class

### Hardware (tracked in house-projects repo)
- [ ] SDR antenna build -- nRSP-ST, rear left gable peak confirmed
- [ ] CAT6 attic run #2 for nRSP-ST
- [ ] CAT6 shed/coop run
- [ ] Solar coop power decision + execution
- [ ] 5th solar panel for shed winter margin

---

## Session Log

### 2026-04-19 -- Session 3
- Verified all servers: Nomad 23 containers, Rogue 4 containers, Butler HA 2026.4.3 -- all healthy
- Cleared all Session 1/2 TODO items
- Set up SSH key pair on scout (ed25519), passwordless SSH to nomad, rogue, butler, command
- Fixed rogue docker PATH (/usr/local/bin), added roguedk alias on scout
- Added hlcontext alias to scout ~/.zshrc -- working passwordless
- GitHub SSH auth configured, both repos switched to SSH remotes
- homelab repo cloned to ~/homelab on scout
- Created house-projects repo with full structure: sdr-antenna-build, solar-shed, solar-coop, cat6-runs
- Egg tracker built in HA: input_number.eggs_collected + script.log_egg_collection + ApexCharts 7-day card
- SDR build discussion: rear left gable peak confirmed as antenna location
- Overwatch IP confirmed 10.10.10.130, Sentry IP confirmed 10.10.10.1
- Herald (HDHomeRun .231) and Lookout (Ecowitt .32) documented

### 2026-04-18 -- Session 2
- GitHub repo made public, session open/close protocol locked in
- Verified full stack: Nomad 22 containers, Rogue 4, Butler HA 2026.4.3
- Added Lookout (.32) and Herald (.231) to peripheral devices
- Confirmed Uptime Kuma healthy
- Simplified session-open diagram rule: 3 boxes, name + IP + OS only

### 2026-04-18 -- Session 1
- Discovered full homelab topology
- Fixed all Uptime Kuma monitors (.home.arpa -> .hofer.lan)
- Created GitHub repo github.com/ryanmichaelhofer/homelab
