# Proposed Model (CNN+OS Model) for Software Change-Proneness Prediction

This repository contains a Google Colab implementation of a hybrid machine-learning and deep-learning framework for predicting **change-prone software modules** from object-oriented software metrics.

The proposed framework is displayed throughout the experimental results as:

> \*\*Proposed Model (CNN+OS model)\*\*

Internally, the framework combines **Random Over-Sampling**, **one-dimensional convolutional neural-network feature learning**, **support vector machines**, and **validation-based probability fusion**.

\---

## Project Overview

Software change-proneness prediction helps identify modules that are more likely to require modification during software maintenance. Early identification of such modules can support:

* maintenance planning;
* testing prioritization;
* software-quality improvement;
* resource allocation;
* defect-risk analysis; and
* reduction of maintenance cost.

The notebook generates a reproducible software-metrics dataset, preprocesses the data, handles class imbalance, trains several baseline models, trains the proposed hybrid model, evaluates all methods, generates comparison tables and figures, and exports the results to Microsoft Excel.

\---

## Proposed Architecture

The main processing pipeline is:

```text
Software Metrics Dataset
          |
          v
Train / Validation / Test Split
          |
          v
Standardization
          |
          v
Random Over-Sampling
          |
          v
One-Dimensional CNN Feature Learning
          |
          +-----------------------------+
          |                             |
          v                             v
SVM on Original Features      SVM on CNN Deep Features
          |                             |
          +--------------+--------------+
                         |
                         v
          Validation-Based Probability Fusion
                         |
                         v
             Optimal Threshold Selection
                         |
                         v
        Change-Prone / Not Change-Prone Prediction
```

The model name shown in all tables and plots remains **Proposed Model (CNN+OS model)**.

\---

## Main Features

* Reproducible synthetic software-metrics dataset
* Stratified training, validation, and test partitions
* Standardization fitted only on training data
* Random Over-Sampling for class-imbalance handling
* SMOTE and SMOTEENN comparison
* Functional Keras CNN implementation
* CNN-based compact deep-feature extraction
* SVM hyperparameter tuning through validation data
* Original-feature and deep-feature SVM classifiers
* Validation-based probability-fusion weight selection
* Validation-based optimal threshold selection
* Baseline comparison with:

  * Support Vector Machine
  * Random Forest
  * Random Over-Sampling with CatBoost
  * Convolutional Neural Network
  * Recurrent Neural Network
  * GNN-like neural architecture
* Automatic result export to Excel
* Model serialization for later inference
* Publication-ready comparison graphs

\---

## Dataset

The notebook generates a synthetic binary-classification dataset with **12,000 samples** and **20 software metrics**.

### Input Metrics

|Metric|General Meaning|
|-|-|
|`wmc`|Weighted Methods per Class|
|`dit`|Depth of Inheritance Tree|
|`noc`|Number of Children|
|`cbo`|Coupling Between Objects|
|`rfc`|Response for a Class|
|`lcom`|Lack of Cohesion in Methods|
|`ca`|Afferent Coupling|
|`ce`|Efferent Coupling|
|`npm`|Number of Public Methods|
|`lcom3`|LCOM3 cohesion metric|
|`loc`|Lines of Code|
|`dam`|Data Access Metric|
|`moa`|Measure of Aggregation|
|`mfa`|Measure of Functional Abstraction|
|`cam`|Cohesion Among Methods|
|`ic`|Inheritance Coupling|
|`cbm`|Coupling Between Methods|
|`amc`|Average Method Complexity|
|`max\_cc`|Maximum Cyclomatic Complexity|
|`avg\_cc`|Average Cyclomatic Complexity|

### Target

|Field|Meaning|
|-|-|
|`bug`|Binary class: `1` for change-prone and `0` for not change-prone|

The generated dataset is saved as:

```text
synthetic\_change\_proneness\_paper\_like.csv
```

\---

## Data Partitioning

The dataset is divided using stratified sampling:

|Partition|Percentage|
|-|-:|
|Training|64%|
|Validation|16%|
|Testing|20%|

The validation set is used for hyperparameter selection, probability-fusion selection, and threshold optimization. The test set is reserved for final performance evaluation.

\---

## Proposed CNN Configuration

The CNN feature extractor contains:

```text
Input
  |
Conv1D: 192 filters, kernel size 3, ReLU
  |
Batch Normalization
  |
Conv1D: 128 filters, kernel size 3, ReLU
  |
Batch Normalization
  |
MaxPooling1D
  |
Dropout
  |
Conv1D: 96 filters, kernel size 3, ReLU
  |
Batch Normalization
  |
Global Average Pooling
  |
Dense: 192 units, ReLU
  |
Dropout
  |
Compact Feature Layer: 96 units, ReLU
  |
Sigmoid Output
```

The `compact\_feature\_layer` is used to obtain deep representations for the deep-feature SVM.

\---

## Validation-Based Threshold Optimization

The classification threshold is searched from `0.20` to `0.80` in increments of `0.01`.

For each threshold, a composite validation score is calculated as:

```text
Composite Score =
0.20 × Accuracy
+ 0.20 × Precision
+ 0.20 × Recall
+ 0.25 × F1-Score
+ 0.15 × ROC-AUC
```

The threshold with the highest composite validation score is selected and then applied to the test probabilities.

\---

## Probability Fusion

The proposed model combines three probability outputs:

1. SVM probability based on the original standardized metrics
2. SVM probability based on CNN-extracted deep features
3. CNN sigmoid probability

The final probability is calculated as:

```text
Final Probability =
w1 × Original-Feature SVM Probability
+ w2 × Deep-Feature SVM Probability
+ w3 × CNN Probability
```

where:

```text
w1 + w2 + w3 = 1
```

The fusion weights are selected using validation-set performance.

\---

## Reported Experimental Results

The following results were obtained from the current experiment.

### Overall Performance

|Model|Accuracy (%)|Precision|Recall|F1-Score|ROC-AUC|
|-|-:|-:|-:|-:|-:|
|**Proposed Model (CNN+OS model)**|**94.58**|0.9096|**0.9237**|**0.9166**|0.9805|
|SVM|94.50|**0.9104**|0.9198|0.9151|**0.9829**|
|Random Over-Sampling (ROS)|91.79|0.8591|0.8913|0.8749|0.9695|
|Random Forest (RF)|90.62|0.8391|0.8771|0.8577|0.9618|
|Recurrent Neural Networks (RNNs)|86.21|0.7650|0.8254|0.7940|0.9305|
|Convolutional Neural Networks (CNN)|86.17|0.8483|0.6947|0.7639|0.9211|
|Graph Neural Networks (GNNs)|39.75|0.3377|0.9056|0.4919|0.5678|

### Training Time

|Model|Training Time (s)|
|-|-:|
|Proposed Model (CNN+OS model)|1031.90|
|SVM|392.20|
|Random Over-Sampling (ROS)|6.44|
|Random Forest (RF)|18.23|
|Recurrent Neural Networks (RNNs)|73.32|
|Convolutional Neural Networks (CNN)|46.06|
|Graph Neural Networks (GNNs)|91.20|

### Model Complexity

|Model|F1-Score|Number of Parameters|
|-|-:|-:|
|Proposed Model (CNN+OS model)|0.9166|338,109|
|SVM|0.9151|70,435|
|Random Over-Sampling (ROS)|0.8749|70,000|
|Random Forest (RF)|0.8577|1,283,900|
|Recurrent Neural Networks (RNNs)|0.7940|34,785|
|Convolutional Neural Networks (CNN)|0.7639|150,497|
|Graph Neural Networks (GNNs)|0.4919|22,561|

### Scalability and Generalizability

|Model|Scalability (1–5)|Generalizability (1–5)|
|-|-:|-:|
|Proposed Model (CNN+OS model)|2.50|4.99|
|SVM|4.17|4.99|
|Random Over-Sampling (ROS)|4.92|4.76|
|Random Forest (RF)|2.98|4.65|
|Recurrent Neural Networks (RNNs)|4.85|4.24|
|Convolutional Neural Networks (CNN)|4.72|4.12|
|Graph Neural Networks (GNNs)|4.83|1.00|

> Experimental values may vary slightly because of runtime environment, hardware, TensorFlow version, and stochastic model training.

\---

## Repository Contents

```text
.
├── Improved\_Proposed\_CNN\_OS\_Model(1).ipynb
├── README.md
└── outputs/
    ├── synthetic\_change\_proneness\_paper\_like.csv
    ├── synthetic\_dataset\_model\_results\_improved.xlsx
    ├── f1\_score\_vs\_parameters\_improved.png
    └── saved\_models\_improved/
```

The output files are generated automatically when the notebook is executed.

\---

## Requirements

The notebook installs the required packages automatically:

```text
imbalanced-learn
tensorflow
scikit-learn
pandas
matplotlib
openpyxl
catboost
joblib
numpy
```

Recommended environment:

* Python 3.10 or later
* Google Colab
* GPU runtime for faster CNN training
* Google Drive access for saving results

\---

## Running the Project in Google Colab

1. Upload `Software\_Change‐Proneness\_Prediction.ipynb` to Google Drive.
2. Open the file with Google Colaboratory.
3. Select:

```text
Runtime → Change runtime type → T4 GPU
```

4. Run the notebook cell.
5. Authorize Google Drive when prompted.
6. Results will be stored under:

```text
/content/drive/MyDrive/DTU-Jyoti-Patidar/Dataset
```

To use another directory, update:

```python
DATASET\_PATH = "/content/drive/MyDrive/DTU-Jyoti-Patidar/Dataset"
```

\---

## Running Locally

The notebook is designed primarily for Google Colab. To run it locally:

1. Remove or comment out:

```python
from google.colab import drive
drive.mount("/content/drive")
```

2. Replace `DATASET\_PATH` with a local directory:

```python
DATASET\_PATH = "./Dataset"
```

3. Install the dependencies:

```bash
pip install imbalanced-learn tensorflow scikit-learn pandas matplotlib openpyxl catboost joblib
```

4. Execute the notebook using Jupyter Notebook or JupyterLab.

\---

## Generated Output Files

### Dataset

```text
synthetic\_change\_proneness\_paper\_like.csv
```

### Excel Workbook

```text
synthetic\_dataset\_model\_results\_improved.xlsx
```

The workbook contains:

* `All\_Results`
* `Table\_7`
* `Table\_8`
* `Table\_9`
* `Table\_10`
* `Table\_11`
* `Table\_12`
* `Table\_13`

### Saved Model Components

```text
standard\_scaler.pkl
proposed\_cnn\_os\_model.keras
proposed\_deep\_feature\_scaler.pkl
proposed\_original\_feature\_svm.pkl
proposed\_deep\_feature\_svm.pkl
proposed\_fusion\_config.pkl

