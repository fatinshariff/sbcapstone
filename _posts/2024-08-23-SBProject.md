---
title: "Udacity Starbucks Capstone Challenge"
date: 2024-07-30
---


![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/SB_profile.png?raw=true)

## Introduction

This data set contains simulated data that mimics customer behavior on the Starbucks rewards mobile app. Once every few days, Starbucks sends out an offer to users of the mobile app. An offer can be merely an advertisement for a drink or an actual offer such as a discount or BOGO (buy one get one free). Some users might not receive any offer during certain weeks. 

Not all users receive the same offer, and that is the challenge to solve with this data set.

The task is to combine transaction, demographic and offer data to determine **which demographic groups respond best to which offer type**. This data set is a simplified version of the real Starbucks app because the underlying simulator only has one product whereas Starbucks actually sells dozens of products.

Every offer has a validity period before the offer expires. As an example, a BOGO offer might be valid for only 5 days. You'll see in the data set that informational offers have a validity period even though these ads are merely providing information about a product; for example, if an informational offer has 7 days of validity, we can assume the customer is feeling the influence of the offer for 7 days after receiving the advertisement.

Transactional data showing user purchases made on the app including the timestamp of purchase and the amount of money spent on a purchase is given. This transactional data also has a record for each offer that a user receives as well as a record for when a user actually views the offer. There are also records for when a user completes an offer. 

Keep in mind as well that someone using the app might make a purchase through the app without having received an offer or seen an offer.

## Example

To give an example, a user could receive a discount offer buy 10 dollars get 2 off on Monday. The offer is valid for 10 days from receipt. If the customer accumulates at least 10 dollars in purchases during the validity period, the customer completes the offer.

However, there are a few things to watch out for in this data set. Customers do not opt into the offers that they receive; in other words, a user can receive an offer, never actually view the offer, and still complete the offer. For example, a user might receive the "buy 10 dollars get 2 dollars off offer", but the user never opens the offer during the 10 day validity period. The customer spends 15 dollars during those ten days. There will be an offer completion record in the data set; however, the customer was not influenced by the offer because the customer never viewed the offer.

## Cleaning

This makes data cleaning especially important and tricky.

We also need to take into account that some demographic groups will make purchases even if they don't receive an offer. From a business perspective, if a customer is going to make a 10 dollar purchase without an offer anyway, we wouldn't want to send a buy 10 dollars get 2 dollars off offer. We'll want to try to assess what a certain demographic group will buy when not receiving any offers.


# Data Sets


All neccessary libraries are imported as below

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/libraries.png?raw=true)

The data is contained in three files:

1. portfolio.json - containing offer ids and meta data about each offer (duration, type, etc.)
2. profile.json - demographic data for each customer
3. transcript.json - records for transactions, offers received, offers viewed, and offers completed

Here is the schema and explanation of each variable in the files:


## Portfolio




**portfolio.json**
* id (string) - offer id
* offer_type (string) - type of offer ie BOGO, discount, informational
* difficulty (int) - minimum required spend to complete an offer
* reward (int) - reward given for completing an offer
* duration (int) - time for offer to be open, in days
* channels (list of strings)

**Cleaning done to this dataset:**
* rename column id to offer id
* change id to numerical offer id
* create a dummy column for offer_type (informational,discount,bogo)
* create dummy columns for channels

The cleaned dataset for the portfolio look as below:

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/portfolio.png?raw=true)

## Profile

**profile.json**
* age (int) - age of the customer 
* became_member_on (int) - date when customer created an app account
* gender (str) - gender of the customer (note some entries contain 'O' for other rather than M or F)
* id (str) - customer id
* income (float) - customer's income

**Cleaning done to this dataset:**

There is a high count for the outliers age (age = 118) which is more than 2k. This could be when customer didn't enter their age in the form, it will automatically set to a default date.

We have to take care of missing values if we want to do modelling later. There are three options that we have; get rid of the corresponding districts, get rid of the whole attributes or replace the values to some value. So, in this case we are going to remove this data because it's representing false age and even the income and gender data are missing.

So the next cleaning process for this dataset will be:

- remove profile with age = 118
- change id to customer_id
- change customer_id to encoded id
- change datatype for column became_member_on to date

The cleaned dataset for the profile look as below:

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/profile.png?raw=true)

## Transcript


**transcript.json**
* event (str) - record description (ie transaction, offer received, offer viewed, etc.)
* person (str) - customer id
* time (int) - time in hours since start of test. The data begins at time t=0
* value - (dict of strings) - either an offer id or transaction amount depending on the record

**Cleaning done to this dataset:**

The cleaned dataset for the trans look as below:

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/transcript.png?raw=true)

## Combining Dataset

All three separate dataframe are now combined on *customer_id* and also *offer_id* and look as below:

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/merge_df.png?raw=true)

# Data Visualisation

## Offers

In the first part, let's explore the offer types. Let find out the business funnel for the offers.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/offer_type.png?raw=true)

From the chart above, we can see that customer seems to be most interested in BOGO offer, hence it has the highest view but it doesn't mean that it has the highest conversion. The discout type offer seems to have the highest conversion which means most of them that viewed the offer has high chance of completing the offer. Meanwhile, the informational offer seems to have no conversion at all, maybe because customer realized they are not gaining anything here.

The next chart is showing the completed offer trend based on their difficulty.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/offer_difficulty.png?raw=true)

| Offer Type   | Difficulty | Count  |
|--------|-----|-------------|
| BOGO  | 5  | 2    |
| BOGO  | 10  | 2    |
| Discount    | 7  | 1 |
| Discount    | 10  | 2 |
| Discount    | 20  | 1 |
| Informational  | 0  | 2      |


Informational offer is the only offer that has 0 difficulty. Most people viewed the  difficulty between 5-10 with 7 having the highest count for both views and completions as well.
The completed offer for difficulty level 20, has more percentage of completions compared to the total viewed. This could obviously resulted by the inclusion of the demographic who doesn't even view the offer but still make the purchase anyway (which was explain earlier in the introduction).

The informational offer could be intentionnally to blast information to customers only and for example if there is a new released product or seasonal drinks. In reality there must have been people buying the new drinks. So in this case the 0 doesn't make sense unless if we can track the distribution of the offer related to which product and find the sales value of that particular product right after the offer distribution date. However, all this informations are not available here in the dataset.

## Distributions of Customer's Age,Gender and Income

The next part explored is the customer's data which are the age, gender and income and their and the offer.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/age_dist.png?raw=true)

They are normally distributed with median around 55 of age and slightly skewed to the right.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/income_dist.png?raw=true)

Above is the income distribution 
Below is the scatter plot to see relationship between age and income.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/income_age.png?raw=true)

The above plot some how show the data are being capped multiple times for both age and income.
The income is capped at around 75k in the first level, then at 100k and 120k for the subsequent levels. Same goes to the age where we can see the age start at 18 years old, then there is another minimum cap that start around mid 30s and another one around 50 years old. 

This is somehow related to the date the customer became a member as shown in the chart below: 



```python
#cust age and income distribution

profile.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>customer_id</th>
      <th>became_member_on</th>
      <th>income</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>14825.000000</td>
      <td>14825.000000</td>
      <td>14825</td>
      <td>14825.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>54.393524</td>
      <td>7412.000000</td>
      <td>2017-02-18 12:30:15.419898880</td>
      <td>65404.991568</td>
    </tr>
    <tr>
      <th>min</th>
      <td>18.000000</td>
      <td>0.000000</td>
      <td>2013-07-29 00:00:00</td>
      <td>30000.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>42.000000</td>
      <td>3706.000000</td>
      <td>2016-05-20 00:00:00</td>
      <td>49000.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>55.000000</td>
      <td>7412.000000</td>
      <td>2017-08-02 00:00:00</td>
      <td>64000.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>66.000000</td>
      <td>11118.000000</td>
      <td>2017-12-30 00:00:00</td>
      <td>80000.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>101.000000</td>
      <td>14824.000000</td>
      <td>2018-07-26 00:00:00</td>
      <td>120000.000000</td>
    </tr>
    <tr>
      <th>std</th>
      <td>17.383705</td>
      <td>4279.753206</td>
      <td>NaN</td>
      <td>21598.299410</td>
    </tr>
  </tbody>
</table>
</div>



![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/max_income.png?raw=true)

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/min_income.png?raw=true)

It's interesting how the maximum income for who became Starbuck's member before midyear 2015 was at 100k and right after that period, the value was at 120k. 

Meanwhile the minimum income is showing an obvious different trend before and after mid 2017 and a slight different trend from the beginneing of the date until mid 2015. This somehow conclude that there are three different timeframe data, mid 2013 to mid 2015, mid 2015 to mid 2017 and lastly after mid 2017. This could be the reason behind the capped data in the scatter plot on the income and age above. 

Now let's explore how gender in the dataset is divided.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/gender_dist.png?raw=true)

Majority of the customer are male with value of more than 50% of them. 

Is there a pattern where woman are more prone to complete the offer compare to man? Let's find about that.

## Relationships between Customer's Feature and the Offers


We realized earlier that the majority of our customer are male but does it apply the same for those who completed the offer? Let's find out on that.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/gender_completed.png?raw=true)

From the about result, we see that our male customer is in contrary contribute to least completed offer based on average per person. This is an interesting find where women and other gender tend to complete the offer most. 


Now let's look at how much each attribute correlate with the completed offer using **corr()** function. A new column that hold percentage value of the offer transaction over the total transactions is added.

Let's look at the age distribution after we have remove the maximum age of 118 in the earlier cleaning part.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/corr.png?raw=true)

The most positive correlated attribute is the perc_complete which is the amount of completed offer over the total transaction. It follows by the total amount spent by the customer. So the customer who spend more are more likely to complete the offer.

So correlation number above shows the strength of the linear relationship between the completed offers and all other numerical variables. Now let have a look at them when they are plotted in graphs.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/features_corr1.png?raw=true)

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/features_corr2.png?raw=true)

There is no direct correlation that we can see here. The higher transaction count doesn't mean the spending amount is also higher. The higher promo usage is in the middle area of the distribution. But what we can see is that the higher offer completion lies more in the the middle range of the transaction count.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/features_corr3.png?raw=true)

This is interesting. We see the higher promo usage, which in green to yellow color marks lies densely for transaction count roughly more than five and below 25 and the total income more than its 50th percentile to 75th percentile. We also noted here that people with higher income don't spend more than 20 transactions. 

# Building Machine Learning Model
Build a Machine Learning model to predict response of a customer to an offer and to find important features that help drive the offers in customers.

## Data Preparation

The dataset needed to be clean before proceeding with data modelling. The steps taken are as below:
- Removed missing value
- Making sure all are in numerical attributes and drop all non numerical
- Rescaled using MinMaxScaler on attributes so they range from 0 to 1
- Separate the target and features columns

After removing unwanted columns we are remained with 17 feature columns as listed below and ur target column is **offer_completed**.

Feature columns:
1. age
2. income
3. time
4. transaction
5. reward
6. difficulty
7. duration
8. bogo
9. discount
10. informational
11. email
12. mobile
13. social
14. web
15. gender_F
16. gender_M
17. gender_O

Since the offers_completed in the dataset is really imbalanced and is very underrepresented, we will be using the F1-score as our metric. F1-score is the combination of precision and recall in single unit.

## Modelling Data

Five different classifier models are used to find the best score and the results are comparible below:

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/ml_results.png?raw=true)

The model that scored the highest is Gradient Boosting Classifier with a score of 0.566 and the highest accuracy as well with 83%. While Gradient Boosting can be slower to train compared to some algorithms, it can handle large datasets efficiently and effectively.

## Refining Model

Since we have now selected a model, which is the Gradient Boosting Classifier, we can proceed with fine tune it to improve it. The method we are going to choose is Grid Search.

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/code_refine.png?raw=true)

The best combination of hyperparameter values are **n_estimators** = 100, **learning_rate** = 0.2 and **min_sample_leaf** = 30. 
The acccuracy and F1 score before and after are displayed below:

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/scores.png?raw=true)

The F1-score has improved from 0.5657 to 0.5955 after hypertuning the parameter. So this is our final best model.

The advantage of Grid search is that it is easy to implement to find the best model within the grid. However, it can quickly become time consuming as the number of the model continues to multiply when we add new hyperparameter values. In our case there are 3x3x4 = 36 combinations of the hyperparameter values and it will train each model 3 times (since we are using three-fold cross validation). Overall there are 36 x 3 = 108 rounds of training were done and it took more than 2 minutes for the time spent modelling.

## Feature Importance

![alt text](https://github.com/fatinshariff/sbcapstone/blob/main/image_blog/ml_fi.png?raw=true)

The top five features that that are predicted by our supervised learning model whether a customer would complete the offer or not are plotted in the graph above with the highest weighted feature as time followed by informational,reward,duration and income.

# Conclusion

__Challenges__

There were a few challenges that were faced in this project. Initially we discovered that the data were inconsistent for example for the age. Some of the customer's have very high value in age(118 years old) which could be some default value passed in the system when customer didn't entered their age when registering. This customers also have missing values for other main attributes. So, instead of taking this data in which would cause a bias result, we removed them. The percentage of them were about 13% from the total unique customers that we have. Removing this has reduced our dataset size which reduced our training sets too. 

Other inconsistency in data we found is in th eportfolio dataframe where there are *offer_id* and *offer id* in the same *value* column. So these are the small things that we need to clean up prior to analysing the dataset which if not, it may impact our analysis.

Besides, whenever a customer made a transaction, the transaction could not be link to whether the customer using the voucher or not. Let say if you can get this information, we can trace to the offer and find out which offer bring the higher revenue to the company. 

__Ideas for Improvements__

* There are many other attributes to explore in the EDA part. For example solving queries related to the channels(either web, mobile or social media) used in distributing offers, duration and membership.

* We could also try different modelling algorithms and applying some hyperparameter tuning to get better prediction model and on the same time making sure we are not overfitting our model. 

