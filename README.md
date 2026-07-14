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


## Dataset

**12,000 samples** and **20 software metrics**.

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

1. Upload Software_Change‐Proneness_Prediction.ipynb to Google Drive.
2. Open the file with Google Colaboratory.
3. Select:

```text
Runtime → Change runtime type → T4 GPU
```

4. Run the notebook cell.
5. Authorize Google Drive when prompted.
6. Results will be stored under drive/repository:

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

