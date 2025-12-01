# Stress Detection from Wearable Signals

## Affective Computing Project

**Authors:** Miłosz Chojecki, Michał Biszczanik

---

## Overview

Binary classification of **stress vs. non-stress** states using physiological signals from a wrist-worn device (Empatica E4).

## Dataset: WESAD

**WESAD** (Wearable Stress and Affect Detection) - a multimodal dataset for stress detection.

| Property | Value |
|----------|-------|
| Subjects | 15 participants |
| Device | Empatica E4 (wrist-worn) |
| Signals | BVP (64 Hz), EDA (4 Hz), TEMP (4 Hz) |
| Labels | Baseline, Stress, Amusement, Meditation |

**Reference:** Schmidt, P., et al. (2018). *"Introducing WESAD, a Multimodal Dataset for Wearable Stress and Affect Detection."* ICMI 2018.

## Methodology

1. **Preprocessing** - 60s sliding windows with 50% overlap
2. **Feature extraction** - Statistical & physiological features using NeuroKit2:
   - EDA: tonic/phasic components, SCR peaks
   - BVP: HR, HRV metrics (RMSSD, SDNN, pNN50)
   - TEMP: mean, std, slope
3. **Classification** - Random Forest with balanced class weights
4. **Validation** - Leave-One-Subject-Out (LOSO) cross-validation

## Results

- **Accuracy:** ~75-85%
- **F1-Score:** ~70-80%
- Generalization validated on unseen subjects

## Requirements

```
neurokit2
scikit-learn
pandas
numpy
matplotlib
seaborn
```

## Usage

```bash
jupyter notebook stress_detection_wesad.ipynb
```

---

*Wrocław University of Science and Technology - Affective Computing Course*