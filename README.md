# Week 10 Homework: ARIA v7.0 All-Weather Auditor

**Student:** 陳冠嘉  
**Course:** 遙測與空間資訊之分析與應用  
**Case Study:** Matai'an Creek barrier-lake overflow, Hualien County  
**Main notebook:** `scripts/Week10_ARIA_v70_陳冠嘉.ipynb`

## Overview

This homework upgrades the Week 9 optical workflow into an ARIA v7.0 all-weather audit by combining:

- Sentinel-1 SAR flood detection
- Sentinel-2 NDWI + cloud masking
- DEM slope-based topographic filtering
- AI strategic briefing
- a same-AOI optical-only vs fused comparison

The final notebook follows the user’s previously successful workflow pattern:

- local AOI clips stored in `HW10/data/sentinel_cache`
- DEM slope analysis based on the HW4 method (`np.gradient` on local DEM)
- all figures drawn with `matplotlib`
- all deliverables written into `HW10/output`

## Folder Structure

```text
HW10/
├── .env
├── .gitignore
├── Homework-Week10.md
├── README.md
├── requirements.txt
├── data/
│   ├── dem_20m_hualien.tif
│   ├── mataian_detections.gpkg
│   ├── validation_points.geojson
│   ├── week9_accuracy_metrics.csv
│   ├── week9_confidence_area_stats.csv
│   ├── week9_week8_comparison.csv
│   └── sentinel_cache/
├── output/
│   ├── task1_sar_before_after.png
│   ├── task1_sar_flood_panel.png
│   ├── task1_sar_area_stats.csv
│   ├── task1_threshold_comparison.csv
│   ├── task2_optical_vs_sar.png
│   ├── task2_confidence_map.png
│   ├── task2_confidence_area_stats.csv
│   ├── task3_topographic_audit.png
│   ├── task3_slope_false_positive_table.csv
│   ├── task4_ai_briefing_log.csv
│   └── task4_week9_week10_comparison.csv
└── scripts/
    └── Week10_ARIA_v70_陳冠嘉.ipynb
```

## How To Run

Use the requested conda environment:

```powershell
cd "D:\Nitro\桌面\遙測與空間資訊之分析與應用\HW\HW10"
C:\Users\Nitro\anaconda3\envs\gis\python.exe -m jupyter lab
```

Open `scripts/Week10_ARIA_v70_陳冠嘉.ipynb` and run all cells. The notebook reads `.env`, displays every figure in-place, and writes the final deliverables to `output/`.

## Reproducibility

The notebook uses `.env` for the final thresholds and local execution settings:

- `SAR_THRESHOLD=-14`
- `SAR_THRESHOLD_STRICT=-18`
- `NDWI_THRESHOLD=-0.05`
- `SLOPE_THRESHOLD=25`
- `MIN_WATER_PIXELS=50`
- `PRE_DATE_RANGE`
- `POST_DATE_RANGE`
- `SEARCH_BBOX_*`
- `S1_PRE_ITEM_ID`
- `S1_POST_ITEM_ID`
- `S2_POST_ITEM_ID`

Supporting inputs come from:

- Week10 local Sentinel-1 / Sentinel-2 AOI clips in `data/sentinel_cache`
- `dem_20m_hualien.tif` copied from the successful HW4 DEM workflow
- Week9 reference tables copied into `HW10/data` for comparison context

## Task Results

### Task 1: SAR All-Weather Flood Detection

Final SAR settings and outputs:

- Final SAR threshold: `-14 dB`
- Strict comparison threshold: `-18 dB`
- Median filter: `5x5`
- Minimum connected component: `50 pixels`
- Raw thresholded area: `161.377 ha`
- Final cleaned flood area: `140.365 ha` (`1.4037 km2`)
- Final water pixels: `14,035`
- Mean flood-zone backscatter: `-17.999 dB`

Saved outputs:

- `output/task1_sar_before_after.png`
- `output/task1_sar_flood_panel.png`
- `output/task1_sar_area_stats.csv`
- `output/task1_threshold_comparison.csv`

Interpretation: the SAR workflow preserves flood evidence even when the post-event optical scene is heavily cloud-covered.

### Task 2: Sensor Fusion Confidence Map

Fusion classes before topographic correction:

- No Detection: `12.1683 km2`
- Optical Only: `0.3552 km2`
- SAR Only (Cloudy): `1.3101 km2`
- High Confidence: `0.0522 km2`

Saved outputs:

- `output/task2_optical_vs_sar.png`
- `output/task2_confidence_map.png`
- `output/task2_confidence_area_stats.csv`

Interpretation: the SAR-only class is the main all-weather gain in this AOI because cloud cover remains extremely high.

### Task 3: Topographic Analysis

Topographic filtering applies the HW4 DEM slope method and removes SAR support on `slope > 25°`.

False positives removed by slope class:

- `25-35 deg`: `0.0895 km2`
- `35-45 deg`: `0.3483 km2`
- `>45 deg`: `0.9252 km2`
- Total removed SAR-supported area: `1.3630 km2`

After topographic correction, the fused map becomes much more conservative:

- Optical Only: `0.4071 km2`
- SAR Only (Cloudy): `0.0388 km2`
- High Confidence: `0.0003 km2`

Saved outputs:

- `output/task3_topographic_audit.png`
- `output/task3_slope_false_positive_table.csv`

Limitation: this correction uses a stable local DEM rather than a post-disaster terrain surface, so it improves false-positive control but may also undercount flood evidence in steep valleys.

### Task 4: AI Strategic Briefing and Week 9 Comparison

Saved outputs:

- `output/task4_ai_briefing_log.csv`
- `output/task4_week9_week10_comparison.csv`

The AI log now contains:

- a complete prompt with all required metrics
- a structured strategic-response draft
- a 4-sentence reflection tied to the actual Week10 outputs

For the final comparison, I **explicitly recalculated an optical-only baseline inside the same Week10 AOI** instead of directly comparing to the original HW9 full-study total. This makes the comparison fairer:

- Optical-only baseline (same AOI): `0.4074 km2`
- W10 fused after topographic correction: `0.4462 km2`
- Net gain from fusion in the same AOI: `0.0388 km2`
- Cloud-covered area analyzed only by fusion: `0.0388 km2`

## Notes on the Week 9 Comparison

The original HW9 folder covers a broader validated optical extent, so directly comparing its total mapped area to the focused Week10 AOI would be misleading. To keep the final table defensible, `task4_week9_week10_comparison.csv` uses:

- a **W9-style optical-only baseline**
- recalculated **inside the same Week10 AOI**
- with a note pointing back to the original Week9 reference table in `data/week9_confidence_area_stats.csv`

## Submission Checklist

- Notebook named `Week10_ARIA_v70_陳冠嘉.ipynb`
- Captain’s Log markdown cells included throughout the notebook
- `.env` kept for reproducibility
- Output files written to `HW10/output`
- Task 1-4 deliverables completed
- README updated with the actual Week10 results
- requirements.txt updated for the notebook environment
