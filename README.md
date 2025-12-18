
# Temporal Airbnb Seasonality + Modeling (EAS 510 Extra Credit)

Hey there! This repo has my solution for the **Temporal Airbnb Seasonality and Modeling** extra credit assignment. Basically, I built a night-level panel using **InsideAirbnb** data from `listings.csv` and `calendar.csv`, did some seasonality plots for prices and booking chances, set up a temporal train/valid/test split to avoid any data leakage, and trained XGBoost and Neural Net models for predicting nightly prices and whether a place gets booked. Oh, and I logged everything with TensorBoard for good measure.

> Just a heads up: As per the assignment rules, I didn't include the raw InsideAirbnb CSVs in this GitHub repo. You'll have to download them yourself and put them in the right folders.

---

## Repo Structure (Key Files)

- `bonus.ipynb` → This is the main notebook—run this to see everything in action.
- `requirements.txt` → All the Python packages you'll need.
- `Austin/`, `Chicago/`, `Santa_Cruz/`, `WashingtonDC/` → Folders where you should drop the downloaded data.
- `logs/` → TensorBoard logs that get created after training.
- `Tensorboard_SS/` → Screenshots of TensorBoard that I saved and used in the notebook.
- `cache_panels/` → Optional cached panels (only if you enable that in the code).

---

## 1) Grab the Data from InsideAirbnb

For each city, make sure to download **both** files:
- `listings.csv.gz`
- `calendar.csv.gz`

And stick to these **exact archived snapshot dates**:

| City | Snapshot Folder | Archived Date |
|------|------------------|--------------|
| Austin | `3625` | March 6, 2025 |
| Austin | `121424` | December 14, 2024 |
| Chicago | `31125` | March 11, 2025 |
| Chicago | `121824` | December 18, 2024 |
| Santa Cruz | `32825` | March 28, 2025 |
| Santa Cruz | `123125` | December 31, 2025 |
| WashingtonDC | `31325` | March 13, 2025 |
| WashingtonDC | `121825` | December 18, 2025 |

Sometimes InsideAirbnb gives you `.csv.gz` files. If that happens, just unzip them so you have plain `.csv` files.

---

## 2) Where to Put the Files (Super Important)

Once you've cloned this repo, organize the files like this (using relative paths):

```

Austin/
3625/
listings.csv
calendar.csv
121424/
listings.csv
calendar.csv

Chicago/
31125/
listings.csv
calendar.csv
121824/
listings.csv
calendar.csv

Santa_Cruz/
32825/
listings.csv
calendar.csv
123125/
listings.csv
calendar.csv

WashingtonDC/
31325/
listings.csv
calendar.csv
121825/
listings.csv
calendar.csv

````

---

## 3) Set Up Your Environment

```bash
python -m venv venv
# To activate it:
# On Windows: venv\Scripts\activate
# On Mac/Linux: source venv/bin/activate

pip install -r requirements.txt
````

---

## 4) Run the Notebook

Fire up Jupyter and open:

* `bonus.ipynb`

Then just run through the cells!

---

## 5) Check Out TensorBoard

The logs end up in `logs/` after training. To view them:

### Option A (in the terminal)

```bash
tensorboard --logdir logs
```

### Option B (right in the notebook)

Use the TensorBoard magic commands that are already in the notebook cells.

---



---

## What the Notebook Actually Does (Quick Pipeline Overview)

The project runs the same process for **each city + snapshot folder** (like `Austin/3625` or `Chicago/121824`):

### 1) Load and Clean the Raw Files
- Pull in `listings.csv` and `calendar.csv` for a specific snapshot.
- Tidy up the key columns for modeling (for example, turn price strings like `$123.00` into numbers, deal with any missing values).
- Keep only the columns we need for the seasonality stuff and ML features.

### 2) Build a Night-Level Panel (Based on Calendar)
- Use `calendar.csv` as the foundation—it's night-level, so one row per listing per date.
- Set up the main targets:
  - **Regression target:** `price` (the nightly price)
  - **Classification target:** `is_booked` (from availability—available means 0, booked means 1)
- Bring in listing details from `listings.csv` and attach them to each calendar row (things like room type, property type, bedrooms, host info, neighborhood, etc.).

### 3) Seasonality Analysis (The Required Plots)
For each snapshot, I made plots for:
- **Average price by month**
- **Booking probability by month**
- **Weekend vs weekday comparison** for both price and booking chances
- **Monthly price trends broken down by room_type**

### 4) Temporal Split (No Sneaky Leakage)
- Split the data **by time** into train/validation/test using months (not a random shuffle).
- Fit the preprocessing (imputers, scaling, one-hot encoding) **only on the train set**, then apply it to valid and test.

### 5) Train and Evaluate Models (For Both Targets)
For each snapshot, I trained both types of models:

**A) XGBoost**
- `XGBRegressor` for predicting nightly prices
- `XGBClassifier` for predicting if it's booked
With evaluations using:
- For regression: **RMSE, MAE**
- For classification: **AUC, Accuracy**

**B) Neural Nets**
- NN Regressor for prices
- NN Classifier for bookings
Same metrics as above.

### 6) TensorBoard Logging + Screenshots
- Saved training curves to `logs/` for both NN models:
  - Regression: loss and MAE over time
  - Classification: accuracy and AUC curves
- Snapped screenshots and put them in `Tensorboard_SS/`, then embedded them in the report.

### 7) Final Write-Up
- Summarized the seasonality patterns across all the cities and snapshots
- Explained the temporal split and why it stops data leakage
- Compared how XGBoost and NNs performed for price vs. booking predictions
- Talked about what TensorBoard showed (like if there was overfitting or if training was stable)
- Shared some business insights (like which prediction is more valuable and why)
