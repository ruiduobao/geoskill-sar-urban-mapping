# SAR Urban Mapping (geoskill-sar-urban-mapping)

> Urban/built-up area extraction from SAR backscatter and GLCM texture: Otsu/fixed σ⁰ thresholding + GLCM contrast texture + morphological closing, outputting a binary urban extent GeoTIFF and area statistics JSON. SAR urban/built-up mapping via backscatter threshold, GLCM texture and morphology.

---

## 1. Overview

Extracts urban / built-up areas from single-temporal SAR backscatter (σ⁰, linear power). Physical basis: - **High backscatter**: urban buildings form numerous dihedral / trihedral corner reflectors, giving σ⁰ significantly higher than cropland and water in the C/X band. - **High texture**: building layouts create strong spatial heterogeneity and high GLCM contrast, whereas cropland / water surfaces have uniform texture. Method flow: 1. **Threshold segmentation**: `--threshold auto` uses Otsu's maximum inter-class variance to determine the σ⁰ threshold automatically; a fixed linear σ⁰ threshold can also be provided. 2. **Texture assistance** (`--texture true`): combines a GLCM contrast threshold (Otsu) to suppress false positives from bare soil / calm water with high σ⁰ but uniform texture. 3. **Morphological closing**: fills holes within blocks, connects urban patches, and applies light opening to remove noise.

## 3. Quick Start

```bash
pip install -r requirements.txt
python geoskill-sar-urban-mapping.py --bbox 116 39 117 40 --synthetic --output-dir ./out
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
| `urban_mask.tif` | GeoTIFF (uint8) | Binary urban extent mask (1=urban, 0=non-urban), EPSG:4326 |
| `urban_statistics.json` | JSON | Urban pixel count / share / area (km²), threshold (dB), etc. |
| `output-manifest.json` | JSON | Run manifest |

## 6. Technical Principle

(see SKILL.md for details)

## 8. License

MIT License. See [`LICENSE`](./LICENSE) for full text.

---

> 中文完整版：[references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)
