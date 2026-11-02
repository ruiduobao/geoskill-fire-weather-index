# 火险天气指数 | Fire Weather Index

本 skill 实现**加拿大林火天气指数系统**（Canadian Forest Fire Weather Index
System，Van Wagner 1987），由每日正午气象观测（气温 ℃、相对湿度 %、风速
km/h、24h 降水 mm）逐日递推六个分量：FFMC（细可燃物湿度码）、DMC（腐殖质
湿度码）、DC（干旱码）、ISI（初始蔓延指数）、BUI（累积指数）与 FWI（火险
天气指数）。递推公式与 cffdrs 开源库一致。

FFMC 对降水高度敏感（一次强降水即可快速回落）；DMC/DC 为累积型干旱指标，
随潜在蒸散逐日增长；ISI/BUI/FWI 综合反映蔓延潜力与能量释放。最终输出末日
六分量栅格、火险等级栅格（Low/Moderate/High/Very High/Extreme）与逐日空间
均值时序 JSON。

应用场景：森林草原火险等级预报、干旱-火险耦合分析、历史火灾气象背景复盘。

## 依赖

```bash
pip install 'numpy' 'rasterio' 'scipy'
```

## 使用方法

### 基本用法

```bash
python geoskill-fire-weather-index.py --bbox 116.0 39.0 117.0 40.0 [其他参数]
```

### 示例 1（合成数据，离线）

```bash
python geoskill-fire-weather-index.py --bbox 116.0 39.0 117.0 40.0 --synthetic --output-dir ./out
```

### 示例 2（仅给 bbox，自动合成，离线）

```bash
python geoskill-fire-weather-index.py --bbox 121.0 31.0 122.0 32.0 --n-dates 45 --output-dir ./out
```

### 示例 3（指定时间步长与静默）

```bash
python geoskill-fire-weather-index.py --bbox 116.0 39.0 117.0 40.0 --synthetic --n-dates 60 --output-dir ./out --quiet
```

### 示例 4（真实气象栅格输入，4 波段=温度/湿度/风速/降水）

```bash
python geoskill-fire-weather-index.py --input meteo_day.tif --output-dir ./out
```

### 示例 5（极小区域）

```bash
python geoskill-fire-weather-index.py --bbox 116.39 39.90 116.40 39.91 --output-dir ./out --quiet
```

## 输出

| 文件 | 格式 | 说明 |
|---|---|---|
| `fwi_components.tif` | GeoTIFF | 末日六分量栈（6 波段：FFMC/DMC/DC/ISI/BUI/FWI） |
| `fwi_danger_class.tif` | GeoTIFF | 末日火险等级（1=Low … 5=Extreme） |
| `fwi_timeseries.json` | JSON | 逐日六分量空间均值时序 |
| `output-manifest.json` | JSON | 运行清单 |

## 数据源 / Source

- **真实模式**：本地多波段气象 GeoTIFF（4 波段 = 温度/湿度/风速/降水）。
- **合成模式**（`--synthetic` 或仅 `--bbox`）：本地生成含"干热大风→强降水"事件的物理一致气象时序，无需网络。

## 隐私声明 / Privacy

- 默认离线运行，`--synthetic` 模式完全无网络。
- 所有处理在本地完成，不上传用户数据。

## License

MIT
