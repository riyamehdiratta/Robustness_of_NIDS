# Robustness of Network Intrusion Detection Systems (NIDS) Under Feature & TTL Perturbations

This repository provides the complete research workflow, code, and reproducible experiments for auditing and defending Machine Learning-based Network Intrusion Detection Systems (NIDS) against adversarial and environmental perturbations on tabular network traffic, evaluated on the **UNSW-NB15** benchmark dataset.

The work investigates:
1. **Dataset Integrity & Reversal Auditing**: Correcting inverted benchmark train/test assignments and auditing feature ambiguity and label overlap.
2. **Standard Baselines**: Multi-seed evaluations of Random Forest and Multi-Layer Perceptrons (MLP).
3. **Non-TTL Feature Perturbations**: Robustness testing under Flow Duration and Timing/Jitter shifts.
4. **TTL Fragility Analysis**: Sensitivity sweeps ($-15\%$ to $+15\%$) exposing significant evasion vulnerabilities tied to TTL features (`sttl`, `dttl`).
5. **Defenses**:
   - **Data Augmentation**: Robustness benefits and limits of random TTL augmentation during training.
   - **Self-Supervised Representation Learning (SCARF)**: Robustness profiles of contrastive embeddings on tabular data.
   - **TTL-Aware SCARF**: A composite architecture combining contrastive learning, targeted TTL perturbation loss, and prediction consistency regularization.

---

## Repository Structure & Notebook Execution Order

The monolithic research notebook (`FINAL.ipynb`) has been modularized into 8 self-contained, sequentially numbered notebooks:

| Notebook | Description | Key Experiments / Outputs |
| :--- | :--- | :--- |
| [`01_data_preprocessing.ipynb`](./01_data_preprocessing.ipynb) | Dataset acquisition, schema validation, duplicate hash analysis, train/test reversal correction (`actual_train_df = test_df`, `actual_test_df = train_df`), and feature pipeline. | Validated training and testing feature sets. |
| [`02_baseline_rf_mlp.ipynb`](./02_baseline_rf_mlp.ipynb) | Clean baseline training and evaluation for Random Forest and MLP across 3 primary seeds (`42, 123, 2026`). Freezes baseline models for downstream testing. | Clean TPR, AUROC, AUPRC baseline tables. |
| [`03_feature_perturbation_experiments.ipynb`](./03_feature_perturbation_experiments.ipynb) | Non-TTL perturbation analysis on frozen baselines: Experiment A (Duration $\pm 5\%$), Experiment B (Timing/Jitter on `sjit`, `djit`), and Experiment D (Combined Duration + Jitter). | Degradation and evasion metrics under temporal perturbations. |
| [`04_ttl_robustness_analysis.ipynb`](./04_ttl_robustness_analysis.ipynb) | Detailed TTL perturbation sweep ($-15\%$ to $+15\%$ on `sttl` and `dttl`), multi-seed reproducibility, and attack-category breakdown. | `ttl_multiseed_mean_sd.csv`, `ttl_attack_category_mean_sd_ci.csv`. |
| [`05_ttl_augmentation.ipynb`](./05_ttl_augmentation.ipynb) | TTL data augmentation defense: Augmentation strength sweep ($\pm 2\%$, $\pm 5\%$, $\pm 10\%$, $\pm 15\%$), 3-seed reproducibility, and Table 8 generation. | Table 8: Standard MLP vs. TTL-Augmented MLP under dTTL perturbations. |
| [`06_scarf_baseline.ipynb`](./06_scarf_baseline.ipynb) | Self-Supervised Contrastive Learning (SCARF) pilot, multi-seed evaluation under dTTL perturbations, and paired-bootstrap statistical significance testing. | SCARF representation benchmarks and paired bootstrap tests. |
| [`07_ttl_aware_scarf.ipynb`](./07_ttl_aware_scarf.ipynb) | Novel TTL-aware SCARF defense with joint contrastive + perturbation + consistency loss, Difference-in-Differences statistical evaluation (M7.1), and clean vs perturbed summaries. | `FINAL_scarf_comparison_raw.csv`, `FINAL_scarf_comparison_summary.csv`. |
| [`08_final_evaluation_figures.ipynb`](./08_final_evaluation_figures.ipynb) | Comprehensive 3-way comparison (Standard MLP vs Original SCARF vs TTL-Aware SCARF) across dTTL levels ($-2\%$, $-5\%$, $-10\%$, $-15\%$) and publication figure generation. | `FINAL_three_way_comparison.csv`, `FINAL_mlp_vs_scarf_comparison.png`. |
| [`FINAL.ipynb`](./FINAL.ipynb) | Original monolithic reference notebook containing the complete contiguous experiment trajectory. | Complete end-to-end preprint pipeline. |

---

## Dataset Acquisition

Per the distribution terms of the UNSW-NB15 benchmark, dataset CSV files are not hosted directly in this repository.

### Option 1: Automatic Download via KaggleHub (Recommended)
`01_data_preprocessing.ipynb` includes an automated fallback that retrieves the dataset directly using `kagglehub`:
```python
import kagglehub
path = kagglehub.dataset_download("mrwellsdavid/unsw-nb15")
```

### Option 2: Manual Download from Official Source
1. Download `UNSW_NB15_training-set.csv` and `UNSW_NB15_testing-set.csv` from the official [UNSW-NB15 Repository (Australian Centre for Cyber Security)](https://research.unsw.edu.au/projects/unsw-nb15-dataset) or from Kaggle.
2. Place the two CSV files into the repository root or in a `./data` directory:
   ```
   ./data/UNSW_NB15_training-set.csv
   ./data/UNSW_NB15_testing-set.csv
   ```

> **Note on Dataset Split**: In the UNSW-NB15 Kaggle distribution, file names are reversed relative to the standard academic benchmark protocol. As implemented in `01_data_preprocessing.ipynb`, the standard split requires:
> - Training set: 175,341 records (`UNSW_NB15_testing-set.csv` in Kaggle naming)
> - Testing set: 82,332 records (`UNSW_NB15_training-set.csv` in Kaggle naming)

---

## Requirements & Environment Setup

Python 3.10+ and PyTorch (CPU or CUDA-enabled GPU) are supported.

```bash
# Clone the repository
git clone https://github.com/riyamehdiratta/Robustness_of_NIDS.git
cd Robustness_of_NIDS

# Create and activate a virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

---

## Reproducibility & Random Seeds

All experiments employ deterministic random seed seeding across Python `random`, `numpy`, and PyTorch:
- **Primary Seeds**: `42`, `123`, `2026`
- **Multi-Seed Extension Seeds**: `7`, `99` (used for 5-seed sensitivity benchmarks in Notebook 04)
- **Bootstrap Re-samples**: `N = 2000` with fixed NumPy generator (`Seed = 2026`)

All numerical outputs, standard deviations, and plots embedded within the notebooks directly reflect the executions reported in the preprint.
