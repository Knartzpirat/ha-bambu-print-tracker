# Bambu Lab Print Status & Energy Tracker

A Home Assistant blueprint to monitor Bambu Lab 3D prints with interactive mobile notifications for progress, completion, and failures — including energy tracking, cost calculation, print logging, filament tracking, nozzle maintenance reminders, cumulative cost/energy counters, and multi-language support (Bambu H2D supported).

<a href="https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2FKnartzpirat%2Fha-bambu-print-tracker" target="_blank" rel="noreferrer noopener"><img src="https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg" alt="Open your Home Assistant instance and show the add app repository dialog with a specific repository URL pre-filled." /></a>

---

## Features

### Notifications

The blueprint sends **5 types of notifications**, all prefixed with your custom printer name:

#### 1. Progress Notification (Mobile Push)
Sent during printing at configurable intervals (e.g. every 5 % or 10 %).

> **Bambu Lab – 3D Print Update | Benchy**
> Print progress | 45 % ⏳ Remaining: 1h 23m

- Live progress bar on Android
- Camera snapshot or cover image
- Up to 3 action buttons (open apps, URIs, or custom actions)
- **Smart Snooze:** Mute via text input directly on your phone (enter minutes). Uses `input_datetime` helper for safe snoozing (automation stays active) or temporarily disables the automation.
- **Quiet Hours:** Suppress progress notifications during configurable time windows. Completion/failure alerts are always sent.

#### 2. Print Complete Notification (Mobile Push)
Sent when a print finishes successfully.

> **Bambu Lab – 3D Print Complete – Benchy**
> ⏱️ Duration: 47 min
> 📊 Filament usage: 12.3 g Energy: 0.15 kWh
> 💰 Cost breakdown
> Filament: €0.25 · Energy: €0.04
> Total cost: €0.29

#### 3. Print Failed Notification (Mobile Push)
Sent when a print fails. Includes the progress percentage at failure.

> **Bambu Lab – 3D Print Failed – Benchy**
> ⚠️ Failed at 73%
> ⏱️ Duration: 34 min
> 📊 Filament usage: 9.0 g Energy: 0.11 kWh
> 💰 Filament: €0.18 · Energy: €0.03 · Total cost: €0.21

#### 4. Nozzle Maintenance Reminder (Mobile Push)
Sent after a print when cumulative nozzle hours exceed the configured maintenance interval.

> **🔧 Bambu Lab – Nozzle maintenance due – Left Nozzle**
> Left Nozzle has reached 205.3h (Interval: 200h). Please clean or replace nozzle.
> Buttons: ✅ Maintenance done · ⏰ Remind in... (text input for hours)

#### 5. Print Log (Persistent HA Notification + Logbook)
Optional detailed log entry saved to the HA Logbook and/or shown as a persistent notification.

> **📋 Bambu Lab – Print log – Benchy**
> ✅ Benchy | Date: 2026-04-20 14:35 | Duration: 0h 47m | Progress: 100%
> | Filament: 12.3g | Materials: PLA Basic, PLA Silk | Nozzle size: 0.4 mm
> | Nozzle type: Left Nozzle=Hardened Steel, Right Nozzle=Brass
> | Ø Bed temp.: 58.2°C | Ø Left Nozzle: 213.5°C | Ø Right Nozzle: 210.1°C
> | Print bed: Textured PEI | Filament cost: €0.25 | Energy: 0.15 kWh (€0.04) | Total: €0.29

### Notification Settings

| Setting | Description |
|---|---|
| **Printer Name** | Custom name shown in all notification titles (default: "Bambu Lab") |
| **Notification Devices** | One or more mobile devices to receive notifications |
| **Progress Interval** | How often to send progress updates (1–50 %, default: 5 %) |
| **Quiet Hours** | Start/end time to suppress progress notifications |
| **Action Button 1** | Configurable title + URI (e.g. open Bambu App) |
| **Action Button 2** | Camera view or fallback URI |
| **Action Button 3** | Disabled / Mute Progress (text input) / Custom Action |
| **Snooze Helper** | `input_datetime` for safe progress muting |

### Cost & Energy Tracking
- **Filament Costs:** Automatic calculation based on print weight and configurable cost per kg.
- **Energy Costs:** Integration with smart plugs (kWh sensors). Supports both static pricing and dynamic price sensors (e.g. Tibber, aWATTar).
- **Cumulative Counters:** Optional `input_number` helpers to track total energy consumption (kWh) and total costs (filament + energy) across all prints.

### Print Logging
- **Detailed Log Entries:** Logs each completed/failed print to the HA Logbook with duration, weight, materials, nozzle types, temperatures, costs, and more.
- **Filament Tracking:** Automatically collects all filament types used during a print via the "Active Slot" sensor — deduplicated and stored in an `input_text` helper.
- **Persistent HA Notifications:** Optionally show log entries as persistent notifications in the HA notification center.
- **Optional Sensors:** Nozzle size, bed temperature, nozzle temperatures, bed type, and tool module status.

### Nozzle Maintenance (Bambu H2D)
- **Operating Hours Tracking:** Counts hours per nozzle individually based on target temperature exceeding a configurable threshold (default: 100 °C). Resolution: 0.01 h (36 seconds).
- **Nozzle Type Tracking:** Configurable nozzle type per nozzle (hardened steel, brass, stainless steel, copper, or custom). Logged in print log entries.
- **Maintenance Reminders:** Configurable interval per nozzle with mobile notification including "Done" and "Snooze" buttons.
- **H2D Labels:** Optionally replaces "Nozzle 1/2" with "Left/Right Nozzle" labels.

### Internationalization
- **Multi-Language:** All notifications, log entries, and button labels are available in 🇩🇪 German, 🇬🇧 English, 🇫🇷 French, and 🇪🇸 Spanish. Additional languages can be easily added to the translation dictionary.
- **Configurable Currency:** Currency symbol for cost display is freely configurable (€, $, £, CHF, etc.).
- **Localized Date Formats:** Date format in logs adapts to the selected language.

---

## Requirements

1. **Bambu Lab Integration** — installed via HACS or official methods.
2. **Mobile App Integration** — required for actionable notifications.

### Optional Helpers

| Helper | Type | Purpose |
|---|---|---|
| Snooze Helper | `input_datetime` (Date + Time) | Safe snooze for progress muting |
| Start kWh Helper | `input_number` (min: 0, max: 999999, step: 0.01, unit: kWh) | Stores energy meter reading at print start |
| Total Energy Helper | `input_number` (min: 0, max: 999999, step: 0.01, unit: kWh) | Cumulative energy consumption across all prints |
| Total Cost Helper | `input_number` (min: 0, max: 999999, step: 0.01) | Cumulative total costs across all prints |
| Filament Log Helper | `input_text` (max 255) | Collects filament names used during a print |
| Print Log Helper | `input_text` (max 255) | Stores last log entry & links logbook entries |
| Nozzle 1/2 Heating Start | `input_number` (min: 0, max: 9999999999, step: 1) | Stores Unix timestamp when nozzle starts heating |
| Nozzle 1/2 Hours | `input_number` (min: 0, max: 10000, step: 0.01, unit: h) | Accumulated operating hours per nozzle |
| Nozzle 1/2 Snooze | `input_datetime` (Date + Time) | Snooze maintenance reminders |

---

## Installation

1. Click the **Import Blueprint** button above or copy the URL of the YAML file.
2. In Home Assistant, navigate to **Settings → Automations & Scenes → Blueprints**.
3. Click **Import Blueprint** and paste the URL.
4. Create an automation from the imported blueprint and map your printer sensors to the inputs.
5. Create the required helpers as needed (see table above).

---

## Contributing

For bug reports or feature requests, please open an issue in this repository.

---

## Credits

Based on the original blueprint by [@willhaggan](https://github.com/willhaggan/ha-bambu-print-tracker) — thank you for the great foundation! 🙏
