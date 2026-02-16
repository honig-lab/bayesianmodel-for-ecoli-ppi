# Bayesian Integration of PPI Modeling (PrePPI-SM + ZEPPI + D-Script-TT)

This repository contains a naïve Bayesian model for integrating multiple protein–protein interaction (PPI) scores—**PrePPI-SM**, **ZEPPI**, and **D-Script-TT**—into a single combined score. The model was trained on **human PPIs** and used to generate predictions for *E. coli* protein–protein interactions.

## Overview

Modern PPI predictors capture different biophysical and evolutionary signals. This project combines three complementary methods:

- **PrePPI-SM**: Structure-based PPI scoring
- **ZEPPI**: Sequence-based PPI interface coevolution scoring
- **D-Script-TT**: Deep-learning based protein-language model scoring for PPIs

These features are fused using a **discretized naïve Bayesian classifier**, which computes bin-wise likelihood ratios for each feature and multiplies them into a global combined score.

The model uses:

- Automatic binning per feature (Doane rule)
- Laplace smoothing for zero-count bins
- Likelihood ratio estimation from positive vs. negative training PPIs
- A highly imbalanced training set (1:1000 positives:negatives), reflecting realistic genome-wide PPI sparsity

Once trained, the Bayesian model can generalize across species and is applied here to score *E. coli* PPIs.

## Repository Structure

```
.
├── notebooks/
│   ├── Train_on_human_DEMO.ipynb
│   ├── Test_on_ecoli_DEMO.ipynb
│
├── src/
│   ├── Bayes.py
│   └── evaluation.py
│
├── data/
│   ├── human_ZP_Bayes_ratio1000.pkl
│   ├── human_DS_Bayes_STRING.pkl
│   ├── ecoli_ROC_data.xlsx
│
└── README.md
└── requirements.txt

```

## How to Run

### 1. Clone the repository

```bash
git clone https://github.com/haiqingzhao/BayesianModel-for-Ecoli-PPI.git
cd BayesianModel-for-Ecoli-PPI
```

### 2. Create Environment and Install Dependencies
Requires Python 3.9 or later.

```bash
conda create -n bayes python=3.9
conda activate bayes
pip install -r requirements.txt
```

### 3. Prepare the Training and Testing Data

Training the Bayesian model requires **three feature tables** and **two label datasets** for human PPIs. Testing the Bayesian model requires **three feature tables** for *E. coli* PPIs. Details of these files are described in the below.

#### 3.1 Training Data (Human)

##### 3.1.1 Feature Tables (Human)

Generate human PPI interactome predictions separately using PrePPI, ZEPPI, and D-Script (TT), and save the results in CSV format. Each output file should contain two columns: `UniprotID_pair` and `score`. The `UniprotID_pair` column should list the predicted protein pairs in the format `UniProtID1_UniProtID2` (two UniProt IDs separated by an underscore). The score column should contain the corresponding prediction score. For convenience, we uploaded the three genome-wide predictions that we used in this work in Figshare (https://dx.doi.org/10.6084/m9.figshare.30822977).

##### 3.1.2 Training Labels (Human TP/TN)

Prepare TP and TN datasets in CSV format. Each dataset consists of two columns of data: `UniprotID_pair` and `label`, where `label = 1` (TP) or `label = 0` (TN). The TP dataset can be downloaded from [HINT website](https://hint.yulab.org/download) (Literature curated binary), or [STRING website](https://string-db.org/cgi/download) (physical subnetwork). The TN dataset was curated in-house by extracting experimentally validated PPIs from all possible pairwise protein combinations. For convenience, we uploaded the experimental PPI datasets that we used in this work in Figshare (https://dx.doi.org/10.6084/m9.figshare.30822977).

#### 3.2 Testing Data (*E. coli*)

##### 3.2.1 Feature Tables (*E. coli*)

Generate *E. coli* PPI interactome predictions from PrePPI, ZEPPI, and D-Script (TT) and save the results in csv format. Each output file should contain two columns: `UniprotID_pair` and `score`. The `UniprotID_pair` column should list the predicted protein pairs in the format `UniProtID1_UniProtID2` (two UniProt IDs separated by an underscore). The score column should contain the corresponding prediction score. As a reference, we uploaded the three genome-wide predictions of *E. coli* that we used in this work in Figshare (https://dx.doi.org/10.6084/m9.figshare.30822977).

### 4. Run training

Open the training notebook:

```
Train_on_human_DEMO.ipynb
```

It will need ~35GB memory to run it. The trained models of ZEPPI-LR and TT-LR on human — used in the paper — are provided as references (under `/data` folder). 

### 5. Run prediction for *E. coli*

Open:

```
Test_on_ecoli_DEMO.ipynb
```
The final integrated prediction file will be generated and stored at assigned path. As a reference, we uploaded the final prediction of *E. coli* PPI with three separate and one integrated clue in Figshare (https://dx.doi.org/10.6084/m9.figshare.30822977). To benchmark our performance, the FPR and TPR that are used to calculate ROC curves (Figure S1) are uploaded as `/data/ecoli_ROC_data.xlsx`. 


## Contact

For questions, please reach out to:

**Haiqing Zhao hz2592@cumc.columbia.edu; hazhao@UTMB.EDU**  

