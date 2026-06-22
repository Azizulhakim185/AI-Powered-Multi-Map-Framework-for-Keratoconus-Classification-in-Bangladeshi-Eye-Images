# Keratoconus AI Screening System

> **Undergraduate Thesis | CSE4100 & CSE4250 | Ahsanullah University of Science and Technology (AUST)**
> 
> **Author:** Azizul Hakim (1st Author) | **Supervisor:** Prof. Dr. Kazi A Kalpoma
> **Clinical Collaboration:** National Institute of Ophthalmology and Hospital (NIOH), Dhaka

---

## What is Keratoconus?

Keratoconus is a progressive eye disease where the cornea thins and bulges into a cone shape. If not caught early, it causes severe vision loss and may require a cornea transplant.

**The problem:** Diagnosis requires a specialist to read 7 complex corneal maps from a Pentacam machine. In Bangladesh, these specialists are only in big cities. Rural patients get diagnosed too late.

**Our solution:** An AI system that reads all 7 maps automatically and flags Normal, Suspect, or Keratoconus cases — bringing specialist-level screening to every clinic.

---

## What This System Does

Upload 7 Pentacam corneal topography maps → Get instant AI prediction with explainable heatmap

| Output | Meaning |
|--------|---------|
| **Normal** | Healthy eye, no action needed |
| **Suspect** | Early signs, monitor closely |
| **Keratoconus** | Confirmed disease, refer for treatment |

---

## Key Results

| Test | Accuracy | What It Means |
|------|----------|---------------|
| International Test (Brazil) | **80.2%** | Works on different population |
| Bangladesh Test (Local) | **74.7%** | Validated on our own patients |
| Binary Screening (Normal vs. Abnormal) | **84.5%** | Safe for first-level screening |
| Sensitivity (catching real cases) | **93.3%** | Misses very few sick patients |

**Why Bangladesh accuracy is lower:** South Asian corneas differ genetically. Most AI systems are trained on Western/Latin American eyes. This study is the **first to validate keratoconus AI on Bangladeshi patients**.

---

## The 7 Corneal Maps

| Map | What It Shows | Why It Matters |
|-----|---------------|--------------|
| **SAG_A** | Front curve of cornea | Keratoconus makes this steeper |
| **SAG_P** | Back curve of cornea | Early changes appear here |
| **ELV_A** | Front elevation | Detects bulging |
| **ELV_P** | Back elevation | Catches subtle changes |
| **CT_A** | Cornea thickness | Disease thins the cornea |
| **EC_A** | Front ectasia index | Measures front warping |
| **EC_P** | Back ectasia index | Measures back warping |

The AI reads all 7 together — like a specialist who never gets tired.

---

## How It Works

```
7 Pentacam Maps → Preprocessing → DenseNet-121 Feature Extraction 
→ Feature Fusion → SVM-RBF Classification → Prediction + Grad-CAM Heatmap
```

**DenseNet-121:** Pre-trained CNN that extracts 1,024 features per map  
**Feature Fusion:** Combines all 7 maps into one unified representation  
**SVM-RBF:** Kernel classifier for final Normal/Suspect/Keratoconus decision  
**Grad-CAM:** Heatmap showing exactly which parts of the cornea the AI focused on

---

## Dataset

| Source | Patients | Eyes | Notes |
|--------|----------|------|-------|
| **International (Brazil)** | 573 | 573 | Kaggle dataset, benchmark comparison |
| **Bangladesh (NIOH, Dhaka)** | 142 | 284 | **First local dataset for this condition** |

**Bangladesh data collection:**
- Printed Pentacam reports manually processed
- Computational labels using BAD-D scoring, verified by Dr. S M Enamul Haque (NIOH)
- Patient privacy protected, no identifying information shared

---

## My Contribution (1st Author)

- Designed multi-map fusion architecture (7 maps → unified feature vector)
- Led Bangladeshi data collection at NIOH, Dhaka (142 patients, 284 eyes)
- Implemented DenseNet-121 feature extraction and SVM-RBF classification pipeline
- Performed cross-ethnic validation (Brazilian vs. Bangladeshi performance analysis)
- Integrated Grad-CAM interpretability for clinical transparency
- Manuscript in preparation for journal submission

**Collaborators:** Morshadul Islam Siam (2nd Author), A M Rohan (3rd Author)

---

## Repository Structure

```
keratoconus-ai/
├── README.md              # This file
├── requirements.txt       # Python dependencies
├── src/
│   ├── preprocess.py      # Image cropping, resizing, grayscale
│   ├── extract_features.py # DenseNet-121 feature extraction
│   ├── train_classifier.py # SVM-RBF training with SMOTE
│   ├── evaluate.py        # Metrics, confusion matrix, ROC
│   └── gradcam_visualize.py # Explainability heatmaps
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_single_map_baseline.ipynb
│   ├── 03_multi_map_fusion.ipynb
│   └── 04_model_comparison.ipynb
├── results/
│   ├── confusion_matrices/
│   ├── roc_curves/
│   └── gradcam_heatmaps/
└── .gitignore
```

---

## How to Run

### Prerequisites
- Python 3.10+
- 8GB+ RAM
- GPU optional (speeds up feature extraction)

### Setup
```bash
git clone https://github.com/Azizulhakim185/Keratoconus_ai.git
cd Keratoconus_ai
pip install -r requirements.txt
```

### Run Full Pipeline
```bash
python src/preprocess.py        # Prepare images
python src/extract_features.py   # Extract DenseNet-121 features
python src/train_classifier.py   # Train SVM-RBF with SMOTE
python src/evaluate.py           # Generate metrics and plots
python src/gradcam_visualize.py  # Create explainability heatmaps
```

---

## Results Summary

### Per-Map Performance (DenseNet-121 + SVM)

| Map | Validation Acc | Test Intl Acc | Test BD Acc |
|-----|---------------|---------------|-------------|
| CT_A | 67.4% | 68.6% | 62.0% |
| EC_A | **81.4%** | 69.8% | 66.2% |
| EC_P | 74.4% | 64.0% | 70.4% |
| ELV_A | 75.6% | 72.1% | 73.2% |
| ELV_P | 66.3% | 61.6% | **78.9%** |
| SAG_A | **81.4%** | 70.9% | 66.2% |
| SAG_P | 72.1% | 68.6% | 49.3% |
| **Fusion (All 7)** | **82.6%** | **80.2%** | **74.7%** |

**Fusion beats every single map.** No individual map captures the full picture.

### Model Comparison (Multi-Map Fusion)

| Model | Val F1 | Test Intl F1 | Test BD F1 |
|-------|--------|--------------|------------|
| EfficientNet-B4 | 0.808 | 0.770 | 0.756 |
| ResNet-50 | 0.794 | 0.757 | 0.707 |
| MobileNet-V3 | 0.744 | 0.792 | 0.767 |
| **DenseNet-121** | **0.811** | **0.802** | **0.763** |

**DenseNet-121 wins** — best balance of accuracy and cross-population generalization.

---

## Explainability: Grad-CAM

The AI focuses on clinically relevant regions:

- **SAG_A (front curve):** Lower cornea — where keratoconus starts
- **ELV_A (front elevation):** Bulging areas — disease progression
- **CT_A (thickness):** Thinnest point — structural weakness
- **EC_A (ectasia index):** Instability markers

This builds **clinical trust** — doctors can see *why* the AI made its decision.

---

## Limitations & Future Work

| Limitation | Future Direction |
|-----------|------------------|
| 142 Bangladeshi patients (small) | Expand to 500+ patients, multi-center |
| Cross-sectional only | Longitudinal tracking with recurrent models |
| Imaging-only | Integrate clinical variables (age, family history, Kmax) |
| GPU-dependent | Model compression for edge/mobile deployment |
| 3-class output | Binary screening for primary care deployment |

---

## Citation

If you use this work in your research, please cite:

```bibtex
@thesis{azizul2026keratoconus,
  author    = {Azizul Hakim and Morshadul Islam Siam and A M Rohan},
  title     = {AI-Powered Multi-Map Framework for Keratoconus Classification in Bangladeshi Eye Images},
  school    = {Ahsanullah University of Science and Technology},
  year      = {2026},
  type      = {Undergraduate Thesis},
  supervisor = {Prof. Dr. Kazi A Kalpoma}
}
```

---

## Contact

| Role | Contact |
|------|---------|
| **Author** | Azizul Hakim — azizulhakimshuvo185@gmail.com |
| **Supervisor** | Prof. Dr. Kazi A Kalpoma — AUST CSE |
| **Clinical Partner** | Dr. S M Enamul Haque — NIOH, Dhaka |

**Interested in collaboration?** We're open to expanding the Bangladeshi dataset, testing on other South Asian populations, and clinical deployment partnerships.

---

## License

This project is released under the **MIT License**.

The **international dataset** comes from Kaggle under its own license. The **Bangladeshi dataset** is not publicly available due to patient privacy protections. Contact us for collaboration inquiries.

---

> *"Early detection saves sight. AI can help make that happen everywhere."*
>
> — Azizul Hakim, 2026

---

**Last Updated:** June 2026  
**Status:** Manuscript in Preparation
