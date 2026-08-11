# 🛡️ Cybersecurity Threat Detection Using Machine Learning

## 📌 Project Overview

This project analyses network traffic data to identify malicious **Bot activity** and explores how data analytics, SQL, rule-based detection and machine learning can be applied to cybersecurity threat detection.

The project follows an end-to-end analytical workflow, beginning with data cleaning and exploratory analysis before developing a rule-based threat detector and comparing its performance with a machine-learning model.

A class-weighted **Random Forest classifier** was ultimately used to detect Bot traffic, achieving approximately **99.47% precision** and **95.65% recall** on the held-out test set.

---

## 🎯 Project Objectives

The main objectives of this project were to:

- Analyse network traffic for patterns associated with Bot activity
- Compare BENIGN and malicious network behaviour
- Identify destination ports associated with suspicious traffic
- Develop a rule-based threat detection baseline
- Build a machine-learning model for Bot detection
- Account for severe class imbalance in the dataset
- Evaluate the model using appropriate cybersecurity classification metrics
- Investigate which network features influence threat detection
- Use SQL to perform security-focused network traffic analysis

---

## 💼 Skills Demonstrated

- Python Data Analysis
- Data Cleaning & Preprocessing
- Exploratory Data Analysis (EDA)
- Cybersecurity Network Traffic Analysis
- SQL & SQLite
- Rule-Based Threat Detection
- Machine Learning Classification
- Random Forest
- Imbalanced Classification
- Train/Test Splitting
- Precision, Recall & F1 Evaluation
- ROC-AUC Analysis
- Precision-Recall Analysis
- Confusion Matrix Analysis
- Feature Importance
- Permutation Importance
- Model Interpretation
- Git & GitHub

---

## 📊 Dataset Overview

### Dataset Source

This project uses data from the **CICIDS2017 (Canadian Institute for Cybersecurity Intrusion Detection System 2017)** dataset.

The analysis focuses on:

`Friday-WorkingHours-Morning.pcap_ISCX.csv`

which contains BENIGN and Bot network traffic.

**Dataset:** [CIC-IDS2017 Dataset – Kaggle](https://www.kaggle.com/datasets/dhoogla/cicids2017)

The original file contained **191,033 network flows**, including:

- **189,067 BENIGN flows**
- **1,966 Bot flows**

After cleaning and duplicate removal, the final dataset used for analysis contained **184,145 network flows**.

### Final Dataset

| Metric | Result |
|---|---:|
| Total Network Flows | 184,145 |
| BENIGN Flows | 182,192 |
| Bot Flows | 1,953 |
| Bot Traffic | 1.06% |
| Machine Learning Features | 78 |

The final dataset was highly imbalanced, with Bot traffic representing only approximately **1.06% of all network flows**.

Because of this imbalance, model performance was assessed primarily using **precision, recall, F1-score and Precision-Recall AUC**, rather than relying solely on overall accuracy.

---

## 🛠️ Technologies Used

- **Python**
- **Pandas**
- **NumPy**
- **Matplotlib**
- **Scikit-learn**
- **SQL**
- **SQLite**
- **Google Colab**
- **Git / GitHub**

---

## 🧹 Data Cleaning & Preparation

The data preparation process included:

- Inspecting dataset structure and data types
- Identifying missing values
- Identifying and removing **6,888 duplicate records**
- Investigating missing `Flow Bytes/s` values
- Detecting infinite values in `Flow Bytes/s` and `Flow Packets/s`
- Converting invalid infinite values and handling resulting missing values
- Verifying that no missing or infinite values remained
- Preparing the dataset for SQL analysis and machine learning
- Creating a binary machine-learning target:
  - `BENIGN = 0`
  - `Bot = 1`

Following cleaning, the dataset contained **184,145 network flows**.

---

## 🔎 Exploratory Data Analysis

Exploratory analysis was performed to identify behavioural differences between BENIGN and Bot network traffic.

## 🔎 Exploratory Data Analysis

Exploratory analysis was performed to identify behavioural differences between BENIGN and Bot network traffic.

## 🔎 Exploratory Data Analysis

Exploratory analysis was performed to identify behavioural differences between BENIGN and Bot network traffic.

### BENIGN vs Bot Traffic Distribution

![BENIGN vs Bot Traffic Distribution](BENIGN%20vs%20Bot%20distribution.png)

The class distribution demonstrates the significant imbalance within the dataset, with Bot traffic representing only **1.06% of network flows**. This imbalance influenced the choice of evaluation metrics and the use of class weighting during machine-learning development.

Several substantial differences were identified:

| Network Characteristic | BENIGN | Bot |
|---|---:|---:|
| Average Flow Duration | 12,198,495.87 | 353,278.67 |
| Average Forward Packets | 14.37 | 3.21 |
| Average Backward Packets | 17.14 | 3.36 |
| Average Forward Bytes | 597.24 | 2,663.02 |
| Average Backward Bytes | 29,758.92 | 64.18 |
| Average Packet Length | 107.47 | 51.56 |

Bot flows generally contained:

- Shorter flow durations
- Fewer forward and backward packets
- Substantially lower backward traffic volume
- Smaller average packet sizes
- Distinct destination-port behaviour

These patterns were subsequently used to investigate potential rule-based threat detection.
---

## 🚨 Destination Port Analysis

Destination port emerged as an important characteristic of Bot traffic.

Of the **1,953 Bot flows** in the cleaned dataset:

**1,248 targeted destination port 8080.**

This represents:

> **63.9% of all Bot traffic in the analysed dataset.**

No BENIGN flows in this dataset targeted destination port 8080.

However, this does **not** mean that traffic using port 8080 is inherently malicious. The relationship observed here may be specific to the attack scenario and network environment represented within this dataset.

This distinction became particularly important when interpreting the machine-learning model.

---

# 🛡️ Rule-Based Threat Detection

Before implementing machine learning, a rule-based detector was developed to establish an interpretable baseline.

## Rule V1

The initial rule produced:

| Metric | Result |
|---|---:|
| True Positives | 510 |
| False Positives | 0 |
| False Negatives | 1,443 |
| True Negatives | 182,192 |
| Precision | 100% |
| Recall | 26.1% |

The rule was extremely precise but detected only approximately one quarter of Bot traffic.

This demonstrated an important cybersecurity trade-off:

> A detection rule can generate very few false alerts while still missing a large proportion of malicious activity.

---

## Rule V2

Further analysis of the missed Bot traffic identified additional characteristics that could improve detection.

The second rule considered:

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

The second rule increased recall from **26.1% to 60.22%** while maintaining zero false positives within this dataset.

Rule V2 was therefore retained as the **rule-based baseline** for comparison with machine learning.

---

# 🤖 Machine Learning Threat Detection

A **Random Forest classifier** was trained to determine whether machine learning could improve threat detection beyond the manually defined rules.

The data was divided using an:

**80% training / 20% testing stratified split**

to preserve the proportion of BENIGN and Bot traffic in both datasets.

### Training Set

- 147,316 network flows
- 145,754 BENIGN
- 1,562 Bot

### Test Set

- 36,829 network flows
- 36,438 BENIGN
- 391 Bot

Because Bot traffic represented only around 1% of the dataset, the Random Forest was configured using:

```python
class_weight="balanced"
```

This helped account for the severe class imbalance during model training.

---

## 📈 Machine Learning Results

The Random Forest produced the following confusion matrix on the held-out test set:

| | Predicted BENIGN | Predicted Bot |
|---|---:|---:|
| **Actual BENIGN** | 36,436 | 2 |
| **Actual Bot** | 17 | 374 |

### Random Forest Confusion Matrix

![Random Forest Confusion Matrix](Confusion%20matrix.png)

The Random Forest correctly identified **374 of 391 Bot flows** in the test set, while only **2 BENIGN flows were incorrectly classified as Bot**.

### Bot Detection Performance

| Metric | Result |
|---|---:|
| True Positives | 374 |
| True Negatives | 36,436 |
| False Positives | 2 |
| False Negatives | 17 |
| Precision | ~99.47% |
| Recall | ~95.65% |
| F1-score | ~97.5% |
| ROC-AUC | 1.0000 |
| PR-AUC | 0.9979 |

The model correctly detected **374 of the 391 Bot flows** in the held-out test set while incorrectly flagging only **2 BENIGN flows**.

---

## 📊 Rule-Based vs Machine Learning Detection

| Approach | Precision | Recall |
|---|---:|---:|
| Rule V1 | 100% | 26.1% |
| Rule V2 | 100% | 60.22% |
| Random Forest | ~99.47% | ~95.65% |

The Random Forest increased Bot recall by approximately **35 percentage points compared with Rule V2**, while maintaining very high precision.

This demonstrates the benefit of using machine learning to identify more complex combinations of network-flow characteristics that cannot easily be represented by a small number of manual rules.

---

# 🧠 Model Interpretation

Achieving strong predictive performance was not considered sufficient on its own. Feature importance analysis was therefore performed to investigate **which network characteristics the Random Forest relied upon**.

## Random Forest Feature Importance

![Random Forest Feature Importance](Feature%20importance.png)

The most important features included:

1. Destination Port
2. Bwd Packet Length Mean
3. Init_Win_bytes_forward
4. Avg Bwd Segment Size
5. Init_Win_bytes_backward
6. Average Packet Size
7. Total Length of Bwd Packets
8. Bwd Packet Length Max
9. Subflow Bwd Bytes
10. Packet Length Mean

Several important features related to **backward network traffic**, supporting patterns identified during exploratory analysis.

---

## Permutation Importance

Permutation importance was also calculated to assess how strongly the model's predictive performance depended on individual features.

The leading features were:

| Rank | Feature | Permutation Importance |
|---|---|---:|
| 1 | Destination Port | 0.713735 |
| 2 | Init_Win_bytes_forward | 0.202637 |
| 3 | Init_Win_bytes_backward | 0.175822 |
| 4 | URG Flag Count | 0.064274 |
| 5 | Flow Duration | 0.022596 |
| 6 | Flow IAT Mean | 0.015590 |
| 7 | Flow IAT Max | 0.010313 |
| 8 | Flow IAT Std | 0.008988 |
| 9 | Bwd Packets/s | 0.007334 |
| 10 | Bwd Packet Length Mean | 0.005708 |

`Destination Port` was particularly influential.

Shuffling this feature caused a substantial deterioration in model performance, suggesting that the classifier relies heavily on destination-port patterns within this dataset.

This is an important consideration when evaluating whether the model would generalise to other network environments.

---

# 🗄️ SQL Security Analysis

The cleaned network traffic was loaded into **SQLite** to perform additional cybersecurity analysis using SQL.

The SQL section investigated:

- Overall network traffic volume
- BENIGN vs Bot traffic distribution
- Bot activity by destination port
- Average network behaviour by traffic class
- Percentage of Bot traffic targeting port 8080
- Rule-based suspicious-flow detection
- Ranking destination ports by malicious activity

SQL techniques demonstrated include:

- `SELECT`
- `WHERE`
- `COUNT()`
- `AVG()`
- `ROUND()`
- `GROUP BY`
- `ORDER BY`
- `LIMIT`
- `CASE`
- Subqueries
- Common Table Expressions (CTEs)
- Window functions
- `RANK()`

The SQL implementation of Rule V2 identified:

> **1,176 suspicious Bot flows and zero BENIGN flows**

which reproduced the results obtained using Python.

SQL analysis also ranked **destination port 8080 as the leading port by Bot-flow volume**, containing 1,248 Bot flows.

---

# ⚠️ Model Limitations

Although the Random Forest achieved extremely strong test performance, the results should be interpreted carefully.

### 1. Dataset-Specific Port Behaviour

Destination Port was the strongest predictor, and **63.9% of Bot traffic targeted port 8080**.

The model may therefore rely heavily on a pattern associated with this particular attack scenario rather than learning a universally applicable representation of Bot traffic.

### 2. Random Train/Test Split

Training and testing samples were randomly selected from the same underlying dataset and network environment.

Flows may therefore share characteristics across the training and testing sets.

Performance on traffic collected from an entirely different network could be lower.

### 3. Limited Attack Scope

This analysis focuses specifically on distinguishing **BENIGN traffic from Bot activity**.

A production intrusion-detection system would need to recognise multiple attack categories and previously unseen threats.

### 4. Very High Evaluation Scores

The **1.0000 ROC-AUC** and **0.9979 PR-AUC** indicate excellent separation within the test data, but these values should not be interpreted as evidence of a universally perfect threat detector.

Independent validation would be required before making claims about real-world performance.

---

# 🚀 Future Improvements

Future development could include:

- Testing the model on an independent network dataset
- Using time-based rather than random validation
- Evaluating additional cyberattack categories
- Cross-environment model testing
- Investigating model performance without Destination Port
- Threshold optimisation based on operational security requirements
- Additional explainability techniques
- Evaluating performance under changing network conditions

---

# 💡 Key Takeaways

This project demonstrates how **data analytics, SQL, cybersecurity knowledge and machine learning** can be combined to investigate malicious network behaviour.

Key findings include:

- Bot traffic represented only **1.06%** of the cleaned dataset.
- **63.9% of Bot flows targeted port 8080**.
- Rule-based detection achieved **60.22% recall with 100% precision**.
- Random Forest increased Bot recall to approximately **95.65%**.
- The model maintained approximately **99.47% precision**.
- Only **2 BENIGN flows** were incorrectly classified as Bot in the test set.
- Permutation importance revealed a strong dependency on Destination Port.
- SQL independently reproduced the rule-based threat detection results.
- Model limitations were considered alongside predictive performance.

The project highlights an important principle in cybersecurity analytics:

> **Strong model performance should be evaluated alongside false positives, false negatives, class imbalance, explainability and generalisability.**

---

# 📁 Repository Contents

### `Cybersecurity_Threat_Detection_Analysis.ipynb`

Complete project notebook containing:

- Data preparation
- Exploratory data analysis
- Network traffic investigation
- Rule-based detection
- Random Forest modelling
- Model evaluation
- Feature importance
- Permutation importance
- SQL analysis
- Visualisations
- Project conclusions

---

# 👩‍💻 Author

**Andrea Mejia**

Data Analytics & Cybersecurity Portfolio Project
