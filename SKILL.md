---
name: geoskill-fire-weather-index
description: '基于加拿大 FWI 系统，由气象要素（温度/湿度/风速/降水）逐日递推计算 FFMC、DMC、DC、ISI、BUI、FWI 六分量，输出火险等级栅格与时序'
---

# 火险天气指数 | Fire Weather Index

This skill implements the **Canadian Forest Fire Weather Index System** (Van Wagner 1987). From daily noon meteorological observations (air temperature °C, relative humidity %, wind speed km/h, 24h precipitation mm), six components are recursively updated day by day: FFMC (Fine Fuel Moisture Code), DMC (Duff Moisture Code), DC (Drought Code), ISI (Initial Spread Index), BUI (Buildup Index), and FWI (Fire Weather Index). The recurrence formulas are consistent with the open-source cffdrs library.

FFMC is highly sensitive to precipitation (a single heavy rainfall event can bring it down quickly); DMC/DC are cumulative drought indicators that grow day by day with potential evapotranspiration; ISI/BUI/FWI jointly reflect spread potential and energy release. The final outputs are the final-day six-component rasters, a fire danger class raster (Low/Moderate/High/Very High/Extreme), and a daily spatial-mean time series JSON.

Application scenarios: forest and grassland fire danger rating forecasting, drought–fire coupling analysis, and post-hoc review of the meteorological context of historical fires.

## Dependencies / 依赖

```bash
pip install 'numpy' 'rasterio' 'scipy'
```

## Usage / 使用方法

### Basic usage

```bash
python geoskill-fire-weather-index.py --bbox 116.0 39.0 117.0 40.0 [other options]
```

### Example 1 (synthetic data, offline)

```bash
python geoskill-fire-weather-index.py --bbox 116.0 39.0 117.0 40.0 --synthetic --output-dir ./out
```

### Example 2 (bbox only; auto-synthesized, offline)

```bash
python geoskill-fire-weather-index.py --bbox 121.0 31.0 122.0 32.0 --n-dates 45 --output-dir ./out
```

### Example 3 (specify the number of time steps and silence)

```bash
python geoskill-fire-weather-index.py --bbox 116.0 39.0 117.0 40.0 --synthetic --n-dates 60 --output-dir ./out --quiet
```

### Example 4 (real meteorological raster input; 4 bands = temperature / humidity / wind speed / precipitation)

```bash
python geoskill-fire-weather-index.py --input meteo_day.tif --output-dir ./out
```

### Example 5 (tiny area)

```bash
python geoskill-fire-weather-index.py --bbox 116.39 39.90 116.40 39.91 --output-dir ./out --quiet
```

## Output / 输出

| File | Format | Description |
|---|---|---|
| `fwi_components.tif` | GeoTIFF | Final-day six-component stack (6 bands: FFMC/DMC/DC/ISI/BUI/FWI) |
| `fwi_danger_class.tif` | GeoTIFF | Final-day fire danger class (1=Low … 5=Extreme) |
| `fwi_timeseries.json` | JSON | Daily six-component spatial-mean time series |
| `output-manifest.json` | JSON | Run manifest |

## Data Source / 数据源 / Source

- **Real mode**: local multi-band meteorological GeoTIFF (4 bands = temperature / humidity / wind speed / precipitation).
- **Synthetic mode** (`--synthetic` or bbox-only): locally generates a physically consistent meteorological time series containing "dry, hot, windy → heavy rainfall" events; no network required.

## Privacy / 隐私声明 / Privacy

- Runs offline by default; `--synthetic` mode requires no network at all.
- All processing is performed locally; user data is never uploaded.

## License / License

MIT

---

> 中文完整版：需要时再读 [references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)，不要默认全量加载。
