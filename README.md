# Fire Weather Index (geoskill-fire-weather-index)

> Implements the Canadian FWI system, deriving FFMC, DMC, DC, ISI, BUI and FWI from daily meteorological elements (temperature/humidity/wind speed/precipitation), outputting fire danger class rasters and time series

---

## 1. Overview

This skill implements the **Canadian Forest Fire Weather Index System** (Van Wagner 1987), deriving six components day-by-day from daily noon meteorological observations (air temperature °C, relative humidity %, wind speed km/h, 24 h precipitation mm): FFMC (Fine Fuel Moisture Code), DMC (Duff Moisture Code), DC (Drought Code), ISI (Initial Spread Index), BUI (Buildup Index) and FWI (Fire Weather Index). The recurrence formulas are consistent with the open-source cffdrs library. FFMC is highly sensitive to precipitation (a single heavy rainfall event can quickly bring it back down); DMC/DC are cumulative drought indicators that grow daily with potential evapotranspiration; ISI/BUI/FWI jointly reflect spread potential and energy release. The final outputs are the final-day six-component raster, a fire danger class raster (Low/Moderate/High/Very High/Extreme) and a daily spatial-mean time series JSON. Application scenarios: forest and grassland fire danger rating forecasting, drought-fire coupling analysis, and review of historical fire weather backgrounds.

## 3. Quick Start

```bash
pip install -r requirements.txt
python geoskill-fire-weather-index.py --bbox 116 39 117 40 --synthetic --output-dir ./out
```

## 4. CLI Parameters

Run `python <skill>.py --help` for the full list. Common parameters:

| Parameter | Type | Description |
|---|---|---|
| `--bbox` | `float[4]` | WGS84 bounding box `min_lon min_lat max_lon max_lat` |
| `--input` | `path` | Local input file (GeoJSON/GeoTIFF/etc.) |
| `--output-dir` | `path` | Output directory (default `./output`) |
| `--synthetic` | `flag` | Use synthetic data instead of real input |
| `--quiet` | `flag` | Suppress non-essential stdout |

## 5. Input / Output

| File | Format | Description |
|---|---|---|
| `fwi_components.tif` | GeoTIFF | Final-day six-component stack (6 bands: FFMC/DMC/DC/ISI/BUI/FWI) |
| `fwi_danger_class.tif` | GeoTIFF | Final-day fire danger class (1=Low … 5=Extreme) |
| `fwi_timeseries.json` | JSON | Daily spatial-mean time series of the six components |
| `output-manifest.json` | JSON | Run manifest |

## 6. Technical Principle

(see SKILL.md for details)

## 8. License

MIT License. See [`LICENSE`](./LICENSE) for full text.

---

> 中文完整版：[references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)
