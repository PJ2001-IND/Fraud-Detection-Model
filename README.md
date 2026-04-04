# 🔍 Fraud Detection Model

![Python](https://img.shields.io/badge/Python-3.8+-blue?style=flat-square&logo=python)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?style=flat-square&logo=jupyter)
![Tableau](https://img.shields.io/badge/Tableau-Dashboard-lightblue?style=flat-square&logo=tableau)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-ML-green?style=flat-square&logo=scikit-learn)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)

> An end-to-end fraud detection pipeline combining machine learning classification models with an interactive Tableau dashboard for visual analysis and business insights.

---

## 📌 Problem Statement

Financial fraud costs businesses and consumers billions of dollars every year. Detecting fraudulent transactions in real time is a critical challenge due to highly imbalanced data — fraudulent transactions make up only a tiny fraction of all activity. This project builds a robust ML-based fraud detection system that addresses class imbalance, maximises detection recall, and visualises patterns through an interactive Tableau dashboard.

---

## 🎯 Objective

- Build a machine learning model to classify transactions as **fraudulent** or **legitimate**
- Handle the class imbalance problem inherent in fraud datasets
- Evaluate models using fraud-appropriate metrics (Precision, Recall, F1, AUC-ROC)
- Visualise transaction patterns and model insights through a **Tableau workbook**

---

## 📂 Dataset

| Property | Detail |
|---|---|
| File | Included in notebook |
| Task | Binary Classification (Fraud / Not Fraud) |
| Challenge | Heavily imbalanced classes (fraud is rare) |
| Key Features | Transaction amount, type, origin/destination balances, timestamps |

---

## 🔬 Methodology

```
Raw Transaction Data
        │
        ▼
Exploratory Data Analysis (EDA)
        │   ├── Class imbalance analysis
        │   ├── Transaction amount distributions
        │   └── Correlation heatmap
        │
        ▼
Data Preprocessing
        │   ├── Handling class imbalance (SMOTE / undersampling)
        │   ├── Feature engineering
        │   ├── Label encoding & scaling
        │   └── Train-test split
        │
        ▼
Model Training & Comparison
        │   ├── Logistic Regression (baseline)
        │   ├── Decision Tree
        │   ├── Random Forest
        │   └── Gradient Boosting / XGBoost
        │
        ▼
Evaluation
        │   ├── Confusion Matrix
        │   ├── Precision / Recall / F1-Score
        │   ├── AUC-ROC Curve
        │   └── Feature Importance
        │
        ▼
Tableau Dashboard
            ├── Fraud vs Legitimate transaction breakdown
            ├── Amount distribution by fraud type
            └── Geographic / temporal trend analysis
```

---

## 📊 Results

| Model | Accuracy | Precision | Recall | F1-Score |
|---|---|---|---|---|
| Logistic Regression | — | — | — | — |
| Decision Tree | — | — | — | — |
| Random Forest | — | — | — | — |
| Best Model | — | — | — | — |

> 📝 *Refer to `Fraud_Detection_Model.ipynb` for full results, ROC curves, and confusion matrices.*

> ⚠️ **Note on Metrics**: For fraud detection, **Recall** is the most critical metric — missing a fraudulent transaction (false negative) is far more costly than a false alarm.

---

## 📊 Tableau Dashboard

The repository includes `Fraud Detection.twb` — an interactive Tableau workbook that provides:

- **Transaction Overview**: Volume of fraud vs legitimate transactions
- **Amount Analysis**: Distribution of transaction amounts flagged as fraudulent
- **Pattern Recognition**: Visual trends to support business decision-making
- **Filter Controls**: Drill down by transaction type, amount range, and more

> Open with **Tableau Desktop** or **Tableau Public** to explore the dashboard.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python | Core programming language |
| Pandas | Data manipulation and analysis |
| NumPy | Numerical operations |
| Matplotlib / Seaborn | Statistical visualisation |
| Scikit-learn | ML model building and evaluation |
| Tableau | Interactive business dashboard |
| Jupyter Notebook | Interactive development environment |

---

## 🚀 Getting Started

### Prerequisites

```bash
pip install pandas numpy matplotlib seaborn scikit-learn imbalanced-learn jupyter
```

### Run the Notebook

```bash
# Clone the repository
git clone https://github.com/PJ2001-IND/Fraud-Detection-Model.git

# Navigate to the project directory
cd Fraud-Detection-Model

# Launch Jupyter Notebook
jupyter notebook Fraud_Detection_Model.ipynb
```

### View the Dashboard

Open `Fraud Detection.twb` in **Tableau Desktop** or upload it to **Tableau Public**.

---

## 📁 Project Structure

```
📦 Fraud-Detection-Model
 ┣ 📓 Fraud_Detection_Model.ipynb   # ML pipeline and model analysis
 ┣ 📊 Fraud Detection.twb            # Tableau interactive dashboard
 ┗ 📄 README.md                      # Project documentation
```

---

## 💡 Key Insights

- Fraud transactions tend to cluster around specific transaction types and high-value amounts
- Class imbalance handling (e.g. SMOTE or class weighting) significantly improves recall
- Ensemble models (Random Forest, Gradient Boosting) outperform linear classifiers on this type of data
- The Tableau dashboard reveals temporal and amount-based patterns invisible in raw tabular data

---

## 🔭 Future Scope

- Deploy the model as a real-time REST API using FastAPI or Flask
- Integrate with a live transaction stream (Kafka / Spark Streaming)
- Add SHAP-based explainability to flag which features triggered fraud alerts
- Upgrade Tableau dashboard to a live-connected Tableau Server publication

---

## 👤 Author

**Praasuk Jain**
- GitHub: [@PJ2001-IND](https://github.com/PJ2001-IND)
- LinkedIn: [praasuk-jain](https://www.linkedin.com/in/praasuk-jain-425b6b1a3/)

---

## 📄 License

This project is licensed under the MIT License.

---

> ⭐ If you found this project useful, consider giving it a star!
