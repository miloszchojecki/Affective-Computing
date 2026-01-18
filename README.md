# Stress Detection from Wearable Signals

## Affective Computing Project

**Authors:** Miłosz Chojecki, Michał Biszczanik

---

## Overview

Binary classification of **stress vs. non-stress** states using physiological signals from wearable devices. The project explores stress detection using the WESAD dataset and compares performance between wrist-worn and chest-worn sensors.

---

## Dataset: WESAD

**WESAD** (Wearable Stress and Affect Detection) - a multimodal dataset for stress detection.

| Property | Value |
|----------|-------|
| Subjects | 15 participants |
| Devices | Empatica E4 (wrist), RespiBAN (chest) |
| Protocol | Baseline → Stress (TSST) → Amusement → Meditation |
| Labels | 0: not defined, 1: baseline, 2: stress, 3: amusement, 4: meditation |

**Reference:** Schmidt, P., et al. (2018). *"Introducing WESAD, a Multimodal Dataset for Wearable Stress and Affect Detection."* ICMI 2018.

---

## Experiments

### Experiment 1: Stress Detection from Wrist Signals
**Notebook:** `stress_detection_wesad.ipynb`

#### Objective
Detect stress states using only wrist-worn device (Empatica E4) signals, simulating a smartwatch scenario.

#### Signals Used
| Signal | Sampling Rate | Description |
|--------|---------------|-------------|
| BVP | 64 Hz | Blood Volume Pulse (photoplethysmography) |
| EDA | 4 Hz | Electrodermal Activity (skin conductance) |
| TEMP | 4 Hz | Skin temperature |

#### Methodology
1. **Windowing:** 60-second windows with 50% overlap (30s shift)
2. **Feature Extraction** using NeuroKit2:
   - **EDA features:** mean, std, min, max, range, tonic/phasic components, SCR peaks count
   - **BVP/HR features:** heart rate (mean, std, min, max), HRV metrics (RMSSD, SDNN, pNN50)
   - **TEMP features:** mean, std, slope
3. **Classification:** Random Forest (100 trees, max_depth=10, balanced class weights)
4. **Validation:** Leave-One-Subject-Out (LOSO) cross-validation

#### Results

| Metric | Value |
|--------|-------|
| **Accuracy** | 88.5% (±5.2%) |
| **F1-Score** | 80.5% (±13.8%) |
| **Best subject** | S16: 96.7% accuracy |
| **Worst subject** | S14: 80.2% accuracy |

**Per-subject results (LOSO):**

| Subject | Accuracy | F1-Score | Stress Ratio |
|---------|----------|----------|--------------|
| S2 | 93.9% | 89.2% | 20.2% |
| S3 | 85.2% | 78.0% | 19.7% |
| S4 | 95.0% | 91.9% | 19.2% |
| S5 | 89.1% | 84.2% | 19.3% |
| S6 | 88.2% | 84.7% | 20.2% |
| S7 | 87.3% | 83.4% | 19.5% |
| S8 | 88.3% | 77.1% | 20.8% |
| S9 | 90.9% | 83.2% | 19.0% |
| S10 | 91.9% | 87.8% | 21.0% |
| S11 | 86.8% | 83.3% | 20.7% |
| S13 | 91.8% | 86.6% | 19.7% |
| S14 | 80.2% | 44.5% | 19.8% |
| S15 | 85.1% | 69.2% | 20.7% |
| S16 | 96.7% | 94.6% | 19.8% |
| S17 | 79.3% | 68.9% | 21.5% |

---

### Experiment 2: Wrist vs Chest Sensor Comparison
**Notebook:** `chest_vs_wrist_comparison.ipynb`

#### Objective
Compare stress detection performance between comfortable wrist-worn device and professional chest-worn sensor to answer: **Is the accuracy loss worth the comfort gain?**

#### Devices Compared

| Device | Location | Signals | Sampling Rate |
|--------|----------|---------|---------------|
| Empatica E4 | Wrist | BVP, EDA, TEMP | 4-64 Hz |
| RespiBAN | Chest | ECG, EDA, TEMP, Resp | 700 Hz |

#### Key Differences
- **Chest:** Direct ECG signal vs optical BVP from wrist
- **Chest:** Higher sampling rate (700 Hz vs 4-64 Hz)
- **Chest:** Additional respiration signal
- **Wrist:** More prone to motion artifacts
- **Wrist:** More comfortable for daily use

#### Features Extracted
- **Wrist (16 features):** EDA stats + tonic/phasic, BVP stats, HR, HRV (RMSSD, SDNN), TEMP
- **Chest (19 features):** ECG-based HR/HRV, EDA stats + SCR, TEMP, respiration rate

#### Results

| Metric | Wrist (Empatica E4) | Chest (RespiBAN) | Difference |
|--------|---------------------|------------------|------------|
| **Accuracy** | 89.2% | 88.4% | +0.8% (wrist) |
| **F1-Score** | 83.9% | 81.5% | +2.4% (wrist) |
| **Sensitivity** | 72.7% | 62.2% | +10.5% (wrist) |
| **Specificity** | 93.9% | 95.8% | -1.9% (chest) |

> **Metrics explained:**
> - **Sensitivity (Recall)** = TP / (TP + FN) — How many actual stress cases were correctly detected? Higher = fewer missed stress episodes.
> - **Specificity** = TN / (TN + FP) — How many non-stress cases were correctly identified? Higher = fewer false alarms.

#### Key Findings
1. **Wrist performs comparably** to chest sensor (difference <1% accuracy)
2. **Wrist has better sensitivity** (72.7% vs 62.2%) - better at detecting stress
3. **Chest has better specificity** (95.8% vs 93.9%) - fewer false alarms
4. **Wrist is sufficient** for practical daily stress monitoring applications

#### Conclusion
> The wrist-worn device (smartwatch) provides **acceptable accuracy** for stress detection while offering significantly **better comfort** for everyday use. The chest sensor provides marginal improvements that don't justify the discomfort for non-clinical applications.

---

### Experiment 3: Multiclass Affective State Detection
**Notebook:** `multiclass_detection_wesad.ipynb`

#### Objective
Classify **four distinct affective states** (baseline, stress, amusement, meditation) using wrist-worn device signals, extending beyond binary stress detection to comprehensive emotional state recognition.

#### Signals Used
Same as Experiment 1 - Empatica E4 wrist device:
| Signal | Sampling Rate | Description |
|--------|---------------|-------------|
| BVP | 64 Hz | Blood Volume Pulse (photoplethysmography) |
| EDA | 4 Hz | Electrodermal Activity (skin conductance) |
| TEMP | 4 Hz | Skin temperature |

#### Methodology
1. **Classes:** 4-class problem (baseline, stress, amusement, meditation)
2. **Windowing:** 60-second windows with no overlap (60s shift) 
3. **Feature Extraction** using NeuroKit2:
   - Same feature set as binary experiments (16 features total)
   - EDA, BVP/HR, and TEMP statistical and physiological features
4. **Classification:** Random Forest with balanced class weights
5. **Validation:** Leave-One-Subject-Out (LOSO) cross-validation

#### Results

| Metric | Value |
|--------|-------|
| **Overall Accuracy** | 62.7% (±12.7%) |
| **F1-Score (macro)** | 51.3% (±12.6%) |
| **F1-Score (weighted)** | 59.1% |

**Per-class performance:**

| Class | Precision | Recall | F1-Score | Support |
|-------|-----------|---------|----------|---------|
| **Baseline** | 62% | 79% | 70% | 290 samples |
| **Stress** | 77% | 79% | 78% | 165 samples |
| **Amusement** | 17% | 2% | 4% | 92 samples |
| **Meditation** | 54% | 53% | 53% | 194 samples |

**Per-subject results (selected):**

| Subject | Accuracy | F1-Score |
|---------|----------|----------|
| S16 | 79.2% | 69.5% |
| S11 | 78.4% | 62.3% |
| S10 | 72.5% | 58.5% |
| S9 | 78.4% | 60.5% |
| S4 | 67.3% | 51.7% |
| S14 | 32.0% | 17.7% |

#### Key Findings
1. **Stress detection remains robust** (77% precision, 78% F1-score) even in multiclass setting
2. **Baseline state well-recognized** (79% recall) - good reference state detection  
3. **Amusement poorly classified** (2% recall) - challenging to distinguish from other states
4. **Meditation moderately detectable** (53% F1-score) - distinct but not always clear
5. **High inter-subject variability** (accuracy range: 32-79%) - individual differences matter

#### Challenges
- **Class imbalance:** Amusement significantly underrepresented (92 vs 290 baseline samples)
- **Subtle physiological differences** between some emotional states
- **Individual response patterns** vary greatly between subjects
- **Wrist-based sensors** may not capture all relevant physiological markers for emotion

#### Comparison with Binary Classification
- **Stress vs. baseline binary:** ~88% accuracy
- **Stress in multiclass:** 78% F1-score  
- **Performance trade-off:** Adding more classes reduces individual class accuracy but provides richer emotional context

---

## Project Structure

```
Affective-Computing/
├── stress_detection_wesad.ipynb       # Binary stress detection pipeline  
├── chest_vs_wrist_comparison.ipynb    # Sensor comparison experiment
├── multiclass_detection_wesad.ipynb   # 4-class affective state detection
├── wesad_features.csv                 # Extracted features dataset
├── loso_results.csv                   # Per-subject LOSO results
├── chest_vs_wrist_comparison.csv      # Comparison results  
├── stress_detection_model.joblib      # Trained binary model
└── archive/WESAD/                     # Dataset (not in repo)
```

## Requirements

```
neurokit2
scikit-learn
pandas
numpy
matplotlib
seaborn
tqdm
```

## Usage

```bash
# Clone repository
git clone https://github.com/miloszchojecki/Affective-Computing.git

# Run main experiment (binary stress detection)
jupyter notebook stress_detection_wesad.ipynb

# Run sensor comparison
jupyter notebook chest_vs_wrist_comparison.ipynb

# Run multiclass affective state detection  
jupyter notebook multiclass_detection_wesad.ipynb
```

---

*Wrocław University of Science and Technology - Affective Computing Course*

