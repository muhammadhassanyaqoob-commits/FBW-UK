# SurroKit Studio — FBW-UK Surrogate Modelling Tool

A standalone Windows desktop application for building, comparing, and deploying surrogate models using Fuzzy-Based Weighted Universal Kriging (FBW-UK) and benchmark methods (RSM, Simple Kriging, ARD).

---

## Table of Contents

1. [System Requirements](#system-requirements)
2. [Installation](#installation)
3. [Running the Software](#running-the-software)
4. [Step-by-Step Usage Guide](#step-by-step-usage-guide)
5. [Exporting Models](#exporting-models)
6. [Datasets Included](#datasets-included)
7. [Troubleshooting](#troubleshooting)

---

## System Requirements

| Requirement | Details |
|---|---|
| **Operating System** | Windows 10 or later (64-bit) |
| **Python** | 3.9 or later (only if running from source) |
| **RAM** | 4 GB minimum, 8 GB recommended |
| **Disk Space** | ~200 MB for the compiled executable |

> **Note:** If using the compiled `.exe`, no Python installation is required.

---

## Installation

### Option A: Run the Compiled Executable (Recommended)

1. Download `SurroKitStudio.exe` (or `FBW-UK_Surrogate_Tool.exe`) from the `dist/` folder or the GitHub releases page.
2. Place the `.exe` in any folder on your computer.
3. Double-click the `.exe` to launch the application. No installation needed.

### Option B: Run from Source Code

1. **Install Python 3.9+** from [python.org](https://www.python.org/downloads/). During installation, check "Add Python to PATH".

2. **Open a terminal** (Command Prompt or PowerShell) and navigate to the source folder:
   ```
   cd path\to\fbwuk_app
   ```

3. **Create a virtual environment** (recommended):
   ```
   python -m venv .venv
   .venv\Scripts\activate
   ```

4. **Install dependencies:**
   ```
   pip install -r requirements.txt
   ```
   Required packages: `numpy`, `scipy`, `pandas`, `openpyxl`, `matplotlib`

5. **Launch the application:**
   ```
   python main.py
   ```

---

## Running the Software

After launching, the application opens with five tabs:

1. **Setup** — Load data and configure variables
2. **Training** — Train surrogate models and view metrics
3. **Compare** — Side-by-side model comparison
4. **Export** — Save trained surrogates in various formats
5. **Predict** — Make predictions using trained models

---

## Step-by-Step Usage Guide

### Step 1: Setup Tab — Load Your Data

1. Set the **Number of Input Variables** (1–30). For the included CubeSat dataset, use **8**.
2. Set the **Number of Responses** (1–6). For the included dataset, use **3**.
3. Click **Browse** and select your Excel file (`.xlsx` or `.xls`).
   - The file must follow the required column naming convention. See the [Data Preparation Guide](DATA_GUIDE.md) for details.
4. Set the **Test Split Ratio** (default: 0.3 means 70% training, 30% testing).
5. Click **Load & Preview** to validate and display the first rows of data.
   - If successful, a summary of detected variables and responses will appear.
   - If errors occur, check that column names match the required format exactly.

### Step 2: Training Tab — Train Models

1. Select the model(s) to train from the dropdown:
   - **All Models** — trains RSM, SK, and FBW-UK simultaneously
   - **RSM** — Response Surface Methodology (2nd-order polynomial)
   - **SK** — Simple Kriging (isotropic Matérn-5/2 GP)
   - **FBW-UK** — Fuzzy-Based Weighted Universal Kriging
2. Click **Train Models**.
   - A progress bar tracks training status.
   - Training log displays per-response progress and completion time.
3. After training completes, review the results:
   - **Accuracy metrics** (R², RMSE, MAE) are displayed for each response.
   - **Fuzzy weight chart** (FBW-UK only) shows how each input variable is weighted per response.
   - **Parity plots** show predicted vs. actual values on the test set.

### Step 3: Compare Tab — Compare Models

1. After training multiple models, switch to the **Compare** tab.
2. A comparison table shows R², RMSE, and MAE for each model across all responses.
3. A bar chart visualises R² scores for quick comparison.

### Step 4: Export Tab — Save Trained Models

1. Select the export format from the dropdown:
   - **Pickle (.pkl)** — Python-native serialisation for reuse in scripts
   - **JSON** — portable format for integration with other tools
   - **Python code** — standalone Python script that reproduces the trained model
2. Click **Export** and choose a save location.
3. The exported model can be loaded in external Python scripts for batch prediction.

### Step 5: Predict Tab — Make Predictions

#### Single-Point Prediction
1. Enter a numeric value for each input variable in the fields provided.
2. Click **Predict (single point)**.
3. Results display the predicted mean and 95% confidence interval for each response.

#### Batch Prediction (Testing with New Data)
1. Click **Load CSV / Excel** and select a file containing new input data.
   - The file must contain the same variable columns (`V1`, `V2`, ..., `Vn`) used during training.
   - Response columns (`R1`, `R2`, ...) are optional; if present, they are ignored during prediction.
2. Predictions for all loaded points are computed immediately and displayed in a table.
3. Click **Export CSV** to save the predictions to a file.

---

## Exporting Models

The software supports three export formats:

| Format | Use Case |
|---|---|
| **Pickle** | Reload the exact model in Python using `pickle.load()` |
| **JSON** | Language-neutral format; includes weights, trend coefficients, and GP parameters |
| **Python code** | Copy-paste-ready script with the trained model embedded |

---

## Datasets Included

This repository includes two datasets used in the research paper:

### 414-Sample Dataset (`datasets/414_sample_dataset/`)
- **File:** `414_dataset.xlsx`
- **Variables:** V1–V10 (8 independent: V1, V3, V5–V10; V2=V1, V4=V3 by symmetry)
- **Responses:** R1 (deflection, m), R2 (stress, Pa), R3 (mass, kg)
- **Source:** Latin Hypercube Sampling, screened from 993 FEA simulations

### 800-Sample Dataset (`datasets/800_sample_dataset/`)
- **File:** `800_dataset.xlsx`
- **Variables:** x1–x10 (all 10 variables, original naming)
- **Responses:** Deflection (m), Stress (Pa), Mass (kg)
- **Filtered results:** Yield-filtered, UTS-filtered, and unfiltered tier results included as CSV files
- **Source:** Independent LHS design spanning the full 10-variable design space

> **Important:** The 800-sample dataset uses different column names (`x1`–`x10`, `Deflection`, `Stress`, `Mass`) than the software expects. To use it in the software, rename columns to `V1`–`V10` and `R1`–`R3`. See the [Data Preparation Guide](DATA_GUIDE.md) for instructions.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Application does not start | Ensure you are running on Windows 10+ (64-bit). Try running as Administrator. |
| `ModuleNotFoundError: openpyxl` | Run `pip install openpyxl` in your virtual environment. |
| `_tkinter` not found | Reinstall Python with Tcl/Tk support (the standard installer includes it). |
| "Column V1 not found" error | Check that your Excel file uses the exact column names `V1`, `V2`, etc. See the Data Guide. |
| Very slow training | Reduce dataset size or lower `n_restarts` in `model.py` (default: 15). |
| Cholesky failure warning | This is normal — numerical jitter is added automatically for stability. |
| Excel file won't load | Ensure the file is `.xlsx` format. Older `.xls` files may need to be re-saved. |
