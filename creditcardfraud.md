---
layout: default
---
# [](#header-1)[Home](index) || [Research](research) || [<font color="MediumSlateBlue">Projects</font>](projects) || [Kaggle](kaggle)
<br/>
## [](#header-2)Current Projects
<font color="MediumSlateBlue">Credit Card Fraud</font><br/>[NYSE](nyse)<br/>

# [](#header-1)Credit card fraud
The data used in this analysis was obtained from Kaggle and can be found <a href="https://www.kaggle.com/dalpozz/creditcardfraud">here</a>. More information on this data set can be found in the README\_creditfraud file in my Projects repository.


## [](#header-2)Analysis
This data set contains 284807 samples and 31 features. The features are: <i>Time</i>, <i>Amount</i>, <i>V1</i>, <i>V2</i>, ..., <i>V28</i>, and <i>Class</i>. The dimensionality of the original data set has been reduced using principal component analysis to obtain the <i>Vi</i>'s, and we aren't given any information about what these features represent. This is also a highly imbalanced data set. There are only 492 fraud cases out of 284,807, which accounts for 0.173% of all samples

The scatter plot in Fig.1 below shows the relationship between the amount of each credit card transaction, the time in seconds after the first transaction, and whether or not the transaction was fraudulent.
<center><img src="./assets/images/scatter_Time_Amount.png" alt="tim v amount scatter" width="600" height="400" />
</center>
<center><strong>Fig.1</strong></center>
We can see that there is periodic variation in the data with a period of approximately 80,000 seconds, <i>i.e.</i> one day. So, we're looking at a two-day data set here. The legitimate transactions follow this periodicity fairly well, but we can't really tell any information about the periodic behavior of fraudulent cases since there are so few of them.


