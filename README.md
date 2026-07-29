# Data Analysis and Visualization Lab

Complete, executable implementations of all 16 sections in the CS4503 Data
Analytics and Visualization lab manual. Every experiment has:

- a standalone Python implementation in `experiments/`;
- an executed Jupyter notebook in `notebooks/`;
- committed console, CSV/XLSX, HTML, and/or PNG results in `outputs/`;
- deterministic inputs in `data/`.

## Run everything

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
python -m pip install -r requirements.txt
python scripts/prepare_data.py
python run_all.py
python scripts/build_notebooks.py
python scripts/verify_outputs.py
```

The scripts use a non-interactive Matplotlib backend, so the full run works in
terminals and CI without a desktop display.

## Experiment index

| Manual section | Topic | Code | Generated output |
|---|---|---|---|
| 1 | Installation and library exploration | [`exp01_environment.py`](experiments/exp01_environment.py) | [`outputs/01_environment`](outputs/01_environment) |
| 2A | NumPy arrays | [`exp02a_numpy.py`](experiments/exp02a_numpy.py) | [`outputs/02a_numpy`](outputs/02a_numpy) |
| 2B | Pandas DataFrames | [`exp02b_pandas.py`](experiments/exp02b_pandas.py) | [`outputs/02b_pandas`](outputs/02b_pandas) |
| 2C | Text, Excel, and web-format data | [`exp02c_data_sources.py`](experiments/exp02c_data_sources.py) | [`outputs/02c_data_sources`](outputs/02c_data_sources) |
| 2D | Iris descriptive analytics | [`exp02d_iris.py`](experiments/exp02d_iris.py) | [`outputs/02d_iris`](outputs/02d_iris) |
| 3A | Diabetes univariate analysis | [`exp03a_univariate.py`](experiments/exp03a_univariate.py) | [`outputs/03a_univariate`](outputs/03a_univariate) |
| 3B | Bivariate linear/logistic models | [`exp03b_bivariate.py`](experiments/exp03b_bivariate.py) | [`outputs/03b_bivariate`](outputs/03b_bivariate) |
| 3C | Multiple regression | [`exp03c_multiple_regression.py`](experiments/exp03c_multiple_regression.py) | [`outputs/03c_multiple_regression`](outputs/03c_multiple_regression) |
| 3D | Computed dataset comparison | [`exp03d_comparison.py`](experiments/exp03d_comparison.py) | [`outputs/03d_comparison`](outputs/03d_comparison) |
| 4A | Normal curves | [`exp04a_normal_curves.py`](experiments/exp04a_normal_curves.py) | [`outputs/04a_normal_curves`](outputs/04a_normal_curves) |
| 4B | Z-test | [`exp04b_z_test.py`](experiments/exp04b_z_test.py) | [`outputs/04b_z_test`](outputs/04b_z_test) |
| 4C | Welch t-test | [`exp04c_t_test.py`](experiments/exp04c_t_test.py) | [`outputs/04c_t_test`](outputs/04c_t_test) |
| 4D | One-way ANOVA | [`exp04d_anova.py`](experiments/exp04d_anova.py) | [`outputs/04d_anova`](outputs/04d_anova) |
| 5A | Linear model validation | [`exp05a_linear_models.py`](experiments/exp05a_linear_models.py) | [`outputs/05a_linear_models`](outputs/05a_linear_models) |
| 5B | Logistic model validation | [`exp05b_logistic_models.py`](experiments/exp05b_logistic_models.py) | [`outputs/05b_logistic_models`](outputs/05b_logistic_models) |
| 5C | Time-series decomposition and ARIMA | [`exp05c_time_series.py`](experiments/exp05c_time_series.py) | [`outputs/05c_time_series`](outputs/05c_time_series) |

## Corrections made to the manual

The PDF contains code and interpretation errors that should not be copied into
an executable repository:

- package versions use the correct `__version__`/distribution metadata;
- experiment 3D computes model scores instead of printing invented constants;
- t-test and ANOVA conclusions are generated from the actual p-values;
- logistic models use stratified splits, scaling, and explicit validation
  metrics;
- time-series analysis uses dated sequential observations rather than treating
  unrelated patient rows as time;
- the manual's unspecified "UCI Diabetes" input is clearly marked as a
  synthetic, schema-compatible teaching dataset. See [`data/README.md`](data/README.md).

These changes preserve the syllabus topics while making the results
reproducible and statistically honest.

