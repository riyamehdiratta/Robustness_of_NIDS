# Robustness of NIDS

This repository contains a Jupyter notebook for analyzing the robustness of Network Intrusion Detection Systems (NIDS) using the UNSW-NB15 dataset.

## Project focus

The notebook explores:

- dataset structure and schema validation
- label and attack-category distribution analysis
- missing value and data-quality checks
- preprocessing and feature engineering for intrusion detection
- baseline model evaluation and robustness considerations for IDS performance

## Files

- `FINAL.ipynb` — main notebook with the analysis and experiments

## Dataset

The notebook uses the UNSW-NB15 dataset, which is a benchmark network intrusion dataset used for evaluating IDS models and studying robustness under realistic traffic conditions.

## Requirements

To run the notebook locally, install the following Python packages:

```bash
pip install notebook pandas numpy scikit-learn matplotlib seaborn kagglehub
```

## How to run

1. Open the repository in Jupyter Notebook or JupyterLab.
2. Launch `FINAL.ipynb`.
3. Run the cells in order.

## Notes

This project is intended as a research and analysis workflow for understanding intrusion detection performance and robustness on a real-world benchmark dataset.
