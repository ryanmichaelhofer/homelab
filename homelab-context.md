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

**nomad -- 10.10.10.203 (Ubuntu 24.04, Docker)**

Infrastructure: npm :80/81/443, pihole :53/:8081, portainer :9443, uptime-kuma :3002, homarr :7575(internal)->dashboard.hofer.lan, diun, dozzle :8080(internal)

Productivity: immich :2283, immich-kiosk :8085, mealie :9925, memos :5230, vaultwarden :80(internal), filebrowser :8083, kavita :5000, romm :8690

Knowledge: kiwix :8080, kolibri :8082

Telemetry: grafana :3000, influxdb :8086

Supporting: immich_redis :6379(internal), immich_postgres :5432(internal), immich_machine_learning, romm-db :3306(internal)

Note: Homepage replaced by Homarr. dashboard.hofer.lan -> Homarr.

**rogue -- 10.10.10.209 (macOS M2, OrbStack)**

Media: jellyfin :8096, epg-updater

AI: open-webui :3000 -> ai.hofer.lan; ollama (native/Metal): llama3.2:3b, gemma3:12b

Infrastructure: portainer :9443

Note: Docker via OrbStack. PATH=$PATH:/opt/homebrew/bin for non-interactive SSH.

Storage: Backpack external drive at /Volumes/backpack (229GB movies, "Movie Title (Year)/" structure)

**butler -- 10.10.10.157 (HA OS 17.2, Core 2026.4.4)**

Integrations: eufy (10 door sensors), garmin (x2 accounts – Ryan + KB), meshtastic (D1L serial),
  govee (4x H7129 purifiers), govee lights local (fan lights), ha-mcp, nws_alerts (VAZ525), fordpass (x2 trucks)

Add-ons: mosquitto :1883, matter server, eufy-security-ws, terminal & SSH :2222, file editor

HACS integrations: meshtastic, nws_alerts v6.7.3, apexcharts-card, mushroom, card-mod,
  mini-graph-card, meshtastic-card v1.0.0, garmin-connect, eufy-security, govee, fordpass

Note: Govee fan lights require LAN Control enabled in Govee app to appear as light entities.

---

## Peripheral Devices

| Device | IP | Role | Data flow |
|---|---|---|---|
| lookout | 10.10.10.32 | Ecowitt weather station | -> butler (pending integration) |
| herald | 10.10.10.231 | HDHomeRun TV tuner | -> jellyfin on rogue |
| scout | 10.10.10.161 | macOS workstation (MacBook Air) | SSH origin |
| command | 10.10.10.68 | Windows 11 workstation (gaming PC / PS7) | SSH origin |

---

## UniFi

| Device | IP | Role |
|---|---|---|
| sentinel | -- | Network controller |
| sentry | 10.10.10.1 | Gateway/router (DR7) |
| overwatch | 10.10.10.130 | UNVR Instant (6 cameras: 3x G6 PTZ, 2x G6 Bullet, 1x G6 Pro Bullet) |

Planned: UCG Fiber replacing DR7. DR7 -> managed WiFi 7 AP.

---

## Meshtastic

7 nodes: Mesh-Butler (D1L, Den, HA serial gateway), Mesh-CatDad (RAK 4631, Garage),
Mesh-KittyMom (RAK 4631, Master), Mesh-Ford (T1000-E), Mesh-Lincoln (T1000-E),
Mesh-Drive, Mesh-House.

Channels: Ch0 PubBroadcast, Ch1 Meow, Ch2 Purr, Ch3 Bark

MQTT -> local Mosquitto, user=ryanhofer, topic=msh/US, JSON+encryption, uplink+downlink on.

HA entity pattern: meshtastic.gateway_btlr_channel_[name] (e.g. meshtastic.gateway_btlr_channel_purr)

Node SNR entities: sensor.meshtastic_btlr_[node_id]_node_snr

Node position entities: device_tracker.meshtastic_btlr_[node_id]_node_position

Node IDs: Butler=1660533308, Lincoln=190953795, House=256319905, KittyMom=260763213,
  Drive=3772939419, Ford=3872920526, CatDad=895847928

---

## SSH Config

**Scout (~/.ssh/config)**

Host nomad -- 10.10.10.203, user ryanhofer, ed25519

Host rogue -- 10.10.10.209, user ryanhofer, ed25519

Host butler -- 10.10.10.157, user root, port 2222, ed25519

Aliases: hlcontext='ssh nomad cat /srv/homelab/homelab-context.md', roguedk='ssh rogue /usr/local/bin/docker'

Repos: ~/homelab, ~/house-projects (SSH remotes)

**Command (C:\Users\ryanh\.ssh\config)**

Host nomad -- 10.10.10.203, user ryanhofer, ed25519

Host rogue -- 10.10.10.209, user ryanhofer, ed25519

Host butler -- 10.10.10.157, user root, port 2222, ed25519

Repos: C:\homelab\homelab, C:\homelab\house-projects (HTTPS remotes)

Key: C:\Users\ryanh\.ssh\id_ed25519 (generated 2026-04-20, fingerprint SHA256:z9BT0fw+qK+I54M/E4ozuEnr//ZV9Oq5u5VtjbWjKyQ)

---

## Home Assistant – Dashboards

Overview (lovelace): Home (10 cards), Vehicles (2 cards), Doors (7 cards)

Mesh (lovelace/mesh): Map card (6 nodes), ApexCharts SNR 24h (6 nodes), Node Status (hops + last heard)

Media (dashboard-media)

Lighting (dashboard-lighting)

Map

---

## Home Assistant – Automations (4 total)

1. Dropbox - Increment package count (counter.packages_in_dropbox on door open)
2. Dogs Fed - Increment feed count (counter.dogs_fed_today on door open)
3. Daily reset - Counters (midnight reset: packages, dogs fed, eggs – merged Session 5)
4. NWS Alert - Send to Purr channel (sensor.nws_alerts_zone_vaz525_nws_alerts_alerts > 0
   -> meshtastic.broadcast_channel_message to meshtastic.gateway_btlr_channel_purr)
   Message format: "NWS: [Event] | [Severity] | until [time]"

---

## Home Assistant – Helpers

input_number.eggs_collected (egg tracker, resets at midnight)

counter.packages_in_dropbox

counter.dogs_fed_today

Light groups (UI-managed, via Settings → Helpers → Group):
- light.master_fans (Master Fan A/B/C/D – Govee local LAN)
- light.jennys_fans (Jenny Fan A/B/C/D – Govee local LAN)
- light.graces_fans
- light.tessas_fans
- light.playroom_fans
- light.all_upstairs
- light.all_downstairs
- light.all_house

---

## Home Assistant – Scripts

script.log_egg_collection, script.add_1_egg, script.add_2_eggs (egg tracker dashboard buttons)

---

## NWS Alerts

Integration: nws_alerts v6.7.3 (HACS, finity69x2)

Zone: VAZ525 (Chesapeake/Virginia Beach)

Sensor: sensor.nws_alerts_zone_vaz525_nws_alerts_alerts

Attribute structure: Alerts[0].Event, .Severity, .Headline, .Ends (ISO datetime)

Tested: 2026-04-21 – message delivered to Purr channel confirmed

---

## TODO

**Infrastructure**
- [ ] InfluxDB monitor in Uptime Kuma (port 8086, no NPM proxy)
- [ ] Gateway upgrade: UCG Fiber replacing DR7

**Home Assistant**
- [ ] Meshtastic SNR/RSSI ApexCharts historical card (InfluxDB source for long-term trending)
- [ ] Ecowitt (Lookout 10.10.10.32) integration in HA
- [ ] Renogy Rover telemetry -> HA via HACS renogy_rover
- [ ] Fix HA recorder retention -- add purge_keep_days: 365 to configuration.yaml
- [ ] Identify and rename binary_sensor.h51278c47_motion -- confirmed air purifier proximity sensor
- [ ] Add electronics space temp alert automation -- alert above 85F, escalate above 90F
- [ ] Revise GALLBUSH_PATTERN_OF_LIFE.md baseline -- re-run analysis mid-May 2026
- [ ] Replace upstairs purifier filter -- at 17%
- [ ] Replace master purifier filter -- at 29%, monitor
- [ ] HA system design Phase 1: layered security automations (3-tier: perimeter / property / house breach)
- [ ] HA system design Phase 2: Gallbush environmental dashboard (Ecowitt, solar, AQI, soil, Meshtastic node health)
- [ ] HA system design Phase 3: dual-user dashboard (Ryan + Kirstin views)

**AI**
- [ ] Upload survival PDFs to Open WebUI knowledge base (RAG workspace)

**Documentation**
- [ ] KB Emergency Playbook (ER310PRO, Meshtastic, evacuation -- plain language)
- [ ] KB Infrastructure Runbook (network, cameras, HA, passwords -- plain language)
- [ ] Ham radio licensing -- FT-60 handhelds for Ryan and KB
    - [ ] Ryan: Fast-track Technician license (few hours, study + exam)
    - [ ] KB: Technician license (ideally yes, not mandatory immediately)
    - [ ] Kids: Optional (can operate FT-60s under supervision once licensed)
    - [ ] Get minimum safe operation cheat sheet for FT-60 handhelds

**Hardware** (tracked in house-projects repo)
- [ ] CAT6 shed/coop run
- [ ] Solar coop power decision + execution
- [ ] 5th solar panel for shed winter margin

---

## Session Log

2026-04-25 -- Session 9

Run from: Scout (macOS, 10.10.10.161)

Fixed broken Lighting dashboard fan toggles

- Diagnosed light.master_fans and light.jennys_fans as orphaned UI group helpers (group definition deleted, registry entry survived)
- Lighting dashboard was referencing light.master_fans_all and light.jenny_fans_all (wrong entity IDs -- never existed)
- Root cause for Jenny fans: Govee LAN Control was not enabled in Govee app -- devices had BLE registration only (no light entity)
- Fix: Enabled LAN Control in Govee app for Jenny Fan A/B/C/D; recreated both light groups via Settings → Helpers → Group
- light.master_fans: Master Fan A/B/C/D (Govee local LAN)
- light.jennys_fans: Jenny Fan A/B/C/D (Govee local LAN)
- Both toggles confirmed working on Lighting dashboard
- Note: All fan light groups are UI-managed helpers, not YAML-defined
- Note: HA Core updated to 2026.4.4 (was 2026.4.3)

2026-04-24 -- Session 8

Run from: Scout (macOS, 10.10.10.161)

House power sensor fix and ham radio licensing planning

- Fixed sensor.house_total_power — broken template formula from prior YAML rewrite
- Added ham radio licensing TODO structure

2026-04-24 -- Session 7

Run from: [Scout or Command?]

Short admin session

- Removed SDR antenna build and CAT6 attic run #2 from TODO

2026-04-24 -- Session 6

Run from Command (Win11/PS7, 10.10.10.68)

HA system design session -- pattern of life analysis and entity inventory

- Pulled full HA entity inventory: 1,743 entities across 33 domains
- Connected to InfluxDB homelab bucket -- 2.2M energy points
- Conducted full pattern of life analysis
- Created GALLBUSH_PATTERN_OF_LIFE.md

2026-04-21 -- Session 5

Run from Command (Win11/PS7, 10.10.10.68)

HA cleanup and infrastructure build session

- Merged egg nightly reset into Daily reset automation
- Fixed entity name typo: Light- Overheard Bar -> Light- Overhead Bar
- Built Mesh dashboard
- Installed NWS Alerts v6.7.3, built automation to Purr channel

2026-04-20 -- Session 4

First session run from Command (Win11/PS7, 10.10.10.68)

- Generated ed25519 key on Command, configured SSH, cloned repos
- Command is now a first-class work machine

2026-04-19 -- Session 3

- Set up SSH key pair on scout, passwordless SSH to all servers
- Egg tracker built in HA
- Created house-projects repo

2026-04-18 -- Session 2

- GitHub repo made public, session protocol locked in
- Verified full stack

2026-04-18 -- Session 1

- Discovered full homelab topology
- Fixed all Uptime Kuma monitors
- Created GitHub repo github.com/ryanmichaelhofer/homelab