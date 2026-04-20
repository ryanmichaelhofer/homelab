# Homarr Dashboard Build Guide
# Hofer Homelab — homarr.hofer.lan

Organized by priority tier: Survival → Resilience → Operational → Knowledge → Comfort

---

## TIER 1 — Survival / Critical
*Must work when everything else is failing*

| App | URL | Notes |
|---|---|---|
| Home Assistant | http://butler.hofer.lan:8123 | Core smart home / automation |
| Pi-hole | http://nomad.hofer.lan:8081 | DNS — everything depends on this |
| NPM | http://nomad.hofer.lan:81 | Reverse proxy admin |
| Uptime Kuma | http://uptime.hofer.lan:3002 | Service health monitoring |
| Mosquitto | mqtt://butler.hofer.lan:1883 | MQTT broker (no UI — skip or use link) |

---

## TIER 2 — Resilience
*Situational awareness, comms, power monitoring*

| App | URL | Notes |
|---|---|---|
| Grafana | http://graph.hofer.lan | Telemetry dashboards |
| InfluxDB | http://nomad.hofer.lan:8086 | Time series DB (admin access) |
| Overwatch (UNVR) | http://10.10.10.130 | Camera NVR — no hofer.lan DNS |
| Vaultwarden | http://vault.hofer.lan | Password manager — critical for KB runbook |

---

## TIER 3 — Operational
*Day-to-day homestead running*

| App | URL | Notes |
|---|---|---|
| Mealie | http://mealie.hofer.lan | Recipes / meal planning |
| Immich | http://photos.hofer.lan | Photo management |
| Immich Kiosk | http://nomad.hofer.lan:8085 | Kiosk photo display |
| Filebrowser | http://filebrowser.hofer.lan | File management |
| Memos | http://notes.hofer.lan | Notes |
| Kavita | http://books.hofer.lan | Book library |

---

## TIER 4 — Knowledge Base
*Offline reference when internet is gone*

| App | URL | Notes |
|---|---|---|
| Kiwix | http://nomad.hofer.lan:8080 | Offline Wikipedia + ZIMs |
| Kolibri | http://nomad.hofer.lan:8082 | Offline learning platform |
| Open WebUI | http://ai.hofer.lan | Local AI / RAG (survival PDFs) |

---

## TIER 5 — Comfort / Hotel
*Nice to have, non-essential*

| App | URL | Notes |
|---|---|---|
| Jellyfin | http://movies.hofer.lan | Media server |
| RomM | http://rom.hofer.lan | ROM / game library |

---

## INFRASTRUCTURE (always visible, sidebar or separate board)

| App | URL | Notes |
|---|---|---|
| Portainer (Nomad) | http://nomad.hofer.lan:9443 | Docker management — Nomad |
| Portainer (Rogue) | http://rogue.hofer.lan:9443 | Docker management — Rogue |
| Dozzle | http://nomad.hofer.lan:8080 | Container log viewer |
| Homepage | http://dashboard.hofer.lan | Existing dashboard (while evaluating) |

---

## Build Order
1. Create board named "Hofer Homelab"
2. Add Tier 1 apps first — verify each URL loads before moving on
3. Work down through tiers
4. Add Infrastructure group last
5. Arrange tiles by tier top-to-bottom on the board
