# Hofer Homelab — Context Document

Paste this at the start of every Claude session.
Update the Session Log and TODO at the end of every session.

---

## Current Model: Claude Sonnet 4.6

---

## Network

| Property | Value |
|---|---|
| LAN subnet | 10.10.10.0/24 |
| DNS domain | *.hofer.lan |
| DNS server | Pihole on nomad (port 53) |
| Reverse proxy | Nginx Proxy Manager on nomad (ports 80/443) |
| SSL | Let's Encrypt via NPM |

---

## Hosts

### nomad — 10.10.10.203 (Ubuntu 24.04, Docker)
Primary server. All compose files live under /srv/homelab/

| Service | Container | Host Port | URL |
|---|---|---|---|
| Homepage | homepage | internal | https://dashboard.hofer.lan |
| Nginx Proxy Manager | npm | 80, 81, 443 | https://npm.hofer.lan |
| Pihole | pihole | 53, 8081 | https://pihole.hofer.lan |
| Portainer | portainer | 9000, 9443 | https://nomad.port.hofer.lan |
| Dozzle | dozzle | internal | https://dozzle.hofer.lan |
| Immich | immich_server | 2283 | https://photos.hofer.lan |
| Immich Kiosk | immich-kiosk | 8085 | https://kiosk.hofer.lan |
| Mealie | mealie | 9925 | https://recipe.hofer.lan |
| Kavita | kavita | 5000 | https://books.hofer.lan |
| Kolibri | kolibri | 8082 | https://kolibri.hofer.lan |
| Kiwix | kiwix | 8080 | https://kiwix.hofer.lan |
| RomM | romm | 8690 | https://rom.hofer.lan |
| Memos | memos | 5230 | https://notes.hofer.lan |
| Vaultwarden | vaultwarden | internal | https://vault.hofer.lan |
| File Browser | filebrowser | 8083 | https://files.hofer.lan |
| Grafana | grafana | 3000 | https://graph.hofer.lan |
| InfluxDB | influxdb | 8086 | (no proxy) |
| Uptime Kuma | uptime-kuma | 3002 | https://uptime.hofer.lan |
| Diun | diun | none | (no web UI, update notifier) |

### rogue — 10.10.10.209 (macOS, Docker)
Secondary server. Media and AI workloads.

| Service | Container | Host Port | URL |
|---|---|---|---|
| Jellyfin | jellyfin | 8096 | https://movies.hofer.lan |
| Open WebUI | open-webui | 3000 | https://ai.hofer.lan |
| Portainer | portainer | 9443 | https://rogue.port.hofer.lan |

### butler — 10.10.10.157
IoT and automation hub. Runs Mosquitto MQTT on port 1883.
Proxied as https://butler.hofer.lan

### scout — 10.10.10.161 (macOS)
Ryan's primary workstation. SSH origin for nomad and rogue.

### Unifi devices (network gear, ping-monitored)
butler, nomad, overwatch, rogue, sentry

---

## Uptime Kuma Monitors
Located at https://uptime.hofer.lan

| Monitor | Type | Target |
|---|---|---|
| Butler- Mosquitto | TCP port | 10.10.10.157:1883 |
| Nomad- Dashboard | HTTP | https://dashboard.hofer.lan |
| Nomad- Dozzle | HTTP | https://dozzle.hofer.lan |
| Nomad- Emulator | HTTP | https://rom.hofer.lan |
| Nomad- File Browser | HTTP | https://files.hofer.lan |
| Nomad- Grafana | HTTP | https://graph.hofer.lan |
| Nomad- Immich | HTTP | https://photos.hofer.lan |
| Nomad- Kavita | HTTP | https://books.hofer.lan |
| Nomad- Kiosk | HTTP | https://kiosk.hofer.lan |
| Nomad- Kiwix | HTTP | https://kiwix.hofer.lan |
| Nomad- Kolibrio | HTTP | https://kolibri.hofer.lan |
| Nomad- Mealie | HTTP | https://recipe.hofer.lan |
| Nomad- Memos | HTTP | https://notes.hofer.lan |
| Nomad- Pihole | HTTP | https://pihole.hofer.lan |
| Nomad- Portainer | HTTP | https://nomad.port.hofer.lan |
| Nomad- Proxy | HTTP | https://npm.hofer.lan |
| Nomad- Vaultwarden | HTTP | https://vault.hofer.lan |
| Rogue- AI | HTTP | https://ai.hofer.lan |
| Rogue- Jellyfin | HTTP | https://movies.hofer.lan |
| Rogue- Portainer | HTTP | https://rogue.port.hofer.lan |
| Unifi- Butler | HTTP | (direct IP) |
| Unifi- Nomad | Ping | 10.10.10.203 |
| Unifi- Overwatch | Ping | (IP unknown) |
| Unifi- Rogue | Ping | 10.10.10.209 |
| Unifi- Sentry | Ping | (IP unknown) |

---

## TODO

- [ ] Confirm Ollama status — was at 10.10.10.203:11434, currently not running. Removed or moved to rogue?
- [ ] Get IPs for overwatch and sentry Unifi devices
- [ ] Confirm butler.hofer.lan NPM proxy target and port
- [ ] Set up shell alias on scout: alias hlcontext='ssh nomad cat /srv/homelab/homelab-context.md'
- [ ] Consider adding InfluxDB monitor (port 8086, no NPM proxy)

---

## Session Log

### 2026-04-18 — Session 1
Model: Claude Sonnet 4.6
- Discovered homelab topology via docker ps, compose ls, NPM nginx configs
- nomad (10.10.10.203) is Ubuntu running Docker directly, not HashiCorp Nomad
- rogue (10.10.10.209) is macOS running Docker
- DNS domain is *.hofer.lan via Pihole; all services proxied via NPM
- Root cause of all Uptime Kuma failures: monitors used .home.arpa DNS which Uptime Kuma cannot resolve
- Fixed all broken monitors to use https://*.hofer.lan URLs
- Added missing monitors: Grafana, Vaultwarden, Kiosk, Rogue-Portainer
- Removed Nomad-Ollama (service not running)
- Created private GitHub repo: github.com/ryanmichaelhofer/homelab
- Initialized /srv/homelab as git repo and pushed

