# Starbucks Capstone Project

[![Python](https://img.shields.io/badge/Python-3.12-blue.svg)](https://www.python.org/)
[![Scikit-Learn](https://img.shields.io/badge/ScikitLearn-1.4.2-orange.svg)](https://scikit-learn.org/)
[![Status](https://img.shields.io/badge/Status-Complete-success.svg)]()

> **Predicting customer offer completion using machine learning to optimize Starbucks mobile app promotional targeting**

📝 **Blog Post:** [Read the full analysis here](https://fatinshariff.github.io/sbcapstone/2024/07/30/SBProject.html)

## 🎯 Project Highlights

- **Final Model Performance:** 88% accuracy, F1-score of 0.85
- **Best Algorithm:** Gradient Boosting Classifier (after testing 5 models)
- **Dataset:** 17,000 customers, 300,000+ transactions
- **Key Discovery:** Offer timing matters more than customer demographics
- **Unique Feature Engineering:** Separated "truly completed" (influenced by offer) from "accidental completed" (natural buyers)

---

## 📊 Overview

The Starbucks Capstone Project analyzes customer behavior on the Starbucks rewards mobile app using simulated data. The goal is to predict which customers will complete promotional offers (BOGO, discounts, informational) and identify the key factors driving offer success.

### Business Problem

Starbucks sends promotional offers through their mobile app, but not all customers respond equally. This project builds a machine learning model to:
1. Predict which customers will complete offers
2. Identify which features (timing, reward, demographics) drive completion
3. Distinguish customers influenced by offers from natural high-spenders

## Dataset

The data for this project is contained in three JSON files:

1. **portfolio.json**: Contains information about each offer.
   - `id`: Offer ID
   - `offer_type`: Type of offer (e.g., BOGO, discount, informational)
   - `difficulty`: Minimum required spend to complete an offer
   - `reward`: Reward given for completing an offer
   - `duration`: Time for offer to be open (in days)
   - `channels`: List of channels where the offer is available

2. **profile.json**: Contains demographic data for each customer.
   - `age`: Age of the customer
   - `became_member_on`: Date when customer created an app account
   - `gender`: Gender of the customer ('M' for male, 'F' for female, 'O' for other)
   - `id`: Customer ID
   - `income`: Customer's income

3. **transcript.json**: Records transactions, offers received, offers viewed, and offers completed.
   - `event`: Record description (e.g., transaction, offer received, offer viewed, etc.)
   - `person`: Customer ID
   - `time`: Time in hours since the start of the test (data starts at t=0)
   - `value`: Dictionary containing offer ID or transaction amount depending on the record

---

## 🔑 Key Results

### Model Performance Comparison

| Model | Train F1 | Test F1 | Accuracy | Training Time |
|-------|----------|---------|----------|---------------|
| **Gradient Boosting** ✅ | 0.89 | **0.84** | **88%** | ~3.5s |
| AdaBoost | 0.85 | 0.81 | 87% | ~1.2s |
| Random Forest | 0.99 | 0.80 | 85% | ~4.8s |
| K-Neighbors | 0.87 | 0.79 | 84% | ~0.02s |
| Decision Tree | 1.00 | 0.78 | 84% | ~0.08s |

**Why Gradient Boosting?**
- Best Test F1-score (0.84) with minimal overfitting (train-test gap: 0.05)
- Improved to F1 = 0.85 after Grid Search hyperparameter tuning
- Handles imbalanced data effectively (only 36% positive class)

### Top 5 Feature Importance

1. **Time** (offer send timing) - Most critical factor
2. **Informational** (offer type) - Strong negative impact
3. **Reward** (offer amount) - Higher rewards drive completions
4. **Duration** (offer validity) - Longer windows increase success
5. **Income** (customer wealth) - Wealthier customers complete more

**Key Insight:** Offer design (timing, type, reward, duration) matters more than customer demographics for predicting completion.

---

## 🚀 Methodology

### 1. Data Cleaning & Feature Engineering

**Handled Missing Data:**
- Removed 13% of customers with invalid age (age=118, default value)
- Final dataset: ~148,000 customer-offer pairs

**Feature Engineering Highlights:**
- **`truly_completed`**: Offer viewed AND completed (customer influenced by offer)
- **`accidental_completed`**: Completed WITHOUT viewing (natural high-spender)
- **`tenure_days`**: Days since membership (captures customer loyalty)

**Why this matters:** Separating truly influenced customers from accidental completions allows targeted offer strategies.

### 2. Exploratory Data Analysis

**Discovered Three Temporal Data Collection Periods:**
- **Period 1 (2013-2015):** Income capped at ~$100k
- **Period 2 (2015-2017):** Income capped at ~$120k  
- **Period 3 (2017+):** Different patterns, suggesting app redesign

**Finding:** These aren't data quality issues—they reflect Starbucks' evolving data collection policies.

**Customer Demographics:**
- Age: Mean ~55 years, normally distributed
- Gender: 60% Male, 40% Female (Females slightly more responsive)
- Income: Shows systematic caps at $75k, $100k, $120k

### 3. Machine Learning Pipeline

**Models Tested:**
1. Decision Tree (baseline, high interpretability)
2. Random Forest (ensemble averaging)
3. AdaBoost (handles imbalanced data)
4. K-Neighbors (non-parametric)
5. Gradient Boosting (sequential error correction) ✅

**Hyperparameter Tuning (Grid Search):**
- Parameters: `n_estimators`, `learning_rate`, `min_samples_leaf`
- 36 combinations × 3 folds = 108 training rounds (~97 seconds)
- Best params: `n_estimators=100`, `learning_rate=0.3`, `min_samples_leaf=30`

**Performance Improvement:**
- Before tuning: F1 = 0.84
- After tuning: F1 = 0.85 (+1.2% improvement)

---

## 📁 Project Structure

```
Starbucks_Project/
├── Starbucks_Capstone_Project.ipynb  # Main analysis notebook
├── PROJECT_SUMMARY.md                 # Interview preparation summary
├── README.md                          # This file
├── Data/
│   ├── portfolio.json                 # Offer information
│   ├── profile.json                   # Customer demographics
│   └── transcript.json                # Transaction records
├── image_blog/                        # Visualizations
└── requirements.txt                   # Python dependencies
```

---

## 🛠️ Installation & Usage

### Prerequisites

Ensure you have Python 3.12+ installed. Install dependencies:

```bash
pip install -r requirements.txt
```

Or manually:

```bash
pip install pandas numpy matplotlib scikit-learn
```

### Quick Start

1. **Clone the repository:**
   ```bash
   git clone https://github.com/fatinshariff/sbcapstone.git
   cd sbcapstone
   ```

2. **Launch Jupyter Notebook:**
   ```bash
   jupyter notebook Starbucks_Capstone_Project.ipynb
   ```

3. **Run all cells** to reproduce the analysis

### Code Example

```python
import pandas as pd

# Load data
portfolio = pd.read_json('Data/portfolio.json', orient='records', lines=True)
profile = pd.read_json('Data/profile.json', orient='records', lines=True)
transcript = pd.read_json('Data/transcript.json', orient='records', lines=True)

# Feature engineering example
df['truly_completed'] = ((df['offer_viewed'] == 1) & (df['offer_completed'] == 1)).astype(int)
df['tenure_days'] = (max_date - df['became_member_on']).dt.days

# Train model
from sklearn.ensemble import GradientBoostingClassifier
model = GradientBoostingClassifier(n_estimators=100, learning_rate=0.3, min_samples_leaf=30)
model.fit(X_train, y_train)
```

---

## 💡 Key Contributions & Learnings

### 1. Novel Feature Engineering
- Created `truly_completed` vs `accidental_completed` distinction
- Engineered `tenure_days` from raw membership dates
- **Impact:** Improved model's ability to identify offer-influenced behavior

### 2. Data Quality Investigation
- Discovered temporal income capping patterns
- Validated data integrity through cross-temporal analysis
- **Impact:** Proved systematic caps were intentional business decisions, not errors

### 3. Model Optimization
- Tested 5 algorithms with rigorous train/test validation
- Grid Search tuning with 3-fold cross-validation
- **Impact:** Achieved 88% accuracy while maintaining good generalization (minimal overfitting)

---

## 📈 Business Recommendations

Based on the analysis:

1. **Optimize Timing:** Offer send time is the #1 predictor—test different times of day
2. **Reconsider Informational Offers:** Near-zero completion rates suggest redesign or removal
3. **Leverage Reward & Duration:** These are powerful levers for driving completions
4. **Focus on Offer Design > Demographics:** Feature importance shows offer characteristics matter more than customer segmentation

---

## 🔧 Technologies Used

| Technology | Purpose |
|------------|---------|
| **Python 3.12** | Core programming language |
| **Pandas 2.2.1** | Data manipulation and analysis |
| **NumPy 1.26.4** | Numerical operations |
| **Matplotlib 3.8.4** | Data visualization |
| **Scikit-Learn 1.4.2** | Machine learning models & metrics |
| **Jupyter Notebook** | Interactive development environment |
| **Git/GitHub** | Version control & collaboration |

---

## 🚧 Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| **Imbalanced dataset (36% positive class)** | Used F1-score metric instead of accuracy |
| **Missing demographic data (13%)** | Removed age=118 outliers after validation |
| **Untrackable offer-transaction link** | Created `truly_completed` flag to infer influence |
| **Overfitting risk** | Applied Grid Search with regularization params |

---

## 📚 Future Improvements

- [ ] Analyze channel effectiveness (email vs mobile vs social vs web)
- [ ] Optimize reward amounts and duration values
- [ ] Build real-time recommendation API
- [ ] Implement customer segmentation for targeted campaigns
- [ ] A/B test offer timing strategies

---

## 📄 License

This project is part of the Udacity Data Science Nanodegree program.

---

## 👤 Author

**Fatin Shariff**

- GitHub: [@fatinshariff](https://github.com/fatinshariff)
- Blog: [Project Analysis](https://fatinshariff.github.io/sbcapstone/2024/07/30/SBProject.html)

---

## 🙏 Acknowledgments

- Starbucks for providing the simulated dataset
- Udacity Data Science Nanodegree program
- Scikit-learn documentation and community

---

**Project Status:** ✅ Complete & Ready for Production
