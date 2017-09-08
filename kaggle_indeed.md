---
layout: default
---
# [](#header-1)[Home](index) || [Research](research) || [<font color="MediumSlateBlue">Projects</font>](projects)
<br/>

* * * 

## [](#header-2)Projects

*   <font color="MediumSlateBlue">Indeed.com: Data Science, Machine Learning,<br/>& AI jobs across the US</font>
*   [Twitter Streaming & Sentiment Analysis for<br/>Game 7 of the NHL Eastern Conference Finals:<br/>Penguins vs. Senators](hockeytweets)<br/>
*   [(Kaggle) Titanic: Machine Learning from Disaster](kaggle_titanic)<br/>
<!-- *   [(Kaggle) House Prices: Advanced Regression Techniques](kaggle_houseprices)<br/> -->
<!-- *   [(Kaggle) Personalized Medicine: Redefining Cancer Treatment](kaggle_cancer)<br/> -->


* * * 
<br/>
# [](#header-1)<center><i>INDEED.COM: DATA SCIENCE, MACHINE LEARNING, AND AI JOBS ACROSS THE US<i/><center/>
<br/>
<br/>
# [](#header-2)I. INTRODUCTION
I got the idea for this project since I'm interested in data science, machine learning, and artificial intelligence, and I'm currently looking for a career in these fields. I wanted to examine such things as geographical locations of jobs, company sizes (start ups vs. older corporations), company ratings, salary information, and possibly some common keywords given in job summaries that could give information on job requirements.

I scraped these job listings from Indeed.com on Sept. 5, 2017. It would be interesting to track some of the features over time to see if there are any seasonal or yearly changes in data science, machine learning, and artificial intelligence job opportunities.

The code that I wrote for this project can be found in my [Projects/Indeed](https://github.com/mrbrins82/Projects/tree/master/Indeed) repository.

<br/>
<br/>
# [](#header-2)II. GETTING THE DATA
<br/>
# [](#header-3)<center>Part 1. Web Scraping<center/>
```python
import numpy as np
import requests
import time
import os

from bs4 import BeautifulSoup
```
```python 
def get_job_info(job):
    """
        We'll want: job title, company, location, 
                    salary (if given), company size, 
                    company rating, and a job summary.
    """

    try:
        job_title = str(job.find('h2', 'jobtitle').a.text.strip())
    except:
        job_title = 'NA'

    try:
        company = str(job.find('span', 'company').text.strip())
    except:
        company = 'NA'

    try:
        location = str(job.find('span', 'location').text.strip())
    except:
        location = 'NA'

    try:
        salary = str(job.find('span', 'no-wrap').text)
    except:
        salary = 'NA'

    try:
        company_size =  int(job.find('span', 'slNoUnderline').text.split()[0]) # use number of company ratings as metric for company size
    except:
        company_size = 'nan'

    try:
        company_rating = float(job.find('span', 'rating').get('style')[6:10]) # Each company's star rating is given by pixel width
    except:
        company_rating = 'nan'
    
    try:
        summary = str(job.find('span', 'summary').text.strip()).replace('"', "'")
    except:
        print 'Could not get job summary for %s: %s.'%(company, job_title)
        summary = 'NA'
    

    return  job_title, company, location, salary, company_size, company_rating, summary
```
```python
def get_next_page(soup):
    """
    """
    next_page_info = soup.find('div', {'class':'pagination'})
    pages_list = next_page_info.find_all('a')
    next_page = pages_list[-1]
    # the new url has separate parts that we need to put together
    next_middle = next_page.get('href')
    next_end = next_page.get('data-pp')

    new_url = home + next_middle + '&pp=' + next_end

    print 'Next url --> ',new_url
    return new_url
```
```python 
sleep_time = 5 # wait 5 seconds before going to the next page
number_of_pages = 60
job_type = ['artificial', 'intelligence']

home = 'https://www.indeed.com'
url = home + '/jobs?q=%s+%s'%(job_type[0], job_type[1])
print 'First page --> ', url
html = requests.get(url).text
soup = BeautifulSoup(html, 'html5lib')

jobs = soup.find_all('div', {'class':' row result'})

next_page_number = 2
time.sleep(sleep_time) # it's polite to wait a little bit
while next_page_number <= number_of_pages:


    try:
        new_url = get_next_page(soup)
        new_html = requests.get(new_url).text
        new_soup = BeautifulSoup(new_html, 'html5lib')

        new_jobs = new_soup.find_all('div', {'class':' row result'})
        jobs = jobs + new_jobs
    
        soup = new_soup
        del(new_html, new_soup, new_jobs)
    except:
        print 'Could not load page %d'%next_page_number

    next_page_number += 1

    time.sleep(sleep_time)
```
```python
# write all of the lines to a .csv file
filename = 'jobs_' + job_type[0] + '_' + job_type[1] + '.csv'
if os.path.exists(filename):
    os.system('rm ' + filename) 

f = open(filename, 'a')
header =  '"jobtitle","company","location","salary","companysize","companyrating","summary"\n'
f.write(header)
for ii in xrange(len(jobs)):
    job_info = get_job_info(jobs[ii])
#    print job_info
    line = '"' + str(job_info[0]) + '","' + str(job_info[1]) + '","' + str(job_info[2]) + '","'\
               + str(job_info[3]) + '",' + str(job_info[4]) + ',' + str(job_info[5]) + ',"' + str(job_info[6]) + '"\n'
    f.write(line)

f.close()
```

<br/>
<br/>
# [](#header-2)III. ANALYZING THE DATA
The code used in my analysis is aptly called _analysis.py_ and currently resides in my github repository <a href="https://github.com/mrbrins82/Projects/blob/master/Indeed/analysis.py" >here</a>.

Now that we have some data, let's take a look at it.
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.colors as colors
import seaborn as sns
import os, sys

"""
get_locations is my own script that get coordinates from 
the city locations in the job csv files. Uncomment the 
'import get_locations' line if new jobs/locations have
been added.
"""
#import get_locations
 
from mpl_toolkits.basemap import Basemap
from matplotlib.colors import LinearSegmentedColormap
 
# load the job csv files and the csv with coordinates
ai_df = pd.read_csv('jobs_artificial_intelligence.csv')
ds_df = pd.read_csv('jobs_data_scientist.csv')
ml_df = pd.read_csv('jobs_machine_learning.csv')
locations_df = pd.read_csv('locations.csv')

# tag each data frame by it's job keyword search
ai_df['keyword'] = 'AI'
ds_df['keyword'] = 'DS'
ml_df['keyword'] = 'ML'

# let's put all of the data frames together and drop duplicates
temp_df = pd.concat((ai_df, ds_df))
all_df = pd.concat((temp_df, ml_df))

df = all_df.drop_duplicates()
locations_df = locations_df.drop_duplicates()

df.count()
```
```ipython
jobtitle         2732
company          2772
location         2775
salary            138
companysize      1228
companyrating    1904
summary          1639
keyword          2775
dtype: int64
```
We have a total of 2807 job listings (924 AI, 951 Data Science, 932 Machine Learning). There are 32 duplicate job listings which after dropping, leaves us with a grand total of 2775 job listings. As we can see, most of the features have a decent amount of values except for the _salary_ feature. 

<br/>
# [](#header-3)<center>Part 1. Who's hiring, and for what?</center>
Let's take a look at the top 25 companies in terms of total job listings broken down into artificial intelligence, machine learning, and data science jobs.
<center><img src="./assets/images/top_companies.png" alt="top companies" width="700" height="350" />
</center>
This is a pretty interesting plot. Here we have the top 25 companies in terms of number of total job listings. It's not surprising to see companies like Apple, Google, or Twitter in the list, but it is interesting to see what types of jobs these companies are hiring for. For example, Udacity Inc. is purely looking to fill artificial intelligence positions. Twitter, and McKinsey & Co. are only looking for data science and machine learning positions.

What are some of the most common job titles that people are hiring for?
<center><img src="./assets/images/top_titles.png" alt="top titles" width="700" height="350" />
</center>

<br/>
# [](#header-3)<center>Part 2. Job Location<center/>

<center><img src="./assets/images/heat_scatter_us.png" alt="us heat map" width="600" height="400" />
</center>
<center><img src="./assets/images/heat_scatter_nyc_zoom.png" alt="nyc zoom heat map" width="500" height="500" />
</center>
<center><img src="./assets/images/heat_scatter_sanfran_zoom.png" alt="san fran zoom heat map" width="500" height="500" />
</center>


