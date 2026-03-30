#  Assessment of Landslide Impact on Vegetation Using NDVI
### A Case Study of Mount Rinjani, Indonesia — April 2018 to October 2025


##  Overview

This project analyses **Normalised Difference Vegetation Index (NDVI)** derived from **Landsat 8/9 OLI satellite imagery** to assess the impact of the **2018 Lombok earthquake-triggered landslides** on vegetation at two gully sites on **Mount Rinjani, Indonesia**.

The 2018 Lombok earthquake sequence — culminating in a magnitude **Mw 6.9 event on 5 August 2018** — triggered widespread slope failures across Mt. Rinjani's flanks, stripping vegetation and exposing bare soil across both study sites.

---

##  Study Objectives

 #  Objective 

 **1**  Quantify the extent of vegetation loss caused by the 2018 earthquake-induced landslides |
 **2**  Assess post-disturbance vegetation recovery trajectories across growing and non-growing seasons |
**3**  Evaluate inter-annual NDVI relationships to identify spatial and temporal patterns of vegetation change |

---

##  Study Sites

| Site | Coordinates (Centre) | Sample Points |
|---|---|---|
| 🔵 **Carbon Valley Gully** | 8.477°S, 116.417°E | 26 points |
| 🟢 **Paradise Valley Gully** | 8.477°S, 116.439°E | 24 points |

Both sites are located on the southern and south-eastern flanks of Mt. Rinjani within the Mt. Rinjani National Park, characterised by tropical montane forest. Site boundaries were identified using **k-means spatial clustering** (k = 2) on the 50 sample point coordinates.

---

## 🔑 Key Findings

| Finding | Carbon Valley Gully | Paradise Valley Gully |
|---|---|---|
| Pre-earthquake NDVI (Apr–May 2018) | 0.709 | 0.811 |
| Post-earthquake NDVI (Aug–Sep 2018) | 0.135 | 0.050 |
| **Vegetation loss** | **81.0%** | **93.8%** |
| Annual mean NDVI — nadir (2020) | 0.013 | 0.033 |
| Recovery peak — annual mean (2023) | 0.633 | 0.700 |
| Inter-site Pearson correlation | **r = 0.899, p < 0.001** | — |
| Seasonal difference (Mann-Whitney) | **p < 0.001** | **p < 0.001** |

> 💡 Both sites show strong synchronised response to the earthquake disturbance and subsequent recovery, driven by the same macro-scale landscape processes.

---

## 📊 Visualisations

### Figure 1 · NDVI Time Series (2018–2025)
> Monthly mean NDVI with the August 2018 earthquake marker and ±1 SD shading across sample points.

<p align="center">
  <img src="plots/fig1_ndvi_timeseries.png" width="95%"/>
</p>

---

### Figure 2 · Inter-Annual NDVI Trend
> Annual mean NDVI for both valleys showing the 2020 and 2022 secondary slumps and the 2023 recovery breakthrough.

<p align="center">
  <img src="plots/fig2_interannual.png" width="85%"/>
</p>



##  Analysis Pipeline

The script `ndvi_analysis.py` follows a fully documented, objective-driven pipeline:

Raw CSV (50 rows × 64 columns)
        │
        ▼
  Load & inspect raw data
        │
        ▼
  Fix NVDI → NDVI typos · remove duplicate columns
        │
        ▼
  Clip invalid NDVI values to NaN  (range: −1 to +1)
  No interpolation — real satellite observations only
        │
        ▼
  Melt wide → long format · parse dates · drop ambiguous columns
        │
        ▼
  K-Means clustering (k=2) on GPS coordinates
  Assign Carbon Valley Gully / Paradise Valley Gully
  Label stability: anchored to cluster centroid longitude
        │
        ▼
  Add Year · Month · Season · YearMonth columns
        │
        ├──► OBJECTIVE 1 ──► Vegetation loss %
        │                    Baseline: Apr–May 2018 (pre-earthquake)
        │                    Post-event: Aug–Sep 2018
        │
        ├──► OBJECTIVE 2 ──► NDVI time series plot (Figure 1)
        │                    Seasonal statistics table
        │                    Full 42-date observation record (Table 3)
        │
        └──► OBJECTIVE 3 ──► Inter-annual trend plot (Figure 2)
                             Pearson correlation  r = 0.899, p < 0.001
                             Mann-Whitney U test  per site, p < 0.001
```

---

## 🛠️ Data Cleaning Notes

The raw dataset (`ndvi_extraction.csv`) contained several data quality issues, all identified and fixed before analysis:

| Issue | Count | Fix Applied |
|---|---|---|
| Columns named `NVDI...` (typo — I and D transposed) | 7 columns | Renamed, then resulting duplicates removed |
| Columns named `NDVI2021_1`, `NDVI2022_2`... (no valid month) | 13 columns | Dropped via date parse failure |
| NDVI values outside valid range −1 to +1 (cloud / sensor artefacts) | 46 values | Replaced with `NaN` — **not interpolated** |

> **Important:** No interpolation was applied at any stage. All analysis uses only genuine satellite observations. Missing values are excluded automatically by pandas aggregation.

---

##  Dependencies

| Library | Version | Purpose |
|---|---|---|
| `pandas` | ≥ 1.3 | Data loading, cleaning, reshaping (wide → long) |
| `numpy` | ≥ 1.21 | Numerical operations, NaN handling |
| `matplotlib` | ≥ 3.4 | Chart generation and export |
| `seaborn` | ≥ 0.12 | Statistical box plots |
| `scipy` | ≥ 1.7 | Pearson correlation, Mann-Whitney U test |
| `scikit-learn` | ≥ 1.0 | K-Means spatial clustering |

Install all at once:

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn
```

---

## 📚 Scientific Background

**NDVI (Normalised Difference Vegetation Index)** is computed from Landsat imagery as:

$$NDVI = \frac{NIR - Red}{NIR + Red}$$

| NDVI Range | Vegetation Condition |
|---|---|
| > 0.6 | Dense, healthy canopy |
| 0.3 – 0.6 | Moderate vegetation cover |
| 0.1 – 0.3 | Sparse vegetation / bare soil |
| < 0.1 | No vegetation / debris / water |

**Seasonal note:** Mt. Rinjani experiences the Indonesian monsoon. The wet season (November–April) brings heavy cloud cover that reduces Landsat optical signal quality. This is why dry-season (May–October) observations tend to show higher apparent NDVI — it is primarily a measurement quality effect, not a true phenological signal.

**The 2018 Lombok Earthquake sequence:**

| Date | Event |
|---|---|
| 29 July 2018 | Mw 6.4 foreshock — initial slope instability begins |
| 5 August 2018 | **Mw 6.9 main shock — widespread landslides triggered** |
| August–September 2018 | Multiple aftershocks — continued slope disturbance |

---

##  Scientific Report

A full peer-review-style scientific report is available in `report/rinjani_ndvi_report.docx`, containing:

- Abstract
- Introduction and study objectives  
- Study area description with site coordinates
- Data and methods (satellite data, k-means clustering, seasonal definition, statistical tests)
- Results for all three objectives (with in-text figures and tables)
- Discussion and conclusion
- Complete NDVI observation table — 42 dates × 2 sites
- References

