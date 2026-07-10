[← All blueprints](../../../README.md)

# 🌡️ Window Advisor

A Home Assistant blueprint that compares your indoor and outdoor temperature
sensors and notifies you when it's worth opening the windows to cool the
house down — or closing them to keep the cool air trapped inside.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fq8webmaster%2Fha-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fwindow_advisor%2Fwindow_advisor.yaml)

## Features

- **Multi-sensor comparison** — select any number of indoor and outdoor
  temperature sensors; the automation uses the outdoor max vs. indoor min.
- **Debounce** — the recommendation must hold steady for a configurable
  number of minutes before you're notified, so brief sensor noise doesn't
  trigger false alerts.
- **Optional periodic recheck** — re-evaluates on a fixed interval as a
  safety net, independent of sensor state changes.
- **No repeat spam** — periodic rechecks only notify if the recommendation
  actually changed since the last notification sent.
- **Multi-device notifications** — notify any number of Companion App
  devices in one automation.

## Requirements

- One or more outdoor temperature sensors (`device_class: temperature`)
- One or more indoor temperature sensors (`device_class: temperature`)
- The [Home Assistant Companion App](https://companion.home-assistant.io/)
  installed on the device(s) you want notified
- An `input_text` helper to track the last notification sent (see setup
  below)

## Setup

1. **Create the helper.** Go to **Settings → Devices & Services → Helpers →
   + Create Helper → Text**. Name it something like `Window Advisor Last
   Decision`. You'll select this helper in the blueprint's Notifications
   section.
2. **Import the blueprint** using the button above, or manually via
   **Settings → Automations & Scenes → Blueprints → Import Blueprint** and
   paste this URL:
   ```
   https://github.com/q8webmaster/ha-blueprints/blob/main/blueprints/automation/window_advisor/window_advisor.yaml
   ```
3. **Create an automation** from the blueprint and fill in:
   - Outdoor and indoor temperature sensors
   - Devices to notify
   - The `input_text` helper you created in step 1
   - Debounce time and periodic recheck settings (optional, sensible
     defaults provided)

## How it works

| Condition | Result |
|---|---|
| Outdoor max < Indoor min | 🪟 Notification: **Open the windows** |
| Outdoor max > Indoor min | 🪟 Notification: **Close the windows** |

The comparison is evaluated both on sensor state changes (debounced) and,
optionally, on a fixed interval. Either path only sends a notification when
the recommendation differs from the last one sent, so you get a single
timely alert per state change rather than repeated nagging.

## Credits

Inspired by [smndhm/ha-blueprint-heatwave-windows](https://github.com/smndhm/ha-blueprint-heatwave-windows), MIT licensed.
