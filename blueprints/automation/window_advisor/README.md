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
- **Optional Alexa announcement** — also speak the recommendation on Echo
  devices via the built-in Alexa Devices integration, toggled independently
  of the phone notification.
- **Alexa quiet hours** — restrict the Alexa announcement to a time window
  (e.g. 08:00–22:00) so it doesn't speak overnight. Phone notifications are
  unaffected.
- **Customizable messages** — override the title/message text for both the
  open and close recommendation (used for both the phone notification and
  the Alexa announcement), with today's wording as the default.

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
   - Alexa Announcements (optional, see below)

### Optional: Alexa announcements

Uses the built-in [Alexa Devices](https://www.home-assistant.io/integrations/alexa_devices/)
integration, which exposes a "Speak" and "Announce" notify entity per Echo
device (e.g. `notify.echo_dot_kitchen_announce`) — no HACS or custom
integration needed.

1. Confirm your Echo device(s) have an `notify.*_announce` (or `*_speak`)
   entity — check under Settings → Devices & Services → Alexa Devices →
   your device.
2. In the blueprint's **Alexa Announcements** section, enable
   "Enable Alexa Announcement" and select the notify entity/entities to
   announce on.
3. Set **Earliest Announcement Time** / **Latest Announcement Time**
   (defaults to 08:00–22:00) to keep Alexa quiet outside those hours. The
   phone notification still fires immediately regardless of this window.

### Optional: Customize the notification text

In the **Notifications** section, the four template fields (Open/Close
Title, Open/Close Message) control what's shown on your phone and spoken
by Alexa. They default to the built-in wording, and support Jinja — two
variables are available: `{{ outdoor_max }}` and `{{ indoor_min }}` (both
°C, already rounded to 1 decimal).

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
