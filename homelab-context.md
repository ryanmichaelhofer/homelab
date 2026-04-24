hHofer Homelab â Context Document
Fetch this at the start of every Claude session from:
https://github.com/ryanmichaelhofer/homelab/blob/main/homelab-context.md
---
Current Model: Claude Sonnet 4.6
---
Session Protocol
Open: Claude fetches this file from GitHub, issues SSH verification commands,
Ryan pastes output, Claude draws 3-box server diagram (name + IP + OS only) + prints todo list.
Close: Claude produces updated homelab-context.md content, Ryan commits and pushes to GitHub.
Sessions can be run from:
Scout (10.10.10.161, macOS MacBook Air) â SSH config at ~/.ssh/config
Command (10.10.10.68, Windows 11 / PS7) â SSH config at C:\Users\ryanh\.ssh\config
Command session start: Before SSH verification, run:
```
cd C:\homelab\homelab && git pull
cd C:\homelab\house-projects && git pull
```
---
Network
Property	Value
LAN subnet	10.10.10.0/24
DNS domain	*.hofer.lan
DNS server	Pihole on Nomad :53
Reverse proxy	Nginx Proxy Manager on Nomad :80/:443
SSL	Let's Encrypt via NPM
---
Servers
nomad -- 10.10.10.203 (Ubuntu 24.04, Docker)
Infrastructure: npm :80/81/443, pihole :53/:8081, portainer :9443, uptime-kuma :3002, homarr :7575(internal)->dashboard.hofer.lan, diun, dozzle :8080(internal)
Productivity: immich :2283, immich-kiosk :8085, mealie :9925, memos :5230, vaultwarden :80(internal), filebrowser :8083, kavita :5000, romm :8690
Knowledge: kiwix :8080, kolibri :8082
Telemetry: grafana :3000, influxdb :8086
Supporting: immich_redis :6379(internal), immich_postgres :5432(internal), immich_machine_learning, romm-db :3306(internal)
Note: Homepage replaced by Homarr. dashboard.hofer.lan -> Homarr.

rogue -- 10.10.10.209 (macOS M2, OrbStack)
Media: jellyfin :8096, epg-updater
AI: open-webui :3000 -> ai.hofer.lan; ollama (native/Metal): llama3.2:3b, gemma3:12b
Infrastructure: portainer :9443
Note: Docker via OrbStack. PATH=$PATH:/opt/homebrew/bin for non-interactive SSH.
Storage: Backpack external drive at /Volumes/backpack (229GB movies, "Movie Title (Year)/" structure)

butler -- 10.10.10.157 (HA OS, Core 2026.4.3)
Integrations: eufy (10 door sensors), garmin (x2 accounts â Ryan + KB), meshtastic (D1L serial),
  govee (4x H7129 purifiers), ha-mcp, nws_alerts (VAZ525), fordpass (x2 trucks)
Add-ons: mosquitto :1883, matter server, eufy-security-ws, terminal & SSH :2222, file editor
HACS integrations: meshtastic, nws_alerts v6.7.3, apexcharts-card, mushroom, card-mod,
  mini-graph-card, meshtastic-card v1.0.0, garmin-connect, eufy-security, govee, fordpass
---
Peripheral Devices
Device	IP	Role	Data flow
lookout	10.10.10.32	Ecowitt weather station	-> butler (pending integration)
herald	10.10.10.231	HDHomeRun TV tuner	-> jellyfin on rogue
scout	10.10.10.161	macOS workstation (MacBook Air)	SSH origin
command	10.10.10.68	Windows 11 workstation (gaming PC / PS7)	SSH origin
---
UniFi
Device	IP	Role
sentinel	--	Network controller
sentry	10.10.10.1	Gateway/router (DR7)
overwatch	10.10.10.130	UNVR Instant (6 cameras: 3x G6 PTZ, 2x G6 Bullet, 1x G6 Pro Bullet)
Planned: UCG Fiber replacing DR7. DR7 -> managed WiFi 7 AP.
---
Meshtastic
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
SSH Config
Scout (~/.ssh/config)
Host nomad -- 10.10.10.203, user ryanhofer, ed25519
Host rogue -- 10.10.10.209, user ryanhofer, ed25519
Host butler -- 10.10.10.157, user root, port 2222, ed25519
Host command -- 10.10.10.68, user ryanhofer, ed25519
Aliases: hlcontext='ssh nomad cat /srv/homelab/homelab-context.md', roguedk='ssh rogue /usr/local/bin/docker'
Repos: ~/homelab, ~/house-projects (SSH remotes)

Command (C:\Users\ryanh\.ssh\config)
Host nomad -- 10.10.10.203, user ryanhofer, ed25519
Host rogue -- 10.10.10.209, user ryanhofer, ed25519
Host butler -- 10.10.10.157, user root, port 2222, ed25519
Repos: C:\homelab\homelab, C:\homelab\house-projects (HTTPS remotes)
Key: C:\Users\ryanh\.ssh\id_ed25519 (generated 2026-04-20, fingerprint SHA256:z9BT0fw+qK+I54M/E4ozuEnr//ZV9Oq5u5VtjbWjKyQ)
---
Home Assistant â Dashboards
Overview (lovelace): Home (10 cards), Vehicles (2 cards), Doors (7 cards)
Mesh (lovelace/mesh): Map card (6 nodes), ApexCharts SNR 24h (6 nodes), Node Status (hops + last heard)
Media (dashboard-media)
Lighting (dashboard-lighting)
Map

Home Assistant â Automations (4 total)
1. Dropbox - Increment package count (counter.packages_in_dropbox on door open)
2. Dogs Fed - Increment feed count (counter.dogs_fed_today on door open)
3. Daily reset - Counters (midnight reset: packages, dogs fed, eggs â merged Session 5)
4. NWS Alert - Send to Purr channel (sensor.nws_alerts_zone_vaz525_nws_alerts_alerts > 0
   -> meshtastic.broadcast_channel_message to meshtastic.gateway_btlr_channel_purr)
   Message format: "NWS: [Event] | [Severity] | until [time]"

Home Assistant â Helpers
input_number.eggs_collected (egg tracker, resets at midnight)
counter.packages_in_dropbox
counter.dogs_fed_today

Home Assistant â Scripts
script.log_egg_collection, script.add_1_egg, script.add_2_eggs (egg tracker dashboard buttons)

NWS Alerts
Integration: nws_alerts v6.7.3 (HACS, finity69x2)
Zone: VAZ525 (Chesapeake/Virginia Beach)
Sensor: sensor.nws_alerts_zone_vaz525_nws_alerts_alerts
Attribute structure: Alerts[0].Event, .Severity, .Headline, .Ends (ISO datetime)
Tested: 2026-04-21 â message delivered to Purr channel confirmed
---
TODO
Infrastructure
[ ] InfluxDB monitor in Uptime Kuma (port 8086, no NPM proxy)
[ ] Gateway upgrade: UCG Fiber replacing DR7

Home Assistant
[ ] Meshtastic SNR/RSSI ApexCharts historical card (InfluxDB source for long-term trending)
[ ] Ecowitt (Lookout 10.10.10.32) integration in HA
[ ] Renogy Rover telemetry -> HA via HACS renogy_rover
[ ] Fix HA recorder retention -- add purge_keep_days: 365 to configuration.yaml (currently losing data after ~10 days)
[ ] Fix Reolink camera motion binary sensors -- all 5 showing unavailable (broken webhook/RTSP event subscription)
[ ] Identify and rename binary_sensor.h51278c47_motion -- confirmed air purifier proximity sensor, not security-relevant
[ ] Add electronics space temp alert automation -- space regularly hits 87-89F, alert above 85F, escalate above 90F
[ ] Fix automation.egg_nightly_reset -- currently unavailable
[ ] Fix master_fans_all and jenny_fans_all lights -- showing unavailable
[ ] Revise GALLBUSH_PATTERN_OF_LIFE.md baseline -- re-run analysis mid-May 2026 with clean post-spring-break data
[ ] Replace upstairs purifier filter -- at 17%
[ ] Replace master purifier filter -- at 29%, monitor
[ ] HA system design Phase 1: layered security automations (3-tier: perimeter / property / house breach)
[ ] HA system design Phase 2: Gallbush environmental dashboard (Ecowitt, solar, AQI, soil, Meshtastic node health)
[ ] HA system design Phase 3: dual-user dashboard (Ryan + Kirstin views)

AI
[ ] Upload survival PDFs to Open WebUI knowledge base (RAG workspace)

Documentation
[ ] KB Emergency Playbook (ER310PRO, Meshtastic, evacuation -- plain language)
[ ] KB Infrastructure Runbook (network, cameras, HA, passwords -- plain language)
[ ] Ham radio licensing -- Technician then General class

Hardware (tracked in house-projects repo)
[ ] CAT6 shed/coop run
[ ] Solar coop power decision + execution
[ ] 5th solar panel for shed winter margin
---
Session Log

2026-04-24 -- Session 8
Run from: Scout (macOS, 10.10.10.161)
Fixed sensor.house_total_power — was broken from a prior YAML rewrite (template not loading)
Formula confirmed correct: (a1_main - a2_solar + b1_main - b2_solar) = net house consumption in W
Negative = net exporting to grid (e.g. -768W with solar running)
Fix applied: edited /config/configuration.yaml directly on Butler, reloaded via homeassistant.reload_all in Developer Tools
Sensor confirmed live at -768W with solar producing ~648W per leg
3-day ApexCharts history shows zeros (recorder had bad data) — will fill in correctly going forward

2026-04-24 -- Session 7
Run from: [Scout or Command?]
Short admin session
- Removed SDR antenna build and CAT6 attic run #2 from TODO -- chose handhelds for resilience/portability

2026-04-24 -- Session 6
Run from Command (Win11/PS7, 10.10.10.68)
HA system design session -- pattern of life analysis and entity inventory
- Pulled full HA entity inventory: 1,743 entities across 33 domains
- Documented all integrations: UniFi Protect, Eufy, Ecowitt GW3000B, Enphase Envoy, FordPass (x2), Garmin Connect (x2), Meshtastic (GALLBUSH mesh 7 nodes), Roborock, SmartThings, Govee, Shelly, Refoss, go2rtc, InfluxDB, NWS, FlightRadar24, and more
- Connected to InfluxDB homelab bucket (no retention limit) -- 2.2M energy points, 245K presence events, 19K motion triggers from April 14 onward
- Conducted full pattern of life analysis: door traffic, motion by hour/day, presence/away windows, environmental baseline
- Key findings: garage side door is real front door, front door is Kirstin morning/evening dog routine, Ryan active in garage from ~4-5AM, true overnight quiet only midnight-3AM, 3PM is busiest door hour, Wed/Thu most active days (spring break inflated)
- Confirmed GALLBUSH-BUTLER Meshtastic gateway connected, all 7 nodes active with healthy SNR (5.5-6.5 dB)
- Solar system net-exporting ~6.3kW at time of analysis; electronics space running 87-89F (action item)
- Created GALLBUSH_PATTERN_OF_LIFE.md (see repo -- TODO: commit separately)
- Added 12 new TODO items to Home Assistant section
- Note: baseline data is spring-break-influenced -- re-run analysis mid-May 2026

2026-04-21 -- Session 5
Run from Command (Win11/PS7, 10.10.10.68)
HA cleanup and infrastructure build session
- Merged egg nightly reset into Daily reset automation (3 counters, one automation at midnight)
- Fixed entity name typo: Light- Overheard Bar -> Light- Overhead Bar (Govee, Kitchen)
- Confirmed Homarr replaced Homepage on Nomad; updated context doc
- Audited all HA helpers, scripts, automations â no orphans found
- Built Mesh dashboard (lovelace/mesh): map card (6 GPS nodes) + ApexCharts SNR 24h + Node Status
- Installed NWS Alerts v6.7.3 via HACS, configured zone VAZ525
- Built automation: NWS alerts -> Meshtastic Purr (Ch2)
  Message: "NWS: [Event] | [Severity] | until [time]"
- Tested Purr channel delivery confirmed via Meshtastic app
- Drive node showing -15.5 dB SNR (anomalous, monitor)
- Butler node has no GPS (expected, D1L serial gateway)
- KittyMom/Drive/Ford sharing stale GPS coordinates (likely indoor, no fix)

2026-04-20 -- Session 4
First session run from Command (Win11/PS7, 10.10.10.68)
Generated ed25519 key on Command, pushed to nomad/rogue/butler authorized_keys
Configured SSH config on Command with short names (nomad/rogue/butler) -- all passwordless verified
Installed Git on Command, cloned homelab + house-projects to C:\homelab\
Verified full stack: Nomad 23 containers, Rogue 4 containers, Butler 5 add-ons -- all healthy
Confirmed AnythingLLM + Ollama removed from Nomad (redundant with Rogue M2/Metal)
Updated session protocol: git pull both repos at start of every Command session
Command is now a first-class work machine alongside Scout

2026-04-19 -- Session 3
Verified all servers: Nomad 23 containers, Rogue 4 containers, Butler HA 2026.4.3 -- all healthy
Cleared all Session 1/2 TODO items
Set up SSH key pair on scout (ed25519), passwordless SSH to nomad, rogue, butler, command
Fixed rogue docker PATH (/usr/local/bin), added roguedk alias on scout
Added hlcontext alias to scout ~/.zshrc -- working passwordless
GitHub SSH auth configured, both repos switched to SSH remotes
homelab repo cloned to ~/homelab on scout
Created house-projects repo with full structure: sdr-antenna-build, solar-shed, solar-coop, cat6-runs
Egg tracker built in HA: input_number.eggs_collected + script.log_egg_collection + ApexCharts 7-day card
SDR build discussion: rear left gable peak confirmed as antenna location
Overwatch IP confirmed 10.10.10.130, Sentry IP confirmed 10.10.10.1
Herald (HDHomeRun .231) and Lookout (Ecowitt .32) documented

2026-04-18 -- Session 2
GitHub repo made public, session open/close protocol locked in
Verified full stack: Nomad 22 containers, Rogue 4, Butler HA 2026.4.3
Added Lookout (.32) and Herald (.231) to peripheral devices
Confirmed Uptime Kuma healthy
Simplified session-open diagram rule: 3 boxes, name + IP + OS only

2026-04-18 -- Session 1
Discovered full homelab topology
Fixed all Uptime Kuma monitors (.home.arpa -> .hofer.lan)
Created GitHub repo github.com/ryanmichaelhofer/homelab
