# Gallbush — Pattern of Life Baseline
**Document Version:** 1.0
**Date:** April 24, 2026
**Data Period:** April 14–23, 2026
**Data Sources:** Home Assistant InfluxDB (homelab bucket), HA Recorder API
**Total Data Points Analyzed:** 878 door events, 9,689 motion triggers, 245,844 presence events

> ⚠️ **Data Caveat:** This baseline was collected during spring break week (approximately April 14–21, 2026).
> Mid-week activity spikes (Tuesday/Wednesday/Thursday motion and door counts) and irregular
> presence patterns are likely spring-break-influenced. This document should be revised in
> mid-May 2026 once a full month of normal-schedule data is available. Core behavioral
> signatures (morning routine, evening shutdown, overnight quiet window) are considered reliable.

---

## 1. HOUSEHOLD MEMBERS & DEVICES

| Person/Entity | Tracker | Typical Pattern |
|---|---|---|
| Ryan (Daddy) | device_tracker.daddy_iphone | Flexible schedule, often home, regular mid-morning and late-afternoon outings |
| Kirstin (Mama) | device_tracker.mamas | Multiple short trips per day, consistent morning/midday/evening structure |
| Kids | No individual trackers | Presence inferred via stairs sensor and motion patterns |
| Scout (dog) | device_tracker.scout | Never leaves property |
| Xena (dog) | Inferred via front door | Never leaves property |
| Daddy iPad | device_tracker.daddy_ipad | Fixed device, always home — not a presence indicator |

---

## 2. DAILY HOUSEHOLD RHYTHM

### The Morning Sequence
The household morning follows a consistent and predictable sequence:

1. **~4:00–5:00 AM** — Ryan is active in the garage. binary_sensor.presence_garage_motion begins firing. This is the first reliable sign the household is awake.
2. **~5:50–6:00 AM** — Kirstin opens the front door to let dogs out. binary_sensor.front_door_sensor_open first open of the day. Time is highly consistent: observed at 5:51, 5:52, 5:54 AM on multiple weekdays.
3. **~7:00–9:00 AM** — Kids are woken up. binary_sensor.stairs_sensor_open begins spiking as children move between floors.
4. **~7:00–9:00 AM** — Garage side door and laundry door begin regular use as the family moves in and out.

### The Midday Window
- Activity dips between approximately 11:00 AM and 1:00 PM
- Both Ryan and Kirstin show regular away periods during 11 AM–2 PM
- This is a moderate vulnerability window — one or both adults may be off-property

### The Afternoon
- Activity picks back up strongly at 3:00 PM (83 door opens at this hour — highest single-hour count)
- Consistent with school pickup or after-school activity returning home
- Garage side and laundry doors are primary re-entry points

### The Evening
- Peak household activity is 7:00–10:00 PM (724–740 motion triggers per hour at peak)
- 9 PM is the single busiest hour of the entire day for motion activity
- Kirstin's evening shutdown: front door opens between 5:00–9:30 PM to bring dogs in and close up
- Evening front door cluster observed at: 5:02, 5:07, 5:27, 5:30, 5:34, 8:37 PM across sample days

### Overnight / True Quiet
- Midnight to 3:00 AM is the only consistently quiet window
- Motion triggers drop to near-zero (0–12 per hour)
- Any sensor activity in this window is genuinely anomalous
- One documented 3:00 AM front door open: dog illness event (Xena) — not a security concern

---

## 3. DOOR USAGE PATTERNS

### Door Hierarchy by Traffic Volume
| Door | Entity | 10-Day Opens | Daily Avg | Primary Use |
|---|---|---|---|---|
| Stairs (internal) | binary_sensor.stairs_sensor_open | 356 | 36 | Children moving between floors |
| Garage side door | binary_sensor.garage_side_sensor_open | 198 | 20 | Primary household entry/exit point |
| Laundry/outer garage | binary_sensor.laundry_outer_sensor_open | 143 | 14 | Secondary garage access |
| Front door | binary_sensor.front_door_sensor_open | 53 | 5 | Kirstin morning/evening dog routine — NOT a general entry point |
| Bedroom back door | binary_sensor.bedroom_back_sensor_open | 49 | 5 | Back patio/yard access |
| Chicken door | binary_sensor.chicken_door_sensor_open | 35 | 4 | Farm chores — morning release, midday check, evening close |
| Dropbox | binary_sensor.dropbox_sensor_open | 34 | 3 | Package and mail checks, ~3x daily |
| Kitchen back door | binary_sensor.kitchen_back_sensor_open | 10 | 1 | Least used exterior door |

### Key Door Behavioral Notes
- The garage side door is the real front door. It handles 23% of all household entry/exit traffic.
- The front door is a routine marker, not a general entry point. Opens predictably at ~5:50 AM and ~5–8 PM.
- The stairs sensor is the best proxy for kids awake/active. 356 opens over 10 days = ~36/day.
- The chicken door follows a farm chore schedule — approximately 3–4 opens per day.
- Front door open pattern: morning (4–11 AM) 23 opens, evening (5–9 PM) 14 opens, midday 15 opens, overnight 1 open.
- Consistent morning time: 5:51–5:54 AM on multiple weekdays — Kirstin's clockwork routine.

### Door Activity by Hour (EST)
| Hour | Opens | Notes |
|---|---|---|
| 4 AM | 23 | Early morning farm/garage activity begins |
| 5 AM | 37 | Morning ramp-up |
| 6 AM | 39 | Dogs out, chicken release |
| 7 AM | 49 | Peak morning — kids up, day fully started |
| 8 AM | 23 | Morning continuation |
| 9–10 AM | 30–45 | Mid-morning movement |
| 11 AM–2 PM | 34–47 | Sustained activity, possible away window |
| 3 PM | 83 | Single busiest hour — afternoon return |
| 4–6 PM | 45–66 | Afternoon/evening activity |
| 7–9 PM | 63–69 | Evening peak |
| 10–11 PM | 19–5 | Wind-down |
| Midnight–3 AM | 0–3 | True quiet — anomalous if active |

### Door Activity by Day of Week
| Day | Opens | Notes |
|---|---|---|
| Sunday | 58 | Quietest day |
| Monday | 64 | Low activity |
| Tuesday | 201 | High — spring break inflated |
| Wednesday | 143 | High — spring break inflated |
| Thursday | 177 | High — spring break inflated |
| Friday | 103 | Moderate |
| Saturday | 132 | Active weekend |

---

## 4. MOTION SENSOR PATTERNS

### Sensor Hierarchy by Activity
| Sensor | Entity | 10-Day Triggers | Daily Avg | Zone |
|---|---|---|---|---|
| Garage presence | binary_sensor.presence_garage_motion | 6,589 | 659 | Ryan's primary work/living space |
| Master bedroom | binary_sensor.presence_master_motion | 1,650 | 165 | Bedroom — regular daytime and evening use |
| Unknown (h51278c47) | binary_sensor.h51278c47_motion | 1,075 | 108 | Confirmed: air purifier proximity sensor — NOT security-relevant |
| Gate | binary_sensor.gate_motion_detected | 267 | 27 | Property perimeter — earliest external warning |
| Driveway spotlight | binary_sensor.spotlight_driveway_motion | 108 | 11 | Driveway approach — secondary external warning |

### Motion Behavioral Notes
- Garage motion sensor is the single most reliable household activity indicator. Ryan's early-morning presence drives 68% of all motion events. NOT security-sensitive — high activity expected at any hour Ryan is awake.
- Gate motion is the earliest perimeter warning. Fires before driveway motion and well before any door sensor.
- binary_sensor.h51278c47_motion = air purifier proximity sensor. Exclude from all security automation logic.
- Master bedroom motion is substantial during daytime — normal.

### Peak Motion Hours (EST)
- True overnight quiet: midnight–3 AM (0–12 triggers/hour)
- Morning ramp: 4–5 AM (149–404 triggers) — Ryan in garage
- Morning peak: 7–8 AM (693/603 triggers) — full household active
- Evening peak: 9–10 PM (740/632 triggers) — busiest period of day
- Day is never truly quiet between 4 AM and 11 PM

### Motion by Day of Week
| Day | Triggers | Notes |
|---|---|---|
| Wednesday | 2,236 | Spring break inflated |
| Thursday | 2,222 | Spring break inflated |
| Tuesday | 1,436 | Possibly inflated |
| Friday | 1,066 | Moderate |
| Saturday | 1,154 | Active weekend |
| Sunday | 808 | Lower |
| Monday | 766 | Lowest |

### Garage Motion by Hour (EST) — Primary Activity Indicator
| Hour | Triggers | Notes |
|---|---|---|
| 4 AM | 116 | Ryan starts day |
| 5 AM | 303 | Ramp up |
| 6 AM | 226 | Morning activity |
| 7 AM | 536 | Peak morning |
| 8 AM | 466 | High activity |
| 9 AM | 348 | Mid-morning |
| 10–14h | 198–389 | Sustained |
| 15–17h | 318–350 | Afternoon return |
| 18–19h | 344–495 | Evening ramp |
| 20–21h | 525–631 | Evening peak |
| 22h | 248 | Wind-down |
| 23h | 76 | Late wind-down |

---

## 5. PRESENCE & OCCUPANCY

### Ryan (Daddy iPhone) — 528 tracked events
- Home the large majority of the time
- Regular departures: 11 AM–12 PM window (mid-morning errands)
- Regular departures: 4–6 PM window (late afternoon)
- Typical returns: 2–4 PM and 7–8 PM
- No consistent daily work-departure signature — flexible/home-based schedule
- 528 tracked state changes over 10 days indicates active GPS reporting

### Kirstin (Mama) — 137 tracked events, 110 away events
- More mobile than Ryan proportionally
- Pattern of multiple short trips per day rather than one long absence
- Observed same-day pattern: short morning trip (~45 min), midday trip (~1 hr), longer evening out (~4 hrs)
- Consistent departure around 8–8:30 AM for short morning errand
- Not consistently home during afternoon hours
- Recent transition log: left 8:19 AM back 9:05 AM, left 11:30 AM back 12:41 PM, left 4:52 PM back 8:59 PM

### Home Alone / Vulnerability Windows
Based on presence data, highest-risk windows for property being unoccupied:
- 11:00 AM – 2:00 PM — both adults may be away simultaneously
- 4:00 PM – 7:00 PM — Kirstin frequently away, Ryan sometimes away

### Presence Device Notes
- device_tracker.daddy_ipad — always home, NOT a valid presence sensor
- device_tracker.scout — always home (dog tracker), NOT a valid presence sensor
- FordPass trackers are valid secondary presence signals but lag GPS updates

---

## 6. FARM & HOMESTEAD OPERATIONS

### Egg Collection
- Observed daily max: 10 eggs/day (April 22–23)
- Partial day count: 6 eggs by mid-morning April 24
- Scripts: script.log_egg_collection, script.add_1_egg, script.add_2_eggs
- Nightly reset: automation.egg_nightly_reset (currently unavailable — needs fix)
- Chicken door (~35 opens/10 days) implies approximately 3–4 farm visits per day

### Dogs
- Names: Xena, Scout
- Scout tracked via device_tracker.scout — never leaves property
- Dog feeding tracked via counter.dogs_fed_count — daily reset automation active
- Xena documented nighttime illness (3 AM front door open) — do not treat single overnight front door open as immediate high-alert

### Dropbox / Mail
- binary_sensor.dropbox_sensor_open — 34 opens over 10 days (~3/day)
- Package counter: automation.dropbox_increment_package_count
- Daily counter reset: automation.daily_reset_dropbox_and_dogs_fed_counters

---

## 7. ENVIRONMENTAL BASELINE (Ecowitt GW3000B)

### Temperature
| Metric | Value |
|---|---|
| Outdoor min (10 days) | 33.1°F |
| Outdoor max (10 days) | 89.7°F |
| Outdoor average | 66.5°F |
| Indoor (electronics space) min | 68.5°F |
| Indoor (electronics space) max | 89.4°F — ACTION REQUIRED |
| Indoor (electronics space) average | 78.7°F |

> Action required: Electronics/server space regularly exceeds 87°F, peaked at 89.4°F. Hardware risk. Alert automation needed above 85°F with escalation above 90°F.

### Humidity
- Range: 26.9% – 99.0%
- Average: 61.4%
- 99% peak indicates rain event or condensation episode

### Soil Moisture
| Channel | Min | Max | Avg | Notes |
|---|---|---|---|---|
| Channel 2 | 11.5% | 47.0% | 28.7% | Dries faster — different drainage or sun exposure |
| Channel 3 | 11.0% | 50.5% | 28.3% | Similar range, slightly wetter |

- Alert threshold: below 15% = irrigation needed, above 45% = oversaturated

### Solar / Irradiance
- Peak solar radiation: 943.7 W/m²
- Average: 250.2 W/m² (includes overnight zeros)
- Solar system net-exporting ~6.3 kW at time of analysis

### Wind
- Average: 1.7 mph | Max observed: 7.2 mph

---

## 8. ENERGY PROFILE

| Metric | Value |
|---|---|
| House Total Power at snapshot | -6,348W (net solar export) |
| Solar Monitor A2 | 3,168W |
| Solar Monitor B2 | 3,180W |
| Enphase Envoy current production | 5.004 kW |
| Grid carbon intensity | 51.49% fossil fuel (Electricity Maps) |

- InfluxDB contains ~2.2 million energy data points from April 14 onward
- Solar Monitor records every 15 seconds

---

## 9. MESHTASTIC / GALLBUSH MESH NETWORK

### Active Nodes
| Node Name | Entity | Node ID | Last Heard | SNR |
|---|---|---|---|---|
| GALLBUSH-BUTLER | meshtastic.gateway_btlr | 1660533308 | Gateway (continuous) | — |
| GALLBUSH-BACKYARD (House) | sensor.meshtastic_btlr_256319905_node_snr | 256319905 | Active daily | 5.5 dB |
| GALLBUSH-COMMAND | sensor.meshtastic_btlr_4055693984_node_snr | 4055693984 | Active daily | 6.5 dB |
| GALLBUSH-CATDAD | sensor.meshtastic_btlr_895847928_node_snr | 895847928 | Active daily | 6.25 dB |
| GALLBUSH-KITTYMAMA | sensor.meshtastic_btlr_260763213_node_snr | 260763213 | Active daily | 6.25 dB |
| GALLBUSH-FORD | sensor.meshtastic_btlr_3872920526_node_snr | 3872920526 | Active daily | 6.25 dB |
| GALLBUSH-GATE | sensor.meshtastic_btlr_3772939419_node_snr | 3772939419 | Active daily | Active |

- Position broadcast interval: 15 minutes (smart positioning enabled)
- All nodes healthy SNR: 5.5–6.5 dB
- Gateway: CLIENT role, EST timezone, serial D1L connection to Butler

---

## 10. SECURITY BASELINE SIGNATURES

### Normal — Do Not Alert
- Garage motion any time between 3 AM and midnight
- Front door open between 5:00–11:30 AM (morning dog routine)
- Front door open between 5:00–9:30 PM (evening dog shutdown)
- Stairs sensor active between 6 AM and 11 PM
- Garage side door and laundry door active between 5 AM and 11 PM
- Chicken door active at ~6 AM, ~noon, ~7 PM
- Gate motion between 6 AM and 10 PM when presence indicates someone home
- Single front door open between 11:30 PM and 4:30 AM (dog illness — notify only, easy dismiss)

### Tier 1 — Notify Only (no audio)
- Any exterior door open between 11:30 PM and 4:30 AM
- Gate motion between 10 PM and 6 AM
- Driveway motion between midnight and 5 AM
- Front door open outside expected windows when both adults show as away
- Ryan's truck doors unlocked while vehicle is home and unoccupied

### Tier 2 — Active Alert (notification + TTS announcement on camera speakers)
- Gate motion at night followed by driveway motion within 5 minutes
- Any exterior door open (except single front door) between midnight and 4 AM
- Garage side door or kitchen back door open with no presence detected
- Camera person/vehicle detection at gate between 11 PM and 5 AM

### Tier 3 — Full Response (siren + notification + TTS + lights)
- Interior door or motion trigger with alarm armed and no known occupant
- Multiple exterior sensors triggering in sequence: gate → driveway → door
- Alarm panel breach with no recent disarm

---

## 11. AIR QUALITY (5 Govee H7129 Purifiers)

| Location | Entity | AQI (1–5) | Filter Life | Notes |
|---|---|---|---|---|
| Den | sensor.purifier_den_air_quality | 1 (Excellent) | 100% | Good |
| Upstairs | sensor.purifier_upstairs_air_quality | 1 (Excellent) | 17% | REPLACE SOON |
| Master | sensor.purifier_master_air_quality | 2 (Good) | 29% | Monitor |
| Mama's Room | sensor.purifier_mama_air_quality | 2 (Good) | 55% | OK |
| Garage | sensor.purifier_garage_air_quality | 3 (Fair) | 100% | Filter new but air quality lower — expected for garage |

- binary_sensor.h51278c47_motion = air purifier motion/proximity sensor (location TBD — likely den or master). NOT security-relevant. Exclude from all security logic.

---

## 12. VEHICLE STATUS

| Vehicle | Lock Entity | Tracker | Battery | Notes |
|---|---|---|---|---|
| Ryan's Truck | lock.fordpass_1ftfw1ed1pfb14848_doorlock | device_tracker.fordpass_1ftfw1ed1pfb14848_tracker | 34% (low) | Doors UNLOCKED when home — action item |
| Kirstin's Truck | lock.fordpass_5lmpj8k43sj920530_doorlock | device_tracker.fordpass_5lmpj8k43sj920530_tracker | 82% | Doors locked |

- Ryan's truck 12V battery at 34% — monitor
- Automation opportunity: notify when Ryan's truck is unlocked and home/unoccupied

---

## 13. KNOWN DATA GAPS & ACTION ITEMS

| Item | Priority | Notes |
|---|---|---|
| Fix HA recorder retention (purge_keep_days: 365) | High | Currently losing data after ~10 days |
| Fix Reolink camera motion binary sensors | High | All 5 showing unavailable — broken webhook |
| Identify/rename binary_sensor.h51278c47_motion | High | Confirmed air purifier — label it and exclude from security |
| Add electronics space temp alert | High | 87–89°F regularly, no automation |
| Fix automation.egg_nightly_reset | Medium | Currently unavailable |
| Fix master_fans_all and jenny_fans_all | Medium | Showing unavailable |
| Re-run baseline analysis mid-May 2026 | High | Spring break data skews this document |
| Replace upstairs purifier filter | Medium | At 17% |
| Build Phase 1: layered security automations | High | Infrastructure ready, logic not built |
| Build Phase 2: Gallbush environmental dashboard | Medium | All data available |
| Build Phase 3: dual-user dashboard | Medium | Ryan + Kirstin views |

---

## 14. REVISION HISTORY

| Version | Date | Author | Notes |
|---|---|---|---|
| 1.0 | 2026-04-24 | Ryan H. / Claude (Session 6) | Initial baseline — spring break data caveat applies |
| 1.1 | TBD | — | Revise with clean May 2026 data |
