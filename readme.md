# Miner Bot

A Python project for automating resource gathering in a mobile game and analyzing the resulting harvest logs. The bot communicates with an Android device through Android Debug Bridge (ADB), uses OpenCV template matching to locate ore, and records each harvest in a CSV file.

> **Status:** Personal project. The bot currently uses fixed screen coordinates and image templates; it may need adjustments for a different device, resolution, interface, or game version.

## Features

- Searches for ore labels in screenshots captured from an Android device.
- Uses a four-state loop to search, aim, approach, and collect.
- Classifies collected nodes using image templates and a color check.
- Saves timestamped harvest logs as CSV.
- Generates per-log and combined performance dashboards from those CSV files.

## Repository contents

| Path | Purpose |
| --- | --- |
| `vision4.py` | ADB controls, image matching, harvesting loop, and CSV logging |
| `analysis.py` | CSV analysis and dashboard generation |
| `templates/` | Image templates used by the bot |
| `statistics/` | Runtime harvest logs; example CSV files are included |
| `analytics_output/` | Generated dashboard images, including example outputs |

## Requirements

- Python 3
- Android Debug Bridge (`adb`) installed and available on your `PATH`
- An Android device connected to ADB, with USB debugging enabled and authorized
- The image templates in `templates/`

Install the Python dependencies from the repository root:

```powershell
python -m pip install -r requirements.txt
```

Check that ADB can see the device:

```powershell
adb devices
```

The device should appear as `device`, not `unauthorized` or `offline`.

## Run the bot

Run from the repository root so the bot can find its templates:

```powershell
python vision4.py
```

The bot pauses for five seconds before starting. Stop it with `Ctrl+C`. It writes harvest records to a timestamped file in `statistics/`.

The bot is configured for a **2340 × 1080 landscape display** and fixed touch coordinates. Those values are defined near the top of `vision4.py`; the code does not currently provide a configuration interface.

## Generate analytics

After collecting one or more logs, run:

```powershell
python analysis.py
```

The analytics script reads files matching `statistics/stats_*.csv` and saves a combined dashboard and per-log dashboards in `analytics_output/`. It uses pandas to process the logs and Matplotlib to plot ore counts, estimated revenue, and hourly harvest rates. Example output for all recorded sessions is shown below:

![Global performance dashboard](analytics_output/global_performance_dashboard.png)

This combined dashboard aggregates results across every session in `statistics/`, making it easy to compare total ore output, revenue estimates, and hourly efficiency trends over time. Each session can also be reviewed individually in the per-log PNG files under `analytics_output/session_analytics_output/`.

The expected CSV columns are:

| Column | Description |
| --- | --- |
| `timestamp` | Date and time of the harvest |
| `ore_type` | `gold`, `silver`, `bronze`, `unknown`, or `stolen` |
| `runtime_minutes` | Elapsed runtime in minutes |

## Templates and data

The bot expects these files in `templates/`:

- `ore_template.png` — ore-label detection
- `gold_template.png`, `silver_template.png`, and `bronze_template.png` — ore classification

Template matching depends on the appearance and resolution of the game interface. You may need to capture and prepare your own templates. Before redistributing any game-derived images, make sure you have permission to do so.

Harvest logs and dashboards are generated locally. Avoid publishing personal logs or other data you do not intend to share.

## Safety and project status

This is an unofficial personal project, not affiliated with or endorsed by the game developer. Automated input may conflict with a game's terms of service. Review the applicable rules and use the bot at your own risk.

The project currently has no automated test suite. Changes to device control or image matching should be tested carefully with the target device.
