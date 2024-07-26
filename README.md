# Starbucks Capstone Project

## Overview

The Starbucks Capstone Project is designed to analyze customer behavior on the Starbucks rewards mobile app using simulated data. The goal is to determine which demographic groups respond best to different types of offers, including discounts, BOGO (buy one get one free) deals, and informational advertisements. 

## Objective

The project involves combining transaction, demographic, and offer data to evaluate the effectiveness of various offers. The data set simulates customer interactions with offers and purchases, allowing us to understand which demographic segments are most responsive to different types of offers.

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

## Installation

Ensure you have the following libraries installed. You can install them using `pip`:

```bash
pip install pandas numpy matplotlib scikit-learn
```

## Usage

1. **Load the Data**: Read the JSON files into dataframes.

    ```python
    import pandas as pd
    portfolio = pd.read_json('portfolio.json', orient='records', lines=True)
    profile = pd.read_json('profile.json', orient='records', lines=True)
    transcript = pd.read_json('transcript.json', orient='records', lines=True)
    ```

2. **Data Cleaning and Preprocessing**: Clean and preprocess the data for analysis. This includes handling missing values, converting timestamps, and merging datasets.

3. **Analysis**: Analyze the data to determine which demographic groups are most responsive to different types of offers. Use various machine learning models to predict offer completion based on demographic and transaction data.

4. **Model Evaluation**: Evaluate the performance of different machine learning models using metrics such as accuracy and F1 score.

5. **Visualization**: Create visualizations to represent the findings and insights from the data.

## Libraries Used

The following libraries are used in this project:

- **Pandas**: For data manipulation and analysis.
- **NumPy**: For numerical operations.
- **Matplotlib**: For data visualization.
- **Scikit-Learn**: For machine learning algorithms and metrics.

```python
import pandas as pd
import numpy as np
import math
import json
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score, f1_score, classification_report
from sklearn.neighbors import KNeighborsClassifier
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier, AdaBoostClassifier, GradientBoostingClassifier
from time import time
```
