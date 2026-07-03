# Data

This benchmark uses four **publicly available** PhysioNet/CinC databases.
Raw signals and the processed `.parquet` feature files (150 MB – 1.4 GB
each, ~3.2 GB total) are **not stored in this repository** — they are
fully reproducible from public sources with the steps below.

## Datasets

| Dataset | Source | Lead | Native Fs | Windows | Recordings |
|---|---|---|---|---|---|
| **AFDB** | [PhysioNet MIT-BIH Atrial Fibrillation Database](https://physionet.org/content/afdb/1.0.0/) | II | 250 Hz | 168,534 | 23 |
| **CinC2017** | [PhysioNet/CinC Challenge 2017](https://physionet.org/content/challenge-2017/1.0.0/) ("AF Classification from a Short Single Lead ECG Recording") | I | 300 Hz | — | — |
| **CPSC2021** | [China Physiological Signal Challenge 2021](http://2021.icbeb.org/CPSC2021) | II | 200 Hz | — | — |
| **LTAFDB** | [PhysioNet Long-Term AF Database](https://physionet.org/content/ltafdb/1.0.0/) | II | 128 Hz | — | — |

Combined across all four datasets: **1,954,594** ten-second windows from
**7,308** recordings (see manuscript abstract in [`../paper/main.tex`](../paper/main.tex)).

## Preprocessing pipeline

Every dataset goes through the identical pipeline (implemented per-dataset
in [`../code`](../code)):

1. Segment each recording into **10-second windows with 5-second overlap**
   (50% hop), using the database's rhythm annotations to assign a
   window-level label (`Normal` vs. `AF`).
2. Z-score normalise each window.
3. Save all windows for a dataset into a single Parquet file:
   `<dataset>_<native_hz>hz_<lead>_10s_5s.parquet` — one row per window,
   columns are the raw samples plus `label` and `record_id`
   (record-of-origin, used later for recording-level CV splits).
4. At benchmark time, each 10-second window is **resampled on the fly** to
   whatever native sampling rate the target foundation model expects
   (100 / 128 / 200 / 250 / 300 / 500 Hz depending on the model) — see
   `MODEL_HZ` in [`../code/AFDB.ipynb`](../code/AFDB.ipynb).

## Regenerating the processed Parquet files

1. Download the raw databases from the links above (AFDB/LTAFDB via
   `wfdb`, CinC2017 via the Challenge's `training2017.zip`, CPSC2021 via
   the CPSC portal).
2. Run the corresponding cells in [`../code`](../code) — each notebook's
   "Load Dataset" cell expects
   `processed_datasets/<dataset>_<hz>hz_<lead>_10s_5s.parquet` and will
   tell you if the file is missing.
3. Point `PARQUET_PATH` in the notebook's config cell at your local
   `processed_datasets/` directory (the notebooks were run on Google
   Colab against a mounted Drive folder — update the path for a local or
   different-cloud environment).

## Pretrained model checkpoints

The nine foundation models evaluated (HuBERT-ECG ×3 sizes, CLEF ×3 sizes,
ST-MEM, ECG-JEPA, ECGFounder) are **not redistributed here** either — they
are downloaded automatically by the "Install & Clone" / "Download Model
Weights" cells in each notebook, from the original authors' GitHub repos,
Hugging Face Hub, Zenodo, and Google Drive mirrors. See those cells for
the exact source URLs.
