# Bambu Lab Print Status & Energy Tracker

A Home Assistant blueprint to monitor Bambu Lab 3D prints with interactive mobile notifications for progress, completion, and failures — including energy tracking, cost calculation, print logging, filament tracking, nozzle maintenance reminders, and multi-language support (Bambu H2D supported).

<a href="https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2FKnartzpirat%2Fha-bambu-print-tracker" target="_blank" rel="noreferrer noopener"><img src="https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg" alt="Open your Home Assistant instance and show the add app repository dialog with a specific repository URL pre-filled." /></a>

---

## Features

### Notifications
- **Real-Time Progress:** Adjustable update intervals (e.g. every 5% or 10%) with live progress bar.
- **Visual Feedback:** Live camera snapshots or cover images in notifications.
- **Smart Snooze:** Mute progress notifications for a custom duration via text input on your phone. Uses an `input_datetime` helper for safe snoozing (automation stays active) or falls back to temporarily disabling the automation.
- **Quiet Hours:** Suppress progress notifications during configurable time windows. Completion and failure alerts are always sent.
- **Custom Action Buttons:** Up to 3 configurable action buttons per notification (open apps, URIs, or run custom actions).
- **Multi-Device Support:** Send notifications to multiple mobile devices simultaneously.

### Cost Tracking
- **Filament Costs:** Automatic calculation based on print weight and configurable cost per kg.
- **Energy Costs:** Integration with smart plugs (kWh sensors). Supports both static pricing and dynamic price sensors (e.g. Tibber, aWATTar).

### Print Logging
- **Detailed Log Entries:** Logs each completed/failed print to the HA Logbook with duration, weight, materials, temperatures, costs, and more.
- **Filament Tracking:** Automatically collects all filament types used during a print via the "Active Slot" sensor — deduplicated and stored in an `input_text` helper.
- **Persistent HA Notifications:** Optionally show log entries as persistent notifications in the HA notification center.
- **Optional Sensors:** Nozzle size, bed temperature, nozzle temperatures, bed type, and tool module status.

### Nozzle Maintenance (Bambu H2D)
- **Operating Hours Tracking:** Counts hours per nozzle individually based on target temperature exceeding a configurable threshold (default: 100 °C). Resolution: 0.01 h (36 seconds).
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
