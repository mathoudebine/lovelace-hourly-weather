# Hourly Weather Card by [@decompil3d](https://www.github.com/decompil3d)

An hourly weather card for Home Assistant. Visualize upcoming weather conditions as a colored horizontal bar.

[![GitHub Release][releases-shield]][releases]
[![License][license-shield]](LICENSE.md)
[![hacs_badge](https://img.shields.io/badge/HACS-Default-orange.svg?style=for-the-badge)](https://github.com/hacs/integration)

![Project Maintenance][maintenance-shield]
[![GitHub Activity][commits-shield]][commits]

[![Lovelace Hourly Weather](https://img.shields.io/endpoint?url=https://dashboard.cypress.io/badge/simple/o468vw/main&style=for-the-badge&logo=cypress)](https://dashboard.cypress.io/projects/o468vw/runs)

![Screenshot of Hourly Weather card](hourly-weather.png)

## Installation

### Easiest method:

✨ Install via HACS

### Alternative method:

1. Download `hourly-weather.js` from the [Releases][releases] page
2. Upload to `/www/hourly-weather/hourly-weather.js` (via Samba, File Editor, SSH, etc.)
3. Visit the Resources page in your Home Assistant install and add `/hourly-weather/hourly-weather.js` as a
   JavaScript Module.
   [![Open your Home Assistant instance and show your dashboard resources.](https://my.home-assistant.io/badges/lovelace_resources.svg)](https://my.home-assistant.io/redirect/lovelace_resources/)
4. Refresh your browser

## Usage

This card will show in the "Add card" modal. It has a GUI editor for configuring settings.

If you prefer YAML, here is a sample config:

```yaml
type: custom:hourly-weather
entity: weather.my_hourly_weather_entity
num_segments: 18 # optional, defaults to 12
name: Next 18 hours # optional, defaults to "Hourly Weather"
```

### Choosing the right entity

This card is focused on displaying hourly weather data. If you try selecting a weather entity that provides daily
forecasts, it will show a warning. I've tested with the OpenWeatherMap integration using the `onecall_hourly` mode and
that works very well.

> ℹ️ NOTE: If your selected weather entity provides forecasts in increments of greater than one hour at a time, each
> segment of the bar will be for one segment, not one hour.

If you already use OpenWeatherMap for daily data, you can add a second integration of the same
component for hourly -- just adjust the latitude or longitude a tiny bit (i.e. change the last decimal by 1).
Otherwise, the integration may complain of a duplicate unique ID.

## Options

| Name              | Type   | Requirement  | Description                                               | Default             |
| ----------------- | ------ | ------------ | --------------------------------------------------------- | ------------------- |
| type              | string | **Required** | `custom:hourly-weather`                                   |                     |
| entity            | string | **Required** | Home Assistant weather entity ID.                         |                     |
| name              | string | **Optional** | Card name (set to `null` to hide)                         | `Hourly Weather`    |
| icons             | bool   | **Optional** | Whether to show icons instead of text labels              | `false`             |
| num_segments      | number | **Optional** | Number of forecast segments to show (even integer >= 2)   | `12`                |
| ~~num_hours~~     | number | **Optional** | _Deprecated:_ Use `num_segments` instead                  | `12`                |
| offset            | number | **Optional** | Number of forecast segments to offset from start          | `0`                 |
| label_spacing     | number | **Optional** | Space between time/temperature labels (even integer >= 2) | `2`                 |
| colors            | object | **Optional** | Set colors for all or some conditions                     |                     |
| hide_hours        | bool   | **Optional** | Whether to hide hour labels under the bar                 | `false`             |
| hide_temperatures | bool   | **Optional** | Whether to hide temeratures under the bar                 | `false`             |
| show_wind         | bool   | **Optional** | Whether to show wind speed and direction under the bar    | `false`             |
| tap_action        | object | **Optional** | Action to take on tap                                     | `action: more-info` |
| hold_action       | object | **Optional** | Action to take on hold                                    | `none`              |
| double_tap_action | object | **Optional** | Action to take on double tap                              | `none`              |
| language          | string | **Optional** | Language to use for card (overrides HA & user settings)   |                     |

### Templating

The following options allow Home Assistant Jinja templates as values:

- `num_segments`
- `offset`
- `label_spacing`

You may use any valid template expression that produces a string or number. For example, you can define offset based on
the current time of day so that you see the next day's weather:

```yaml
name: Tomorrow
num_segments: 24
offset: |
  {{ 24 - now().hour }}
label_spacing: 4
```

Or maybe show the rest of today's weather:

```yaml
name: Today
num_segments: |
  {{ 24 - now().hour }}
label_spacing: |
  {% set segments = 24 - now().hour %}
  {{ 4 if segments > 12 else 2 }}
```

## Action Options

| Name            | Type   | Requirement  | Description                                                                                        | Default     |
| --------------- | ------ | ------------ | -------------------------------------------------------------------------------------------------- | ----------- |
| action          | string | **Required** | Action to perform (more-info, toggle, call-service, navigate url, none)                            | `more-info` |
| navigation_path | string | **Optional** | Path to navigate to (e.g. /lovelace/0/) when action defined as navigate                            | `none`      |
| url             | string | **Optional** | URL to open on click when action is url. The URL will open in a new tab                            | `none`      |
| service         | string | **Optional** | Service to call (e.g. media_player.media_play_pause) when action defined as call-service           | `none`      |
| service_data    | object | **Optional** | Service data to include (e.g. entity_id: media_player.bedroom) when action defined as call-service | `none`      |
| haptic          | string | **Optional** | Haptic feedback _success, warning, failure, light, medium, heavy, selection_                       | `none`      |
| repeat          | number | **Optional** | How often to repeat the `hold_action` in milliseconds.                                             | `none`      |

## Color Options

`colors` is specified as an object containing one or more of the keys listed below and values that are valid CSS
colors. Invalid color values will be discarded and will trigger a warning.

Some conditions will default to whatever the value is of some other condition. For example, `fog` will default to
whatever `cloudy` is.

| Key               | Default                |
| ----------------- | ---------------------- |
| `clear-night`     | `#000`                 |
| `cloudy`          | `#777`                 |
| `fog`             | same as `cloudy`       |
| `hail`            | `#2b5174`              |
| `lightning`       | same as `rainy`        |
| `lightning-rainy` | same as `rainy`        |
| `partlycloudy`    | `#b3dbff`              |
| `pouring`         | same as `rainy`        |
| `rainy`           | `#44739d`              |
| `snowy`           | `#fff`                 |
| `snowy-rainy`     | same as `partlycloudy` |
| `sunny`           | `#90cbff`              |
| `windy`           | same as `sunny`        |
| `windy-variant`   | same as `sunny`        |
| `exceptional`     | `#ff9d00`              |

### Sample colors configuration

```yaml
colors:
  sunny: '#bbccee' # note that hex colors must be quoted
  snowy-rainy: rgba(255, 255, 255, 0.8) # rgba works (and hsla too)
  exceptional: red # as do valid CSS color names
```

## Upgrades

### Version 3 ➡️ 4

In version 4.x, the `num_hours` option was deprecated in favor of `num_segments`. This simplifies the card and makes it
clear that it operates on whatever size forecast segment your entity provides. The `num_hours` option still works, but
`num_segments` takes precedence, if set. `num_hours` will be removed in a later major version of the card.

[commits-shield]: https://img.shields.io/github/commit-activity/y/decompil3d/lovelace-hourly-weather.svg?style=for-the-badge
[commits]: https://github.com/decompil3d/lovelace-hourly-weather/commits/master
[devcontainer]: https://code.visualstudio.com/docs/remote/containers
[license-shield]: https://img.shields.io/github/license/decompil3d/lovelace-hourly-weather.svg?style=for-the-badge
[maintenance-shield]: https://img.shields.io/maintenance/yes/2022.svg?style=for-the-badge
[releases-shield]: https://img.shields.io/github/release/decompil3d/lovelace-hourly-weather.svg?style=for-the-badge
[releases]: https://github.com/decompil3d/lovelace-hourly-weather/releases
