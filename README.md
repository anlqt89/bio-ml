# Ventricular Fibrillation Detection from ECG Signals

This project implements a complete **machine learning pipeline for detecting Ventricular Fibrillation (VF)** from ECG signals using the **CU Ventricular Tachyarrhythmia Database**.  
The system processes raw ECG recordings, segments them into fixed-length windows, extracts signal features, and trains machine learning models to classify **Normal vs VF** rhythms.

---

## Dataset

- **Source**: CU Ventricular Tachyarrhythmia Database  
- **Format**: WFDB (`.dat`, `.hea`, `.atr`)
- **Signals**: ECG waveforms with expert annotations
- **Target classes**:
  - `Normal` – Normal sinus rhythm
  - `VF` – Ventricular Fibrillation  
- Other annotation symbols are ignored or excluded during segmentation.

---

## Pipeline Overview

# Ventricular Fibrillation Detection from ECG Signals

This project implements a complete **machine learning pipeline for detecting Ventricular Fibrillation (VF)** from ECG signals using the **CU Ventricular Tachyarrhythmia Database**.  
The system processes raw ECG recordings, segments them into fixed-length windows, extracts signal features, and trains machine learning models to classify **Normal vs VF** rhythms.

---

## Dataset

- **Source**: CU Ventricular Tachyarrhythmia Database  
- **Format**: WFDB (`.dat`, `.hea`, `.atr`)
- **Signals**: ECG waveforms with expert annotations
- **Target classes**:
  - `Normal` – Normal sinus rhythm
  - `VF` – Ventricular Fibrillation  
- Other annotation symbols are ignored or excluded during segmentation.

---

## Pipeline Overview

Raw ECG
↓
Preprocessing
↓
Segmentation (5s windows, 50% overlap)
↓
Feature Extraction (33 features)
↓
Machine Learning Classification


---

## 1. Record Loading & Annotation Analysis

- Reads ECG signals and annotations using `wfdb`
- Summarizes annotation types per record
- Validates signal integrity (NaN checks)
- Visualizes ECG signals with annotation markers

**Purpose**:  
Verify correct annotation loading and understand class distribution before modeling.

---

## 2. Preprocessing

Each ECG signal is standardized using:

- **Band-pass filtering (0.5–45 Hz)**  
  Removes baseline drift and high-frequency noise
- **Z-score normalization**  
  Ensures amplitude consistency across records
- **Resampling to 250 Hz**  
  Standardizes time resolution
- **Annotation realignment**  
  Rescales annotation indices after resampling

**Output**: Clean, normalized ECG signals with aligned annotations.

---

## 3. Signal Segmentation

- Window length: **5 seconds**
- Overlap: **50%**
- Sliding-window segmentation

**Labeling rules**:
- If **any VF annotation** appears in a window → label as `VF`
- If **only Normal annotations** appear → label as `Normal`
- Mixed or ambiguous windows are discarded

**Purpose**:  
Convert long ECG recordings into independent labeled samples for ML.

---

## 4. Feature Extraction

Each ECG segment is transformed into a **33-dimensional feature vector**, including:

### Time-domain features
- Mean, median, standard deviation, RMS
- Skewness, kurtosis, peak-to-peak amplitude
- QRS interval statistics
- Local maxima/minima counts

### Frequency-domain features
- Dominant frequency
- Spectral entropy
- Total power
- Median frequency

### Wavelet features
- Energy and standard deviation across multiple wavelet levels (`db4`)

### Morphological features
- First and second derivatives
- Slope change counts

---

## 5. Dataset Summary

After segmentation and feature extraction:

Total segments: 5075
Features per segment: 33
Class distribution:
Normal: 4920 (~97%)
VF: 155 (~3%)


⚠️ The dataset is **highly imbalanced**, which strongly affects model evaluation.

---

## 6. Model Training

- **Record-wise split** using `GroupShuffleSplit` to prevent data leakage
- Preprocessing pipeline:
  - Missing-value imputation
  - Feature scaling
  - SMOTE oversampling (training only)
- Models evaluated:
  - Random Forest
  - Linear SVM
  - Multilayer Perceptron (MLP)

---

## 7. Results (Random Forest)

Normal:
Precision: 0.99
Recall: 0.88
VF:
Precision: 0.13
Recall: 0.69
Overall Accuracy: 0.88


### Interpretation
- The model detects **~69% of VF events** (high sensitivity)
- Precision is low due to false positives
- Accuracy is misleading due to class imbalance
- Suitable as a **VF screening-stage model**, not a final diagnostic system

---

## Visualization & Validation

The project includes visualization steps to:
- Inspect ECG signals and annotations
- Validate segmentation correctness
- Compare Normal vs VF segments
- Display confusion matrices (absolute and normalized)
- Inspect extracted feature values

These steps serve as **sanity checks** and improve interpretability.

---

## Key Takeaways

- Feature-based ML can detect VF with high sensitivity
- Class imbalance heavily impacts precision
- Recall (sensitivity) is the most clinically relevant metric
- Visual and statistical validation steps are essential

---

## Technologies Used

- Python
- NumPy, SciPy
- WFDB
- PyWavelets
- scikit-learn
- imbalanced-learn
- Matplotlib, Seaborn

---

## Disclaimer

This project is for **educational and research purposes only** and is **not intended for clinical use**.

---

## Author

Developed as part of a machine learning project on ECG signal analysis and arrhythmia detection.

