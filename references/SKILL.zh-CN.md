# SAR 城市制图 | SAR Urban Mapping

从单时相 SAR 后向散射（σ⁰，线性功率）中提取城市 / 建成区。物理依据：

- **高后向散射**：城市建筑形成大量二面角 / 三面角反射器，在 C/X 波段 σ⁰
  显著高于农田与水体。
- **高纹理**：建筑布局造成强空间异质性，GLCM 对比度高；农田 / 水面纹理均匀。

方法流程：

1. **阈值分割**：`--threshold auto` 用 Otsu 最大类间方差自动确定 σ⁰ 门限，
   也可传入固定线性 σ⁰ 门限。
2. **纹理辅助**（`--texture true`）：叠加 GLCM 对比度门限（Otsu），抑制
   高 σ⁰ 但纹理均匀的裸土 / 静水误检。
3. **形态学闭运算**：填充街区空洞、连通城市斑块，并轻微开运算去噪。

## 依赖

```bash
pip install numpy rasterio scipy
```

## 使用方法

### 基本用法（仅给 bbox，自动合成）

```bash
python geoskill-sar-urban-mapping.py --bbox 116.0 39.0 117.0 40.0 --output-dir ./out
```

### 示例 1：合成数据（离线）

```bash
python geoskill-sar-urban-mapping.py --bbox 116 39 117 40 --synthetic --output-dir ./syn
```

### 示例 2：真实 SAR σ⁰ 影像

```bash
python geoskill-sar-urban-mapping.py --input sigma0_linear.tif --output-dir ./real
```

### 示例 3：固定阈值 + 关闭纹理

```bash
python geoskill-sar-urban-mapping.py --input sigma0.tif --threshold 0.05 --texture false --output-dir ./fixed
```

### 示例 4：另一区域（上海）

```bash
python geoskill-sar-urban-mapping.py --bbox 121.0 31.0 122.0 32.0 --output-dir ./shanghai --quiet
```

## 输出

| 文件 | 格式 | 说明 |
|---|---|---|
| `urban_mask.tif` | GeoTIFF (uint8) | 城市范围二值掩膜（1=城市，0=非城市），EPSG:4326 |
| `urban_statistics.json` | JSON | 城市像元数 / 占比 / 面积(km²)、阈值(dB)等 |
| `output-manifest.json` | JSON | 运行清单 |

## 数据源 / Source

- **真实模式**：本地 SAR σ⁰ GeoTIFF（线性功率，可由 Sentinel-1 GRD 经辐射定标得到）。
- **合成模式**：本地生成低值农田 / 水体背景 + 高值高纹理城市斑块，无外部数据源。

## 隐私声明 / Privacy

- 默认完全离线运行，`--synthetic` 无任何网络。
- 所有处理本地完成，不上传用户数据。

## License

MIT
