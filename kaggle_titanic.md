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

Let's load the training set and see what kinds of features we have, and determine which ones are helpful for classification.
```python
import pandas as pd

train_df = pd.read_csv('train.csv')
print train_df.count()
print ''
print train_df.pivot_table(values='Age', index=['Pclass'], columns=['Sex', 'Survived'])
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
dtype: int64
```
```ipython
Sex          female                  male           
Survived          0          1          0          1
Pclass                                              
1         25.666667  34.939024  44.581967  36.248000
2         36.000000  28.080882  33.369048  16.022000
3         23.818182  19.329787  27.255814  22.274211
```
```ipython
In [88]: train_df.pivot_table(values='Survived', index=['Sex'], columns=['Pclass'])
Out[88]: 
Pclass         1         2         3
Sex                                 
female  0.968085  0.921053  0.500000
male    0.368852  0.157407  0.135447
```







