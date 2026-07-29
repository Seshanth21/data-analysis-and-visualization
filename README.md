# Data Analysis and Visualization Lab

All 16 CS4503 Data Analytics and Visualization lab experiments are contained in
one self-contained Google Colab notebook with embedded executed outputs.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Seshanth21/data-analysis-and-visualization/blob/main/DAV_All_Experiments_Colab.ipynb)

## Main file

[`DAV_All_Experiments_Colab.ipynb`](DAV_All_Experiments_Colab.ipynb)

The notebook includes:

- complete code for all 16 experiments;
- deterministic dataset preparation with an offline fallback;
- embedded console results and 16 generated plots;
- CSV, XLSX, HTML, text, and PNG output generation;
- a final `DAV_All_Outputs.zip` containing every generated output.

## Run in Google Colab

1. Click **Open in Colab** above.
2. Select **Runtime -> Run all**.
3. Allow the dependency-installation cell to finish.
4. Run through the final cell. It creates `DAV_All_Outputs.zip`.
5. Use the download command printed by the final cell.

No manual dataset upload is required. The notebook downloads the Pima dataset
when internet access is available and uses a deterministic fallback otherwise.

## Experiment index

| Section | Experiment |
|---|---|
| 1 | Installation and library exploration |
| 2A | NumPy arrays and operations |
| 2B | Pandas DataFrame operations |
| 2C | Reading text, Excel, and web-format data |
| 2D | Iris descriptive analytics |
| 3A | Diabetes univariate analysis |
| 3B | Bivariate linear and logistic regression |
| 3C | Multiple regression |
| 3D | Computed comparison of analysis results |
| 4A | Normal curves |
| 4B | Z-test |
| 4C | Welch independent t-test |
| 4D | One-way ANOVA |
| 5A | Building and validating linear models |
| 5B | Building and validating logistic models |
| 5C | Time-series decomposition and ARIMA forecasting |

## Validation

The committed notebook was validated before upload:

- 20 of 20 code cells executed;
- 16 experiment sections present;
- zero error outputs;
- 16 plots embedded;
- 42 generated files packaged successfully.

