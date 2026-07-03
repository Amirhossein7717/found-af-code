# ECG Foundation Models Benchmark — Code (FOUND-AF)

Code for benchmarking pretrained ECG foundation models as frozen feature
extractors for atrial fibrillation (AF) detection, evaluated across four
public PhysioNet ECG datasets.

This is the code-only companion to an ongoing research project. Paper
source and results are kept in a separate private repository until
publication.

## Repository structure

```
.
├── code/                 Benchmark notebooks (one per dataset)
│   ├── 00_environment_diagnostics.ipynb
│   ├── AFDB.ipynb
│   ├── CINC2017.ipynb
│   ├── CPSC2021.ipynb
│   └── LTAFDB.ipynb
├── data/
│   └── README.md         Dataset sources and preprocessing pipeline
├── requirements.txt
└── LICENSE
```

## What each notebook does

Each dataset notebook:
- clones the original model repositories and downloads pretrained
  checkpoints (HuBERT-ECG, CLEF, ST-MEM, ECG-JEPA, ECGFounder) from their
  official sources (GitHub / Hugging Face Hub / Zenodo / Google Drive
  mirrors),
- loads the dataset's processed windows,
- extracts frozen embeddings from all 9 models,
- runs 5-fold recording-level cross-validation with a fixed-configuration
  XGBoost classifier,
- writes per-model metrics and efficiency numbers.

## Getting started

1. See [`data/README.md`](data/README.md) for the four public datasets
   used, download links, and the exact preprocessing pipeline (10-second
   windows, 5-second overlap, per-window z-score normalisation).
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Open a notebook in [`code/`](code/) and update the `PROJECT_DIR` /
   `PARQUET_PATH` variables in the first cell to point at your local data
   location (the notebooks were originally run on Google Colab against a
   mounted Drive folder).

## License

Code is released under the [MIT License](LICENSE). The underlying ECG
databases and pretrained model checkpoints are third-party resources under
their own licenses — see `data/README.md`.
