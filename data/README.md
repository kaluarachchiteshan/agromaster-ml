# Data

Data files are not stored in this repository because of their size.

- Source: https://www.kaggle.com/datasets/khandelwalmanas/daily-commodity-prices-india
- Run `notebooks/01_data_loading.ipynb` to download the dataset and build `agmarknet_subset.parquet`.
- All intermediate files are written to `MyDrive/agromaster` on Google Drive.

## Sample file

`my_prices_example.csv` is a small example input for `notebooks/10_test_new_values.ipynb` (Test D).
It holds about 120 days of prices for one Onion market.

| Column | Meaning |
|---|---|
| `date` | Report date (YYYY-MM-DD) |
| `min_price` | Minimum price (INR per quintal) |
| `max_price` | Maximum price (INR per quintal) |
| `modal_price` | Most common price (INR per quintal) |

To test the model on your own prices, copy this file to `MyDrive/agromaster/my_prices.csv`,
change the prices, and run Cell 5 of notebook 10.
