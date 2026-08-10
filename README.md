# 🛡️ Cybersecurity Threat Detection Using Machine Learning

## 📌 Project Overview

This project analyses network traffic data to identify malicious Bot activity and explores how data analytics, SQL, rule-based detection and machine learning can be used for cybersecurity threat detection.

The project begins with exploratory analysis of network-flow behaviour, develops a rule-based threat detection approach, and then compares this with a machine-learning model.

A class-weighted Random Forest classifier was ultimately used to detect Bot traffic and achieved **95.65% recall** and approximately **99.47% precision** on the held-out test set.

---

## 🎯 Project Objectives

The main objectives were to:

- Analyse network traffic for patterns associated with Bot activity
- Compare BENIGN and malicious network behaviour
- Identify destination ports associated with suspicious traffic
- Develop a rule-based threat detection baseline
- Build a machine-learning model for Bot detection
- Evaluate the model using appropriate metrics for imbalanced data
- Investigate which network features influence threat detection
- Use SQL to perform security-focused network analysis

---

## 📊 Dataset Overview

After cleaning, the dataset contained:

| Metric | Result |
|---|---:|
| Total Network Flows | 184,145 |
| BENIGN Flows | 182,192 |
| Bot Flows | 1,953 |
| Bot Traffic | 1.06% |
| ML Features | 78 |

The dataset is highly imbalanced, with Bot traffic representing only approximately **1.06%** of all network flows.

Because of this imbalance, model performance was assessed using **precision, recall, F1-score, ROC-AUC and PR-AUC**, rather than relying solely on accuracy.

---

## 🛠️ Technologies Used

- Python
- Pandas
- NumPy
- Matplotlib
- Scikit-learn
- SQL
- SQLite
- Google Colab
- Git / GitHub

---

## 🧹 Data Preparation

The data preparation process included:

- Inspecting dataset structure and data types
- Identifying missing values
- Removing **6,888 duplicate records**
- Handling missing `Flow Bytes/s` values
- Identifying infinite values in network-rate features
- Replacing invalid infinite values and handling resulting missing values
- Verifying the final dataset contained no missing values
- Preparing a binary target variable for machine learning

The final cleaned dataset contained **184,145 network flows**.

---

## 🔎 Exploratory Data Analysis

Exploratory analysis was used to compare BENIGN and Bot network behaviour.

Several differences were identified.

| Network Characteristic | BENIGN | Bot |
|---|---:|---:|
| Average Flow Duration | 12,198,495.87 | 353,278.67 |
| Average Forward Packets | 14.37 | 3.21 |
| Average Backward Packets | 17.14 | 3.36 |
| Average Forward Bytes | 597.24 | 2,663.02 |
| Average Backward Bytes | 29,758.92 | 64.18 |
| Average Packet Length | 107.47 | 51.56 |

Bot flows therefore exhibited substantially different packet, traffic-volume and flow-duration characteristics compared with BENIGN traffic.

---

## 🚨 Destination Port Analysis

Destination port emerged as an important characteristic of Bot traffic.

**1,248 of the 1,953 Bot flows targeted destination port 8080.**

This represents:

> **63.9% of all Bot traffic in the analysed dataset.**

No BENIGN flows in the analysed dataset targeted port 8080.

However, destination port alone should not be considered a universal indicator of malicious activity. This pattern may be specific to the network environment represented by this dataset.

---

## 🛡️ Rule-Based Threat Detection

A rule-based detector was initially developed using patterns identified during exploratory analysis.

### Initial Rule

The first detector achieved:

- **Precision:** 100%
- **Recall:** 26.1%
- **Bot flows detected:** 510
- **False positives:** 0

Although highly precise, the detector missed a large proportion of malicious traffic.

### Improved Rule

A second rule incorporated:

- Destination port 8080
- Low forward packet counts
- Low backward packet counts
- Low average packet length

The improved detector achieved:

| Metric | Result |
|---|---:|
| True Positives | 1,176 |
| False Positives | 0 |
| False Negatives | 777 |
| True Negatives | 182,192 |
| Precision | 100% |
| Recall | 60.22% |

The improved rule substantially increased threat coverage while maintaining zero false positives within this dataset.

---

## 🤖 Machine Learning Threat Detection

A **Random Forest classifier** was trained to determine whether machine learning could improve upon the rule-based approach.

The data was divided using an **80/20 stratified train-test split**, preserving the class distribution between the training and testing datasets.

Because Bot traffic represented only around 1% of the dataset, the Random Forest used:

`class_weight="balanced"`

to account for class imbalance.

---

## 📈 Model Performance

The Random Forest produced the following confusion matrix on the held-out test set:

| | Predicted BENIGN | Predicted Bot |
|---|---:|---:|
| **Actual BENIGN** | 36,436 | 2 |
| **Actual Bot** | 17 | 374 |

### Bot Detection Performance

| Metric | Result |
|---|---:|
| Precision | ~99.47% |
| Recall | ~95.65% |
| F1-score | ~97.5% |
| False Positives | 2 |
| False Negatives | 17 |
| ROC-AUC | 1.0000 |
| PR-AUC | 0.9979 |

The machine-learning model substantially increased Bot detection compared with the rule-based baseline.

### Rule-Based vs Machine Learning

| Approach | Precision | Recall |
|---|---:|---:|
| Rule-Based Detector V2 | 100% | 60.22% |
| Random Forest | ~99.47% | ~95.65% |

The Random Forest increased recall by approximately **35 percentage points** while producing only two false-positive classifications on the test set.

---

## 🧠 Model Interpretation

Feature importance was analysed to understand which network characteristics contributed to Random Forest predictions.

Important features included:

- Destination Port
- Bwd Packet Length Mean
- Init_Win_bytes_forward
- Avg Bwd Segment Size
- Init_Win_bytes_backward
- Average Packet Size
- Total Length of Bwd Packets
- Bwd Packet Length Max
- Subflow Bwd Bytes
- Packet Length Mean

Permutation importance was also used as an additional model-interpretation technique.

The strongest permutation features included:

1. Destination Port
2. Init_Win_bytes_forward
3. Init_Win_bytes_backward
4. URG Flag Count
5. Flow Duration
6. Flow IAT Mean
7. Flow IAT Max
8. Flow IAT Std
9. Bwd Packets/s
10. Bwd Packet Length Mean

Destination Port was particularly influential, suggesting the model relies heavily on port-related patterns within this dataset.

---

## 🗄️ SQL Security Analysis

SQLite was used to perform additional cybersecurity analysis on the cleaned network traffic.

The SQL section demonstrates:

- `SELECT`
- `WHERE`
- `GROUP BY`
- `COUNT()`
- `AVG()`
- `ROUND()`
- `CASE`
- Subqueries
- Common Table Expressions (CTEs)
- Window functions
- `RANK()`

SQL was used to investigate Bot activity by destination port, compare BENIGN and Bot network characteristics, identify suspicious flows and rank destination ports by malicious activity.

The SQL implementation of the improved detection rule identified **1,176 suspicious Bot flows with no BENIGN flows flagged** within the analysed dataset.

---

## ⚠️ Limitations

Although the Random Forest achieved very strong test performance, these results should be interpreted carefully.

Destination Port was particularly influential, and **63.9% of Bot traffic targeted port 8080**. The model may therefore be learning characteristics that are specific to this dataset and network environment.

Additionally, the train-test split was created randomly from the same underlying dataset. Network flows within the dataset may share characteristics that make the held-out test set easier to classify than traffic collected from an entirely different network.

Therefore, the reported performance should **not** be interpreted as evidence that the model would achieve the same results on real-world unseen network traffic.

Future work could include:

- Testing against an independent network dataset
- Time-based validation
- Detection of additional attack categories
- Cross-environment testing
- Model threshold optimisation
- Further explainability analysis

---

## 💡 Key Takeaways

This project demonstrates how traditional data analytics and machine learning can complement each other in cybersecurity.

Exploratory analysis identified distinctive Bot traffic characteristics, while SQL provided an additional method of investigating suspicious network behaviour.

The rule-based detector provided a highly precise baseline, but machine learning substantially improved threat coverage.

Most importantly, model interpretation showed that the classifier relied heavily on destination-port and TCP/network-flow characteristics, highlighting the importance of evaluating both **model performance and model behaviour**.

---

## 📁 Repository

The complete analysis, SQL queries, visualisations, model development and evaluation can be found in:

`Cybersecurity_Threat_Detection_Analysis.ipynb`

---

## 👩‍💻 Author

**Andrea Mejia**

Data Analytics & Cybersecurity Portfolio Project
