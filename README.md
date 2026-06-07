# Electricity Supply Inequality Index

**Project file:** `Electricity_Supply_Inequality_Index.qgz`
**Coordinate reference system:** EPSG:32628 (WGS 84 / UTM Zone 28N)
**Study area:** Sierra Leone - all 14 districts
**Date completed:** May 2026

---

## Purpose

This project quantifies spatial inequality in electricity supply infrastructure across Sierra Leone by measuring the total length and areal density of the medium voltage (MV) distribution network within each administrative district. The output enables energy planners, government ministries, and development partners to compare grid infrastructure coverage across districts, identify the most underserved areas, and prioritise investment in network expansion or off-grid alternatives.

---

## Input layers

| Layer | File | Geometry | Features | CRS |
|---|---|---|---|---|
| MV transmission lines (Sierra Leone) | `mv_lines_SierraLeone.gpkg` | Line | 3,739 | EPSG:32628 |
| Administrative districts (Level 2) | `SLE_adm2.gpkg` (via `src_extract/`) | Polygon | 14 | EPSG:32628 |

Both layers were confirmed in EPSG:32628 before processing. The MV lines dataset carries electrical attributes including `NOMINALVOL` (nominal voltage, kV), `OPERATINGV` (operating voltage, kV), `CONDUCTORT` (conductor type), `CONDUCTORS` (conductor size), and `CONDUCTORR` (conductor resistance).

---

## Processing workflow

### Step 1 - Layer validation and CRS check

Both input layers were loaded and inspected to confirm CRS alignment (EPSG:32628) and geometry validity. An early diagnostic run recorded in `debug_log.txt` identified an issue with the raw MV lines layer where line geometries had invalid bounding box values (infinite extents), resulting in zero features after clipping. This was resolved by fixing geometries using **Vector > Geometry Tools > Fix Geometries**, saving the corrected layer as `mv_lines_fixed.gpkg`, and subsequently reprojecting and validating the layer as `mv_lines_reproj_test.gpkg`. The final clean working layer is `mv_lines_SierraLeone.gpkg`.

### Step 2 - MV line length by district (spatial join)

Using **Vector > Analysis Tools > Sum Line Lengths**, the total length of MV line segments falling within each district polygon was computed. This tool performs an implicit spatial intersection, attributing the aggregate line length to the enclosing polygon. The raw output was saved as `mv_length_by_district_raw.gpkg`.

### Step 3 - Density calculation

Using the field calculator, two derived fields were appended to produce `mv_density_by_district_v2.gpkg`:

| Field | Formula | Unit |
|---|---|---|
| `mv_length_km` | `mv_length_m / 1000` | km |
| `mv_density_km_km2` | `mv_length_km / area_km2` | km of MV line per km² |

District area in km² (`area_km2`) was either pre-existing in the administrative layer or computed from polygon geometry prior to joining.

---

## Output layers

| File | Features | Description |
|---|---|---|
| `mv_lines_SierraLeone.gpkg` | 3,739 | Final clean MV lines layer |
| `mv_lines_fixed.gpkg` | - | Geometry-fixed intermediate |
| `mv_lines_clipped.gpkg` | - | Clipped to country boundary |
| `mv_length_by_district_raw.gpkg` | 14 | Raw sum-line-lengths output |
| `mv_density_by_district_v2.gpkg` | 14 | Primary deliverable with density fields |

---

## Results by district

| District | Region | MV length (km) | Area (km²) | Density (km/km²) |
|---|---|---|---|---|
| Koinadugu | Northern | 633 | 12,398 | 0.051 |
| Kono | Eastern | 618 | 5,377 | 0.115 |
| Kailahun | Eastern | 208 | 4,151 | 0.050 |
| Kambia | Northern | 230 | 3,082 | 0.075 |
| Kenema | Eastern | 156 | 6,217 | 0.025 |
| Tonkolili | Northern | 120 | 6,363 | 0.019 |
| Bombali | Northern | 97 | 8,216 | 0.012 |
| Western Rural | Western Area | 23 | 635 | 0.036 |
| Bo | Southern | 19 | 5,582 | 0.003 |
| Bonthe | Southern | 13 | 3,695 | 0.004 |
| Pujehun | Southern | 14 | 3,895 | 0.004 |
| Port Loko | Northern | 50 | 5,949 | 0.008 |
| Moyamba | Southern | 0 | 6,959 | 0.000 |
| Western Urban | Western Area | 0 | 83 | 0.000 |

---

## Key findings

- Kono and Koinadugu carry the greatest absolute MV line length, reflecting legacy mining infrastructure and northern grid expansion respectively.
- Moyamba and Western Urban record zero MV line length under this dataset, indicating either no MV infrastructure or a data gap requiring field verification.
- Bo District, which is the second-largest district by population, has only 19 km of MV coverage across 5,582 km², giving it one of the lowest density scores (0.003 km/km²).
- The distribution is heavily skewed, confirming significant spatial inequality in grid infrastructure across the country.

---

## Notes

- `Electricity_Supply_Inequality_Index2.qgz` is an interim project file saved during debugging and may not reflect the final layer configuration.
- `debug_log.txt` records the geometry issue encountered with the raw MV lines layer and the diagnostic steps taken to resolve it.
- The `src_extract/` subfolder contains source extracts used during layer preparation.
- Task reference document: `Task.docx`.
