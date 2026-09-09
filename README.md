# Machine Learning for Virus Severity Prediction

## Overview

This project explores whether machine learning can predict a virus's severity risk based on information from its outer proteins.

The original research question was:

> **Is it possible for machine learning algorithms to predict whether a new virus has the highest severity risk based on its outer proteins?**

The project uses viral protein sequences, ESM-2 protein embeddings, biochemical features, and additional virus information to train several machine learning models.

The results were not sufficient to answer the original question. In particular, the experiment used information beyond the outer protein sequence, and the dataset and evaluation approach have several limitations that make it difficult to determine whether the models would work on a genuinely new virus.

This repository documents the approach, results, and the problems identified during the experiment.

---

## Project Goals

The main goals of the project were to:

* Collect viral protein sequence information
* Use ESM-2 to represent protein sequences numerically
* Create additional features from the protein sequences
* Categorize viruses into different severity levels
* Train multiple machine learning models
* Compare their performance
* Use SHAP to examine which features influenced predictions
* Test whether the models could identify viruses in the highest severity category

---

## Dataset

The dataset contains viral protein records that were manually associated with three severity categories:

| Label | Severity |
| ----- | -------- |
| 0     | Mild     |
| 1     | Medium   |
| 2     | High     |

Protein sequences are retrieved from NCBI using accession numbers.

The dataset also contains additional information about the viruses. Some of this information includes:

* CFR
* Incubation period
* Host range
* Zoonotic potential
* Viral family
* Cellular localization
* Protein information

Because of this, the final models are not based exclusively on outer-protein sequences.

---

## Protein Features

The project uses **ESM-2**, a protein language model, to convert protein sequences into numerical representations that can be used by machine learning models.

The notebook also calculates several traditional protein features, including:

* Protein length
* Amino-acid composition
* Molecular weight
* Charge
* Isoelectric point
* Instability
* Aromaticity
* GRAVY
* Secondary-structure information

The general process is:

```text
Protein Sequence
       ↓
      ESM-2
       ↓
Protein Embedding
       ↓
Feature Engineering
       ↓
Machine Learning Model
       ↓
Severity Prediction
```

---

## Machine Learning Models

Several models were tested as part of the experiment:

* XGBoost
* Random Forest
* LightGBM
* Soft-voting ensemble

Class weighting also accounted for differences in the number of examples in each severity category.

The notebook evaluates the models using metrics including:

* Accuracy
* Precision
* Recall
* F1 score
* Balanced accuracy
* ROC-AUC
* Confusion matrices
* Precision-recall curves

---

## Model Explainability

SHAP was used to look at which features had the largest influence on the model's predictions.

This helped clarify what the models used when making predictions.

However, a key limitation is that a feature being important to a model does not necessarily mean it is biologically responsible for a virus being more severe. SHAP describes the model's behavior rather than establishing biological causation.

---

## Results

The models found patterns in the available dataset, but the results were not sufficient to show that outer-protein information can reliably predict the severity of a new virus.

There are several reasons for this.

### The models use more than protein sequences

The original question focuses on outer proteins, but the models also use virus-level metadata.

Some of these variables would not necessarily be available for a newly discovered virus.

This means the experiment is closer to:

> Can machine learning classify known viruses using protein information and additional biological metadata?

rather than:

> Can the outer-protein sequence of a new virus be used to predict its severity?

These are different questions.

### Dataset size

The dataset is relatively small compared with the number of features ESM-2 generates.

This creates a risk that the models learn patterns specific to the dataset rather than patterns that generalize to other viruses.

### Related viruses

The experiment uses a standard train/test split.

Closely related viruses can therefore end up in both the training and testing sets. This can make the test problem easier than predicting a virus that is genuinely different from anything the model has seen before.

A better approach is to separate related sequences, or entire viral families, between the training and testing data.

### Data preprocessing

Some preprocessing is performed before the train/test split.

For a more rigorous experiment, learn preprocessing using the training data only, then apply it to the test data.

### Threshold tuning

The notebook also explores different prediction thresholds for identifying the High severity class.

Because the test data is involved in this process, it is not completely independent of model selection.

A separate validation set or cross-validation procedure would be better for selecting the threshold before evaluating the final model.

---

## What I Would Change in a Future Version

A second version of the project would focus more closely on the original research question.

The input would be limited to information that can actually be obtained from the outer protein:

```text
Outer Protein Sequence
          ↓
        ESM-2
          ↓
     Protein Features
          ↓
      ML Model
          ↓
 Severity Prediction
```

I would also:

1. Use a larger and more consistently labeled dataset.
2. Define the severity target using a standardized measurement.
3. Remove metadata that would not be available for a newly discovered virus.
4. Cluster related protein sequences before splitting the data.
5. Hold out entire sequence groups or viral families for testing.
6. Fit preprocessing only on the training data.
7. Use a validation set for model and threshold selection.
8. Compare ESM-2 against simpler protein-sequence baselines.
9. Evaluate how well the models generalize to viruses that are not closely related to the training examples.

The most important test is whether a model can predict a virus's severity category from a protein group completely absent from its training data.

---

## What This Project Shows

The main outcome of this project is that building a machine learning model and answering a biological research question are not necessarily the same thing.

The technical pipeline can work even if the experiment itself has limitations.

This project helped identify several issues that need to be addressed before claiming we can predict the severity of a new virus from its outer proteins.

---

## Technologies

* Python
* Pandas
* NumPy
* PyTorch
* Biopython
* NCBI Entrez
* Hugging Face Transformers
* Meta ESM-2
* Scikit-learn
* XGBoost
* LightGBM
* imbalanced-learn
* SHAP
* Matplotlib
* Jupyter Notebook

---

## Repository Structure

```text
virus-severity-protein-ml/
│
├── virus_severity_protein_ml.ipynb
├── README.md
├── requirements.txt
├── project.json
├── LICENSE
└── .gitignore
```

The notebook retrieves protein sequences from NCBI while running, so the generated datasets and model files are not included in the repository.

---

## Running the Notebook

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/virus-severity-protein-ml.git
cd virus-severity-protein-ml
```

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

Or on Linux/macOS:

```bash
source .venv/bin/activate
```

Install the required packages:

```bash
pip install -r requirements.txt
```

The notebook uses the `NCBI_EMAIL` environment variable for NCBI requests.

Windows PowerShell:

```powershell
$env:NCBI_EMAIL="your_email@example.com"
```

Linux/macOS:

```bash
export NCBI_EMAIL="your_email@example.com"
```

Then open the notebook with Jupyter:

```bash
jupyter notebook virus_severity_protein_ml.ipynb
```

---

## Project Status

**Inconclusive**

The experiment did not establish that machine learning can reliably predict whether a new virus has the highest severity risk based only on its outer proteins.

The current results are best viewed as an initial experiment that identifies several areas where the dataset and methodology need improvement.
