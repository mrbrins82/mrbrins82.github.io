---
layout: default
---
# [](#header-1)[Home](index) || [Research](research) || [<font color="MediumSlateBlue">Projects</font>](projects)
<br/>

* * * 

## [](#header-2)Projects

*   [Twitter Streaming & Sentiment Analysis for<br/>Game 7 of the NHL Eastern Conference Finals:<br/>Penguins vs. Senators](hockeytweets)<br/>
*   <font color="MediumSlateBlue">(Kaggle) Titanic: Machine Learning from Disaster</font>
*   [(Kaggle) House Prices: Advanced Regression Techniques](kaggle_houseprices)<br/>
*   [(Kaggle) Personalized Medicine: Redefining Cancer Treatment](kaggle_cancer)<br/>


* * *

# [](#header-1)Titanic: Machine Learning from Disaster

The sinking of the Titanic resulted in the loss of many lives. There were over 1500 deaths, accounting for approximately 2 out of every 3 people on the ship (passengers and crew combined). In this competition, Kaggle provides two data sets (a training set and a testing set) which contain numerical and categorical features for each of the passengers. The two sets contain the same numerical and categorical features, except that the training set also tells us if the passengers survived. The objective is to use the data to predict whether or not passengers in the testing data set survived or not. This is a binary classification problem, since we wish to classify each passenger as belonging to one of two classes: deceased, survived
<br/>
<br/>
# [](#header-2)Examining the data
Let's load the training set and see what kinds of features we have, and determine which ones are helpful for classification.
```python
import pandas as pd

# train.csv contains entries for 891 passengers total
train_df = pd.read_csv('train.csv')
print train_df.count() # this will tell us how many non-NA values for each feature
```
```ipython
PassengerId    891
Survived       891
Pclass         891
Name           891
Sex            891
Age            714
SibSp          891
Parch          891
Ticket         891
Fare           891
Cabin          204
Embarked       889
```
We can see that there are some missing values for the _Age_, _Cabin_, and _Embarked_ features. We can fill in the missing values or drop the columns all together. Age likely played a significant role in survival, as did class which could be tied to port of embarkation, so it will be important to fill the missing values.

Just how much do age, gender, and social class effect survival? Let's look at a couple pivot tables and see what information we can glean from them.
```python
print train_df.pivot_table(values='Survived', index=['Sex'], columns=['Pclass'])
```
```ipython
Pclass         1         2         3
Sex                                 
female  0.968085  0.921053  0.500000
male    0.368852  0.157407  0.135447
```
From the looks of this, it's quite obvious that gender and class had importance for survival. For example, 97% of 1st-class women survived, whereas only 13% of 3rd-class men survived. The survival rate of women beat out that of men in every class, and for males and females individually, a higher class meant a higher probability of survival.

In the next pivot table, we'll take a look at the average ages of passengers broken down by gender, class, and survival.
```python
print train_df.pivot_table(values='Age', index=['Pclass'], columns=['Sex', 'Survived'])
```
```ipython
Sex          female                  male           
Survived          0          1          0          1
Pclass                                              
1         25.666667  34.939024  44.581967  36.248000
2         36.000000  28.080882  33.369048  16.022000
3         23.818182  19.329787  27.255814  22.274211
```
In all but one case, survivors tended to be younger on average. This was not the case for 1st-class women, where the average age of survivors was about 35, and 26 for the deceased.

Age, gender, and class are clearly important features to incorporate into our machine learning model. How do the other features correlate to survival? We can look at the correlation between all of the numerical features in our training data frame.
```python
print train_df.corr()['Survived']
```
```ipython
PassengerId   -0.005007
Survived       1.000000
Pclass        -0.338481
Age           -0.077221
SibSp         -0.035322
Parch          0.081629
Fare           0.257307
```
The above shows how each of the numerical features correlates with survival specifically. The strongest correlation is with passenger class, with fare being the next strongest. One of the weaker correlations is with age, which is a bit surprising given the pivot table with mean ages. Finally, there is some correlation between the number of accompanying parents and children (Parch), and siblings and spouses (SibSp) and is comparable to the age correlation.
<br/>
<br/>
# [](#header-2)Cleaning the data


