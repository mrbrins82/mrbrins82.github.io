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
<br/>
# [](#header-1)<center><i>TITANIC: MACHINE LEARNING FROM DISASTER<i/><center/>
<br/>
<br/>
# [](#header-2)I. INTRODUCTION
The sinking of the Titanic resulted in the loss of many lives. There were over 1500 deaths, accounting for approximately 2 out of every 3 people on the ship (passengers and crew combined). In this competition, Kaggle provides two data sets (a training set and a testing set) which contain numerical and categorical features for each of the passengers. The two sets contain the same numerical and categorical features, except that the training set also tells us if the passengers survived. The objective is to use the data to predict whether or not passengers in the testing data set survived or not. This is a binary classification problem, since we wish to classify each passenger as belonging to one of two classes: deceased, survived
<br/>
<br/>
# [](#header-2)II. EXPLORING THE DATA
<br/>
# [](#header-2)<center>Part 1.<center/>
The first thing we need to do is load the data and take a quick look at it to see what, if anything, we need to do with it.
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

from sklearn.preprocessing import LabelEncoder

# load the training and testing data files
train_df = pd.read_csv('train.csv')
test_df = pd.read_csv('test.csv')

# check to see if the data files have any missing values
print train_df.shape # gives dimension of training set
print ''
print train_df.count() # this will tell us how many non-NA values for each feature
print ''
print train_df.dtypes # this will tell us what type of data each feature contains
```
```ipython
(891, 12)

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

PassengerId      int64
Survived         int64
Pclass           int64
Name            object
Sex             object
Age            float64
SibSp            int64
Parch            int64
Ticket          object
Fare           float64
Cabin           object
Embarked        object

```
```python
print test_df.shape
print ''
print test_df.count()
```
```ipython
(418, 11)

PassengerId    418
Pclass         418
Name           418
Sex            418
Age            332
SibSp          418
Parch          418
Ticket         418
Fare           417
Cabin           91
Embarked       418
```

We can see that there are some missing values for the _Age_, _Cabin_, and _Embarked_ features in the training set and one missing _Fare_ value in the testing set as well. We will need to look at the importance of these features in terms of survival to decide whether or not we should fill in the missing values or if we can just drop any of them.

In order to build a classifier, all of the data must be in numerical form. If there are any categorial features, we will need to encode them. A quick check of the data types gives us
```python
print train_df.dtypes
```
```ipython
PassengerId      int64
Survived         int64
Pclass           int64
Name            object
Sex             object
Age            float64
SibSp            int64
Parch            int64
Ticket          object
Fare           float64
Cabin           object
Embarked        object
```

_Name_, _Sex_, _Ticket_, _Cabin_, and _Embarked_ are all categorical features. At this point, it's only useful to encode _Sex_ and _Embarked_ into numerical values since there are too many possible values for _Name_, _Ticket_, and _Cabin_. Before we can encode the _Sex_ and _Embarked_ features, we'll need to fill the two missing values for _Embarked_. Taking a quick look at the two passengers that don't have values for _Embarked_, we have
```python
print train_df[train_df.Embarked.isnull()]
```
```ipython
     PassengerId  Survived  Pclass                                       Name  \
61            62         1       1                        Icard, Miss. Amelie   
829          830         1       1  Stone, Mrs. George Nelson (Martha Evelyn)   

        Sex   Age  SibSp  Parch  Ticket  Fare Cabin Embarked  
61   female  38.0      0      0  113572  80.0   B28      NaN  
829  female  62.0      0      0  113572  80.0   B28      NaN 
```

These are two 1st-class women that are on the same ticket and in the same cabin, so they probably boarded at the same location. How do we determine what to set as their port of embarkation? Let's look at a couple pivot tables and see if we can find any helpful information. The first pivot table shows a breakdown by _Sex_, _Pclass_, _Embarked_, and shows the number of people in each subset that embarked at a specific port. The second pivot table shows the same breakdown, except that instead of the number of people in each subset, it shows the survival probability.

```python
train_df.pivot_table(values='Survived', index=['Sex', 'Pclass'], columns=['Embarked'], aggfunc='count')
```
```ipytyon
Embarked        C   Q    S
Sex    Pclass             
female 1       43   1   48
       2        7   2   67
       3       23  33   88
male   1       42   1   79
       2       10   1   97
       3       43  39  265
```
```python
train_df.pivot_table(values='Survived', index=['Sex', 'Pclass'], columns=['Embarked'], aggfunc='mean')
```
```ipython
Embarked              C         Q         S
Sex    Pclass                              
female 1       0.976744  1.000000  0.958333
       2       1.000000  1.000000  0.910448
       3       0.652174  0.727273  0.375000
male   1       0.404762  0.000000  0.354430
       2       0.200000  0.000000  0.154639
       3       0.232558  0.076923  0.128302
```

The above two pivot tables show us that 1st-class women were approximately equally likely to board at either Cherbourg or Southampton. There is a slightly higher probability for 1st-class women that boarded at Cherbourg to survive, and since the two women missing _Embarked_ values survived, I'll choose to fill the missing values as Cherbourg.

```python
train_df.Embarked.iloc[61] = 'C'
train_df.Embarked.iloc[829] = 'C'
```

We are now ready to map the _Sex_ and _Embarked_ features to numerical values. For this, we can use sklearn's LabelEncoder.
```python
le_Sex = LabelEncoder()
train_df.Sex = le_Sex.fit_transform(train_df.Sex)
test_df.Sex = le_Sex.transform(test_df.Sex)

le_Embarked = LabelEncoder()
train_df.Embarked = le_Embarked.fit_transform(train_df.Embarked)
test_df.Embarked = le_Embarked.transform(test_df.Embarked)

```
<br/>
# [](#header-3)<center>Part 2.<center/>
At this point we've filled in a couple missing _Embarked_ values, and converted the cagetorical _Sex_, and _Embarked_ features into numerical values. We would like to now examine the strength of correlation that the data have with the target variable, _Survived_.
```python
print train_df.corr()
```
```ipython
             PassengerId  Survived    Pclass       Sex       Age     SibSp  \
PassengerId     1.000000 -0.005007 -0.035144  0.042939  0.036847 -0.057527   
Survived       -0.005007  1.000000 -0.338481 -0.543351 -0.077221 -0.035322   
Pclass         -0.035144 -0.338481  1.000000  0.131900 -0.369226  0.083081   
Sex             0.042939 -0.543351  0.131900  1.000000  0.093254 -0.114631   
Age             0.036847 -0.077221 -0.369226  0.093254  1.000000 -0.308247   
SibSp          -0.057527 -0.035322  0.083081 -0.114631 -0.308247  1.000000   
Parch          -0.001652  0.081629  0.018443 -0.245489 -0.189119  0.414838   
Fare            0.012658  0.257307 -0.549500 -0.182333  0.096067  0.159651   
Embarked        0.013078 -0.174199  0.170334  0.115513 -0.040248  0.070653   

                Parch      Fare  Embarked  
PassengerId -0.001652  0.012658  0.013078  
Survived     0.081629  0.257307 -0.174199  
Pclass       0.018443 -0.549500  0.170334  
Sex         -0.245489 -0.182333  0.115513  
Age         -0.189119  0.096067 -0.040248  
SibSp        0.414838  0.159651  0.070653  
Parch        1.000000  0.216225  0.042325  
Fare         0.216225  1.000000 -0.229304  
Embarked     0.042325 -0.229304  1.000000 
```

At first sight, the strongest correlations seem to be with _Pclass_, _Sex_, _Fare_, and _Embarked_. We can't write off the features with weaker correlations just yet as they may still contain some useful information.

In the next few subsections, we will examine each of the features individually and determine its usefulness for classification and if there are any new features that we can engineer from them.

<br/>
# [](#header-3)_Pclass_

<center><img src="./assets/images/class_survival_prob.png" alt="class_survival" width="500" height="500" />
</center>

<br/>
# [](#header-3)_Sex_
<center><img src="./assets/images/sex_survival_prob.png" alt="sex_survival" width="500" height="500" />
</center>


<br/>
# [](#header-3)_Age_
<center><img src="./assets/images/class_survival_prob.png" alt="class_survival" width="500" height="500" />
</center>


<br/>
# [](#header-3)_SibSp_
<center><img src="./assets/images/sibsp_survival_prob.png" alt="sibsp_survival" width="500" height="500" />
</center>


<br/>
# [](#header-3)_Parch_
<center><img src="./assets/images/parch_survival_prob.png" alt="parch_survival" width="500" height="500" />
</center>


<br/>
# [](#header-3)_Fare_
<center><img src="./assets/images/class_survival_prob.png" alt="class_survival" width="500" height="500" />
</center>


<br/>
# [](#header-3)_Embarked_
<center><img src="./assets/images/embarked_survival_prob.png" alt="embarked_survival" width="500" height="500" />
</center>


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


