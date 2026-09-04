# Used Car Price Prediction

Machine learning project for predicting used-car market prices in Germany from vehicle characteristics. The project covers data quality analysis, preprocessing, exploratory data analysis, regional analysis, model training, model comparison, final test evaluation, and comparison with a naïve baseline.

## Project objective

Build a regression model that balances three criteria:

- predictive quality;
- model training time;
- prediction time.

The target variable is vehicle **price in EUR**.

## Dataset

The original dataset contains **354,369 listings** and **16 features** describing used vehicles, including brand, model, registration year, mileage, engine power, body type, gearbox, fuel type, repair history, and postal code.

The listings originate from **eBay Kleinanzeigen** and represent the German used-car market. The notebook first attempts to load `/datasets/autos.csv` and otherwise uses the public fallback URL included in the notebook.

After preprocessing, the modeling dataset contains **341,531 observations** and **10 predictors**.

## Workflow

1. **Data inspection and quality assessment**
   - missing values and duplicate records;
   - anomalous registration years, prices, and engine power values;
   - categorical-value consistency;
   - postal-code analysis.

2. **Data preprocessing**
   - snake_case feature names;
   - duplicate removal;
   - removal of non-informative technical features;
   - treatment of anomalous values;
   - hierarchical group-based imputation;
   - creation of postal-code prefix features for regional analysis.

3. **Exploratory data analysis**
   - market structure by brand and model;
   - mileage, age, power, fuel type, gearbox, and price distributions;
   - German regional patterns;
   - PhiK associations between vehicle characteristics and price.

4. **Modeling**
   - LightGBM Regressor;
   - Random Forest Regressor;
   - regularized linear models: Ridge and Lasso;
   - 60/20/20 train-validation-test split;
   - hyperparameter selection with `GridSearchCV` and 3-fold cross-validation;
   - RMSE as the primary evaluation metric.

5. **Final evaluation**
   - selection of the best model on the validation set;
   - one-time evaluation on the held-out test set;
   - comparison with a constant mean-value baseline.

## Key EDA findings

- The top 10 brands account for approximately **80%** of listings.
- Around **80%** of vehicles were registered between **1995 and 2009**.
- About **82%** of vehicles fall into the **100,000–150,000+ km** mileage range.
- Petrol and diesel vehicles account for approximately **98%** of listings.
- The strongest PhiK associations with price were observed for:
  - `registration_year` — **0.59**;
  - `model` — **0.58**;
  - `power` — **0.51**.
- Price relationships are frequently nonlinear and interdependent, which supports the use of multivariate tree-based models.

## Model results

| Model | Validation RMSE, EUR | Training time, s | Prediction time, s |
|---|---:|---:|---:|
| **LightGBM** | **1,589.76** | **192.61** | 4.32 |
| Random Forest | 1,656.41 | 480.23 | 3.14 |
| Ridge | 2,692.15 | 722.55 | **0.17** |

**LightGBM** achieved the best validation performance and was selected as the final model.

Final test result:

- **Test RMSE:** €1,612.45
- Validation-to-test RMSE increase: **1.43%**
- Mean-value baseline test RMSE: **€4,505.11**
- RMSE reduction relative to the baseline: **64.2%**

The close validation and test scores indicate stable performance across the two held-out samples, while the baseline comparison shows that the model captures useful predictive structure beyond a constant prediction.

## Best LightGBM configuration

The best configuration among the tested parameter combinations was:

```text
max_depth = 15
min_child_samples = 2
n_estimators = 300
num_leaves = 400
```

## Repository structure

```text
used-car-price-prediction/
├── README.md
├── used_car_price_prediction.ipynb
├── requirements.txt
├── .gitignore
├── data/
│   └── README.md
└── images/
    └── German_postcode_information.png
```

The trained `.pkl` files produced by the notebook are treated as generated artifacts and are excluded by `.gitignore` by default.

## Technologies

- Python 3.12
- pandas, NumPy
- Matplotlib, Seaborn, missingno
- PhiK
- scikit-learn
- LightGBM
- Jupyter

## How to run

Clone the repository and create a virtual environment:

```bash
git clone <your-repository-url>
cd used-car-price-prediction
python -m venv .venv
```

Activate the environment:

```bash
# Windows
.venv\Scripts\activate

# macOS / Linux
source .venv/bin/activate
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

Start Jupyter:

```bash
jupyter lab
```

Then open `used_car_price_prediction.ipynb` and run the notebook from top to bottom.

## Notes and limitations

- The data describe a historical snapshot of the German used-car market in **2016**, so the fitted price relationships should not be interpreted as current market prices.
- Training and prediction times depend on hardware and runtime conditions and should be interpreted as results of this experiment rather than universal benchmarks.
- Postal-code regions do not perfectly match German administrative boundaries; regional mappings in the notebook are approximate and are used mainly for exploratory analysis.
- The project evaluates model quality offline and does not include production deployment, monitoring, or data-drift infrastructure.

## Notebook

The complete analysis, visualizations, preprocessing decisions, model training, and conclusions are available in [`used_car_price_prediction.ipynb`](used_car_price_prediction.ipynb).
