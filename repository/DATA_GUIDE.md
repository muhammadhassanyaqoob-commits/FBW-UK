# Data Preparation Guide for SurroKit Studio

This guide explains how to prepare your Excel data files for use in the SurroKit Studio software, including column naming rules, file splitting for training and testing, and step-by-step loading instructions.

---

## Table of Contents

1. [Excel File Format Requirements](#excel-file-format-requirements)
2. [Column Naming Convention](#column-naming-convention)
3. [Preparing Your Data from Scratch](#preparing-your-data-from-scratch)
4. [Splitting Data into Training and Testing Files](#splitting-data-into-training-and-testing-files)
5. [Loading the Training File](#loading-the-training-file)
6. [Using the Test File for Prediction](#using-the-test-file-for-prediction)
7. [Converting the 800-Sample Dataset](#converting-the-800-sample-dataset)
8. [Common Mistakes and How to Avoid Them](#common-mistakes-and-how-to-avoid-them)

---

## Excel File Format Requirements

- **File format:** `.xlsx` (Microsoft Excel Open XML format). Older `.xls` files should be re-saved as `.xlsx`.
- **Sheet:** Data must be on the **first sheet** of the workbook.
- **Header row:** The **first row** must contain column names. No merged cells.
- **Data rows:** Starting from row 2, every row is one data sample. All values must be **numeric** (no text, blanks, or formulas that return errors).
- **No extra header rows:** Do not include units, descriptions, or blank rows above the data.

---

## Column Naming Convention

The software identifies input variables and output responses **strictly by column name**. Column names are **case-sensitive**.

### Input Variables

Name your input variable columns as:

```
V1, V2, V3, ..., Vn
```

- Start with uppercase `V` followed by a number starting from `1`.
- Numbers must be consecutive: `V1`, `V2`, `V3`, etc.
- The number of V-columns must match the **Number of Input Variables** you set in the Setup tab.

**Examples:**
| Correct | Incorrect |
|---|---|
| `V1` | `v1` (lowercase) |
| `V2` | `Var2` (wrong prefix) |
| `V10` | `V 10` (space in name) |
| `V3` | `X3` (wrong letter) |

### Output Responses

Name your response columns as:

```
R1, R2, R3, ..., Rm
```

- Start with uppercase `R` followed by a number starting from `1`.
- Numbers must be consecutive: `R1`, `R2`, `R3`, etc.
- The number of R-columns must match the **Number of Responses** you set in the Setup tab.

**Examples:**
| Correct | Incorrect |
|---|---|
| `R1` | `r1` (lowercase) |
| `R2` | `Response2` (wrong name) |
| `R3` | `Stress` (descriptive name) |

### Column Order

The columns can appear in **any order** in the Excel file. The software identifies them by name, not position. Additional columns beyond the required V and R columns are allowed and will be ignored.

### Complete Example

For a problem with **8 input variables** and **3 responses**, the Excel file should look like:

| V1 | V2 | V3 | V4 | V5 | V6 | V7 | V8 | R1 | R2 | R3 |
|---|---|---|---|---|---|---|---|---|---|---|
| 11.96 | 11.96 | 2.99 | 2.99 | 45.2 | 55.1 | 60.3 | 42.7 | 0.000413 | 219681700 | 1.1316 |
| 11.08 | 11.08 | 2.76 | 2.76 | 50.8 | 48.3 | 55.7 | 39.1 | 0.000465 | 212998000 | 1.0028 |
| ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

Where:
- `V1`–`V8` are the design variables (e.g., thicknesses, dimensions in mm)
- `R1` = deflection (m)
- `R2` = von Mises stress (Pa)
- `R3` = mass (kg)

---

## Preparing Your Data from Scratch

Follow these steps to prepare a new dataset:

### Step 1: Open Excel and Create a New Workbook

Open Microsoft Excel and create a new blank workbook.

### Step 2: Add Column Headers in Row 1

In cell A1, type `V1`. In B1, type `V2`. Continue for all your input variables. After the last variable column, add `R1`, `R2`, etc. for your responses.

### Step 3: Enter Your Data Starting from Row 2

Each row represents one simulation or experiment. Enter all numeric values. Do not leave any cells blank.

### Step 4: Verify Your Data

- Check that all column headers match the naming convention exactly.
- Check that there are no blank rows or non-numeric entries.
- Check that the number of V columns and R columns matches what you plan to set in the software.

### Step 5: Save the File

Save as **Excel Workbook (.xlsx)**. Do not save as `.csv` for the training file — the software requires `.xlsx` for the Setup/Training tabs.

---

## Splitting Data into Training and Testing Files

The software can automatically split your data using the test ratio on the Setup tab. However, if you want to use a **separate test file** for the Prediction tab (recommended for independent validation), follow these steps:

### Method A: Automatic Split (Within the Software)

1. Load your **complete dataset** (all samples) in the Setup tab.
2. Set the **Test Split Ratio** (e.g., 0.3 for 30% test, 70% training).
3. The software automatically splits the data internally.
4. Training and evaluation both use this internal split.

This is the simplest approach and is recommended for initial use.

### Method B: Manual Split (Separate Training and Testing Files)

Use this method when you want full control over which samples are used for training and which for testing.

#### Step 1: Open Your Complete Dataset in Excel

Open the full dataset file containing all samples.

#### Step 2: Decide on the Split

A common split is **70% training / 30% testing**. For example:
- 414 total samples → 289 training + 125 testing
- 800 total samples → 560 training + 240 testing

#### Step 3: Create the Training File

1. Select all rows you want to use for training (e.g., the first 289 rows of data, plus the header row).
2. Copy them.
3. Open a new Excel workbook.
4. Paste the data (make sure the header row with `V1`, `V2`, ..., `R1`, `R2`, ... is included).
5. Save as `training_data.xlsx`.

#### Step 4: Create the Testing File

1. Go back to the original file.
2. Select the remaining rows (e.g., rows 290–414, plus the header row).
3. Copy them.
4. Open a new Excel workbook.
5. Paste the data. **The header row must be included.**
6. Save as `test_data.xlsx`.

> **Important:** Both files must have identical column headers. The test file must contain at least the **V columns**. R columns are optional in the test file — if present, they are ignored during prediction but can be used for manual accuracy comparison.

#### Step 5: Alternative — Use Python to Split

If you are comfortable with Python, you can split programmatically:

```python
import pandas as pd
from sklearn.model_selection import train_test_split

df = pd.read_excel('your_dataset.xlsx')
train_df, test_df = train_test_split(df, test_size=0.3, random_state=42)

train_df.to_excel('training_data.xlsx', index=False)
test_df.to_excel('test_data.xlsx', index=False)
```

---

## Loading the Training File

1. Open **SurroKit Studio**.
2. Go to the **Setup** tab.
3. Set the number of input variables and responses to match your file.
4. Click **Browse** and select your training file (`training_data.xlsx`).
5. Set the **Test Split Ratio**:
   - If using Method A (auto split), set to 0.3 (or your preferred ratio).
   - If using Method B (separate files), set to **0.0** so all loaded data is used for training.
6. Click **Load & Preview**.
7. Verify the data preview looks correct.
8. Go to the **Training** tab, select your model(s), and click **Train Models**.

---

## Using the Test File for Prediction

After training is complete:

1. Go to the **Predict** tab.
2. Click **Load CSV / Excel**.
3. Select your test file (`test_data.xlsx`).
   - The file must contain the same V-columns used during training.
   - Response columns are optional and will be ignored.
4. The software computes predictions for all rows and displays them in the results table.
5. Click **Export CSV** to save predictions to a file for further analysis.

### Comparing Predictions with Actual Values

If your test file contains both V-columns and R-columns, you can compare predictions with actual values manually:
1. Export the predictions from the software.
2. Open both the exported predictions and the original test file.
3. Compare the predicted values against the actual R-column values.

---

## Converting the 800-Sample Dataset

The 800-sample dataset included in this repository uses different column names than the software expects. To use it in SurroKit Studio:

### Current Column Names (800-sample file)
```
x1, x2, x3, x4, x5, x6, x7, x8, x9, x10, Deflection, Stress, Mass
```

### Required Column Names
```
V1, V2, V3, V4, V5, V6, V7, V8, V9, V10, R1, R2, R3
```

### Renaming Steps in Excel

1. Open `800_dataset.xlsx`.
2. Click on cell A1 (which says `x1`) and change it to `V1`.
3. Change `x2` → `V2`, `x3` → `V3`, ..., `x10` → `V10`.
4. Change `Deflection` → `R1`, `Stress` → `R2`, `Mass` → `R3`.
5. Save as a new file (e.g., `800_dataset_renamed.xlsx`).

### Renaming Using Python

```python
import pandas as pd

df = pd.read_excel('datasets/800_sample_dataset/800_dataset.xlsx')
rename_map = {f'x{i}': f'V{i}' for i in range(1, 11)}
rename_map.update({'Deflection': 'R1', 'Stress': 'R2', 'Mass': 'R3'})
df.rename(columns=rename_map, inplace=True)
df.to_excel('800_dataset_renamed.xlsx', index=False)
```

After renaming:
1. Set **Number of Input Variables = 10** and **Number of Responses = 3** in the Setup tab.
2. Load the renamed file and proceed as usual.

---

## Common Mistakes and How to Avoid Them

| Mistake | What Happens | How to Fix |
|---|---|---|
| Using lowercase `v1` instead of `V1` | Software cannot find the variable | Rename to uppercase: `V1`, `V2`, etc. |
| Using descriptive names (`Stress`, `Mass`) | Software cannot find the response | Rename to `R1`, `R2`, `R3`, etc. |
| Blank cells in the data | Training fails or produces errors | Fill all cells with numeric values or remove incomplete rows |
| Skipping a column number (e.g., `V1`, `V3` with no `V2`) | Software expects consecutive numbering | Renumber so all V-columns are consecutive |
| Setting wrong variable/response count | Mismatch between file and settings | Count V-columns and R-columns in your file and match in Setup |
| Saving as `.csv` instead of `.xlsx` | Setup tab may not load it | Re-save from Excel as `.xlsx` format |
| Header row missing | First data row is treated as header | Always include column names in row 1 |
| Non-numeric entries (e.g., "N/A", "#REF!") | Parsing error during load | Replace all non-numeric cells with actual values or remove those rows |
| Training and test files have different columns | Prediction fails | Ensure both files have identical V-column headers |
