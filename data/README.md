# Data

The raw vehicle-listings dataset is not stored in this repository.

The notebook is configured to:

1. try the original training-environment path `/datasets/autos.csv`;
2. if that path is unavailable, load the dataset from the fallback URL already included in the notebook.

The listings originate from **eBay Kleinanzeigen** and represent the German used-car market in 2016.

If you prefer to keep a local copy of the dataset, place it in this directory as `autos.csv` and update the loading cell in the notebook to use `data/autos.csv`.

Raw datasets and generated model files are excluded from Git by default to keep the repository lightweight and avoid redistributing data unnecessarily.
