# 💳 Advanced Financial Fraud Detection Model — Logistic Regression, Decision Tree, Random Forest, XGBoost & Real-Time Kafka Streaming

![Python](https://img.shields.io/badge/Python-3.8+-blue?style=flat-square&logo=python)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?style=flat-square&logo=jupyter)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-ML-green?style=flat-square&logo=scikit-learn)
![XGBoost](https://img.shields.io/badge/XGBoost-Gradient%20Boosting-red?style=flat-square)
![Apache Kafka](https://img.shields.io/badge/Apache-Kafka-231F20?style=flat-square&logo=apachekafka)
![PySpark](https://img.shields.io/badge/Apache-PySpark-E25A1C?style=flat-square&logo=apachespark)
![Tableau](https://img.shields.io/badge/Tableau-Dashboard-E97627?style=flat-square&logo=tableau)

> An **end-to-end financial fraud detection pipeline** built on the 2023 European credit card transaction dataset — training and benchmarking four classification models (**Logistic Regression**, **Decision Tree**, **Random Forest**, and **XGBoost**) with **RandomizedSearchCV** hyperparameter tuning, and extending into a **real-time fraud detection system** using **Apache Kafka streaming**, **PySpark Structured Streaming**, and **automated email alerting**. A companion **Tableau workbook** (3 sheets) provides exploratory visual analysis of transaction amounts, class distribution, and feature correlations.

---

## 📌 Problem Statement

Credit card fraud causes billions of dollars in losses annually. The core challenge lies in detecting rare fraudulent transactions hidden within overwhelmingly legitimate ones — a classic extreme class imbalance problem. This project builds a production-aware fraud detection system that:

- **Classifies transactions as fraudulent or legitimate** using four ML classifiers trained on 28 PCA-transformed features and transaction amount
- **Benchmarks model performance** across accuracy, precision, recall, F1-score, and confusion matrix on both train and test sets
- **Tunes the best-performing model (XGBoost)** with RandomizedSearchCV over a structured hyperparameter grid using 5-fold cross-validation
- **Streams live transactions** through an Apache Kafka pipeline, processes them in real time with PySpark Structured Streaming, and triggers automated email alerts for every flagged fraudulent transaction

---

## 🎯 Objectives

- Load and clean `creditcard_2023.csv` — inspect shape, dtypes, null values, and descriptive statistics
- Visualise feature correlations via a full heatmap, transaction amount via box plot and KDE, and class balance via a pie chart
- Drop the `id` column and separate features (`X`) from the target (`Class`), then apply **80/20 train/test splitting** with `random_state=42`
- Apply **StandardScaler** normalisation to training and test feature sets
- Train and evaluate **Logistic Regression** (`max_iter=10000`) on train and test sets
- Train and evaluate **Decision Tree Classifier** (`random_state=42`) on train and test sets
- Train and evaluate **Random Forest Classifier** (default parameters) on train and test sets
- Train and evaluate **XGBoost RF Classifier** (default parameters) on train and test sets
- Tune XGBoost with **RandomizedSearchCV** — `n_estimators`, `learning_rate`, `max_depth` across 20 iterations, 5-fold CV, `n_jobs=-1`
- Build a **Kafka Producer** to stream transaction records as JSON to a Kafka topic
- Set up a **PySpark Structured Streaming** consumer to read from Kafka, parse JSON payloads, and filter transactions with `amount > 10000`
- Send **automated email alerts** via SMTP/Gmail for every fraudulent transaction detected in the stream

---

## 📂 Dataset

| Property | Detail |
|---|---|
| File | `creditcard_2023.csv` |
| Source | European credit card transactions — 2023 edition |
| Target Variable | `Class` — 1: fraudulent, 0: legitimate |
| Features | 28 PCA-transformed components (V1–V28) + transaction `Amount` |
| Class Imbalance | Highly imbalanced — fraudulent transactions are a small minority |

### Dataset Columns

| Column | Type | Description |
|---|---|---|
| `id` | Integer | Unique transaction identifier — **dropped** before modelling |
| `V1` – `V28` | Float | PCA-transformed anonymised transaction features (28 components) |
| `Amount` | Float | Transaction amount in currency units |
| `Class` | Integer | **Target** — 0: legitimate transaction, 1: fraudulent transaction |

---

## 🔬 Methodology

```
creditcard_2023.csv
       │
       ▼
Data Cleaning & Inspection
       │   ├── Re-index from 1
       │   ├── df.info(), df.isnull().sum(), df.describe()
       │   └── Confirm no null values across all columns
       │
       ▼
Exploratory Data Analysis (EDA)
       │   ├── Correlation Heatmap — all features, cmap='crest', annotated (20×10)
       │   ├── Amount Box Plot — outlier distribution
       │   ├── Amount KDE Plot — transaction amount distribution shape
       │   └── Class Distribution Pie Chart — % legitimate vs. fraudulent
       │
       ▼
Feature / Target Separation & Train-Test Split
       │   ├── X = drop(['id', 'Class']) → 29 features (V1–V28 + Amount)
       │   ├── y = Class
       │   └── train_test_split(test_size=0.2, random_state=42)
       │
       ▼
StandardScaler Normalisation
       │   ├── fit_transform on X_train → x_train_scaled
       │   └── transform  on X_test  → x_test_scaled
       │
       ├─────────────────────────────────────────────────────────┐
       ▼                                                         ▼
Model 1 — Logistic Regression (max_iter=10000)    Model 2 — Decision Tree (random_state=42)
  Eval: accuracy + confusion matrix +               Eval: accuracy + confusion matrix +
        classification report (train + test)               classification report (train + test)

       ▼                                                         ▼
Model 3 — Random Forest (default params)          Model 4 — XGBoost RF Classifier (default params)
  Eval: accuracy + confusion matrix +               Eval: accuracy + confusion matrix +
        classification report (train + test)               classification report (train + test)
       │
       ▼
Hyperparameter Tuning — XGBoost (RandomizedSearchCV)
       │   ├── Search space:
       │   │       n_estimators : [50, 100, 150, 200, 300, 400]
       │   │       learning_rate: [0.01, 0.1, 0.2, 0.3]
       │   │       max_depth    : [3, 4, 5, 6]
       │   ├── n_iter=20, cv=5, scoring='accuracy', n_jobs=-1, random_state=42
       │   └── Print: best_params_ + best_score_
       │
       ▼
Real-Time Kafka + PySpark Streaming Pipeline
       │
       ├── Kafka Admin → Create topic "test-topic" (1 partition)
       │
       ├── Kafka Producer (kafka-python)
       │       └── JSON-serialised transactions → broker: 192.168.29.111:9092
       │
       ├── PySpark Structured Streaming Consumer
       │       ├── SparkSession: appName="Fraud_Detection"
       │       ├── Read stream from Kafka topic "data"
       │       ├── Parse JSON schema: {id: DoubleType, amount: DoubleType}
       │       └── Filter: col("amount") > 10000 → fraudulent stream
       │
       └── Email Alert System (smtplib / Gmail)
               ├── send_email_alert(data) per flagged transaction
               ├── MIMEText: sender=alert@bank.com → receiver=security_team@bank.com
               ├── SMTP Gmail port 587 + STARTTLS + app password auth
               └── foreachBatch → process_batch → awaitTermination()
```

---

## 📊 Model Evaluation Framework

All four models share a unified `model_eval()` function applied to both train and test predictions:

| Metric | Method |
|---|---|
| Accuracy | `accuracy_score(actual, predicted)` |
| Confusion Matrix | `confusion_matrix(actual, predicted)` |
| Classification Report | `classification_report(actual, predicted)` — precision, recall, F1-score per class |

> 📝 *Refer to `Fraud_Detection_Model.ipynb` for full numeric evaluation outputs — accuracy scores, confusion matrices, and classification reports for all four models on both train and test sets, plus XGBoost best hyperparameters and best CV score from RandomizedSearchCV.*

---

## 🏗️ Real-Time Streaming Architecture

```
Transaction Records
       │
       ▼
Kafka Producer (Python / kafka-python)
  JSON-serialised → topic: "test-topic" / "data"
       │
       ▼
Apache Kafka Broker  (192.168.29.111:9092)
       │
       ▼
PySpark Structured Streaming Consumer
  Schema parse: {id: Double, amount: Double}
  Filter: amount > 10000
       │
       ▼
Fraudulent Transaction Batch (foreachBatch)
       │
       ▼
SMTP Email Alert (Gmail, port 587, STARTTLS)
  To: security_team@bank.com
  Subject: "Fraudulent Transaction Detected"
  Body: Transaction ID + Amount
```

---

## 📈 Visualisations

### Notebook Plots

| Plot | Description |
|---|---|
| Correlation Heatmap | Full 30-column feature correlation matrix — `cmap='crest'`, annotated, 20×10 figure |
| Amount Box Plot | Outlier and spread distribution of the transaction `Amount` column |
| Amount KDE Plot | Kernel density estimate of transaction amount — distribution shape and skew |
| Class Distribution Pie Chart | Percentage split of legitimate (Class=0) vs. fraudulent (Class=1) transactions |

### Tableau Workbook

The companion workbook (`Fraud_Detection.twb`) connects directly to `creditcard_2023.csv` and contains **3 analytical sheets** for interactive EDA — exploring PCA feature distributions, transaction amount patterns, and class imbalance across the full dataset.

---

## 💡 Key Insights

- **28 PCA-transformed features (V1–V28)** anonymise raw transaction data for cardholder privacy — this makes feature engineering minimal and places the modelling burden on capturing latent structure within the PCA space alongside raw `Amount`
- **StandardScaler normalisation** is applied before Logistic Regression — features on different scales would dominate gradient-based optimisation without it; tree-based models (Decision Tree, Random Forest, XGBoost) are trained on unscaled `x_train` directly, as they are invariant to monotonic feature transformations
- **RandomizedSearchCV with 5-fold CV** over 20 iterations explores a 96-point hyperparameter space for XGBoost efficiently — `n_jobs=-1` parallelises the search across all available CPU cores for faster tuning
- **Accuracy alone is misleading** for imbalanced fraud data — the per-class precision, recall, and F1-score for the fraud class (Class=1) in the `classification_report` is the true measure of detection effectiveness; a model that predicts "no fraud" for everything achieves high accuracy but zero utility
- **Kafka + PySpark Structured Streaming** extends the offline model into a real-time detection layer — simulating a production pipeline where transactions flow continuously and alerts fire within the same processing micro-batch cycle
- **`foreachBatch` + SMTP alerting** bridges streaming output to human-readable incident notifications — a lightweight but realistic pattern for real-time fraud response in financial systems

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python 3.8+ | Core programming language |
| Pandas | Data loading, cleaning, re-indexing, feature separation |
| NumPy | Array operations and metric computation |
| Matplotlib / Seaborn | Heatmap, box plot, KDE plot, class distribution pie chart |
| Scikit-learn | Logistic Regression, Decision Tree, Random Forest, StandardScaler, train_test_split, RandomizedSearchCV, metrics |
| XGBoost | XGBRFClassifier — gradient-boosted random forest classifier with RandomizedSearchCV tuning |
| Apache Kafka (`kafka-python`) | KafkaAdminClient, NewTopic, KafkaProducer — topic creation and real-time transaction streaming |
| PySpark | Structured Streaming — Kafka source, JSON schema parsing, stream filtering via `foreachBatch` |
| smtplib / email | Automated Gmail SMTP alerting for flagged fraudulent transactions |
| Tableau Desktop 2025.2 | 3-sheet interactive EDA workbook on `creditcard_2023.csv` |
| Jupyter Notebook | Interactive development, training, evaluation, and streaming orchestration |

---

## 🚀 Getting Started

### Prerequisites

```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost kafka-python pyspark jupyter
```

> ⚠️ **Kafka setup:** The real-time streaming cells (Cells 34–41) require a running **Apache Kafka broker**. The notebook is configured for `192.168.29.111:9092` — update `bootstrap_servers` to match your local or cloud Kafka instance before running.

> ⚠️ **PySpark setup:** Ensure Java 8 or 11 is installed and `JAVA_HOME` is set correctly before running PySpark cells.

> ⚠️ **Email alerting:** Replace `user_email@gmail.com` and `user_app_password` in the SMTP cell with a valid Gmail account and [Google App Password](https://support.google.com/accounts/answer/185833) before running the alert pipeline.

### Run the Notebook

```bash
# Clone the repository
git clone https://github.com/PJ2001-IND/Fraud-Detection-Model.git

# Navigate to the project directory
cd Fraud-Detection-Model

# Launch Jupyter Notebook
jupyter notebook Fraud_Detection_Model.ipynb
```

> ⚠️ `creditcard_2023.csv` must be in the same directory as the notebook. Download it from [Kaggle — Credit Card Fraud Detection Dataset 2023](https://www.kaggle.com/datasets/nelgiriyewithana/credit-card-fraud-detection-dataset-2023).

### Open the Tableau Workbook

Open `Fraud_Detection.twb` in **Tableau Desktop 2025.2** or later. When prompted, update the data source path to point to `creditcard_2023.csv` in your local project directory.

---

## 📁 Project Structure

```
📦 Fraud-Detection-Model
 ┣ 📓 Fraud_Detection_Model.ipynb   # Full pipeline — EDA, 4 classifiers, tuning, Kafka streaming (42 cells)
 ┣ 📄 creditcard_2023.csv           # European credit card dataset [download from Kaggle]
 ┣ 📊 Fraud_Detection.twb           # Tableau workbook — 3-sheet EDA on creditcard_2023.csv
 ┣ 📄 requirements.txt              # Python dependencies
 ┗ 📄 README.md                     # Project documentation
```

---

## ⚠️ Disclaimer

> This project is built purely for **educational and analytical purposes** as a machine learning and data engineering case study. The fraud detection models and real-time streaming pipeline are derived from a publicly available anonymised dataset and are intended to demonstrate classification, hyperparameter tuning, Kafka streaming, and PySpark Structured Streaming proficiency — not to serve as production financial fraud detection tooling. The SMTP credentials, Kafka broker address, and sender/receiver email addresses in the notebook are placeholders and must be replaced with real values before running the streaming cells.

---

## 🔭 Future Scope

- Address **class imbalance explicitly** using SMOTE, ADASYN, or `class_weight='balanced'` — the current pipeline trains on raw imbalanced data, which may bias classifiers toward the majority (legitimate) class
- Add **Precision-Recall AUC and ROC-AUC** as primary evaluation metrics alongside accuracy — both are far more informative than accuracy for highly imbalanced fraud data
- Extend **RandomizedSearchCV tuning** to Logistic Regression, Decision Tree, and Random Forest for a fully tuned model comparison
- Replace the static `amount > 10000` Spark filter with **real-time ML inference** — load the trained XGBoost model inside `foreachBatch` to score each transaction by its full feature vector
- Integrate a **feature store** (e.g., Feast) to serve pre-computed PCA feature vectors to the streaming inference pipeline in real time
- Deploy the trained model as a **FastAPI REST endpoint** for synchronous fraud scoring alongside the Kafka pipeline for asynchronous batch alerting
- Build a **Streamlit or Grafana dashboard** to monitor live transaction throughput, fraud detection rate, and alert count in real time
- Experiment with **Autoencoder-based anomaly detection** — reconstruct legitimate transaction patterns unsupervisedly and flag high reconstruction-error transactions as potential fraud

---

## 👤 Author

**Praasuk Jain**
- GitHub: [@PJ2001-IND](https://github.com/PJ2001-IND)
- LinkedIn: [praasuk-jain](https://www.linkedin.com/in/praasuk-jain-425b6b1a3/)

---

> ⭐ If you found this project useful, consider giving it a star!
