# Data setup

No dataset files are distributed with this repository. Keep all downloaded and derived data inside this directory; its contents are ignored by Git except for this file.

## Public sources

### ComStock

- Provider: National Renewable Energy Laboratory (NREL)
- Project: https://comstock.nrel.gov/
- Public S3 bucket used by the download notebook: `s3://oedi-data-lake/`
- The current notebook references the `2024/comstock_amy2018_release_2` object prefix. Confirm and pin the exact release used for the final manuscript before publishing a tagged version.

### Building Data Genome Project 2

- Repository: https://github.com/buds-lab/building-data-genome-project-2
- Dataset paper: https://doi.org/10.1038/s41597-020-00712-x

Review and comply with the terms and citation requirements published by each data provider.

## Expected local layout

```text
data/
├── raw/
│   ├── comstock/                  # downloaded synthetic profiles and weather
│   └── bdg2/                      # downloaded BDG2 meter, metadata, and weather files
├── interim/
│   ├── ready-raw/                 # per-building aligned source tables
│   ├── windows_first2weeksjuly.pkl.gz
│   ├── windows_first2weeksjan.pkl.gz
│   └── knn_*.json
└── processed/
    ├── combined_ready_raw.csv     # model-ready ComStock source table
    └── bdg2_ny_2017_with_weather.csv
```

The historical notebooks were developed against equivalent filenames in the project root. The public copies use the directory layout above; if you change it, update the path constants at the top of each notebook.

## Git safety check

Before pushing:

```bash
git status --short
git ls-files data
```

The second command should return only `data/README.md`.
