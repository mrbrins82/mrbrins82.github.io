---
layout: default
---
# [](#header-1)[Home](index) || [Research](research) || [<font color="MediumSlateBlue">Projects</font>](projects)
<br/>

* * * 

## [](#header-2)Current Projects

*   <font color="MediumSlateBlue">Twitter Streaming During Game 7 Eastern<br/>Conference Finals: Penguins vs. Senators</font>
*   [Credit Card Fraud](creditcardfraud)
*   [NYSE](nyse)<br/>

* * * 

# [](#header-1)Twitter Streaming of Eastern Conference Finals Game 7 between the Pittsburgh Penguins and Ottawa Senators

Game 7 of the Eastern Conference Finals was one of the most stressful games I've ever watched as a Penguins fan. It took the Penguins two heart-palpitation-inducing overtime periods to score a goal by Chris Kunitz. No team has won back to back Stanley Cups in the salary cap era, and the Penguins stand a chance to be the first when they faceoff against the Nashville Predators. The last team to do it was the Detroit Red Wings in 97-98. Before that, the Pittsburgh Penguins in 91-92. 

I live streamed tweets in the moments leading up to the game and for the duration of the game, amassing a total of 15,208 tweets (64MB) with keywords: 'sidney crosby', 'pittsburgh penguins', 'ottawa senators', 'nhl playoffs', and 'stanley cup.'

<img src="./assets/images/ECF_game7_stream_hist.png" alt="tweet histogram" align="right" width="600" height="400" hspace="25" vspace="25"/>

The streaming began about an hour before the first faceoff and from the histogram of tweets-per-minute, we can see that tweeting begins to pick up about 20 or 30 minutes before gamtime and remains fairly constant for most of the game. It's pretty clear that towards the end of the game tweet frequency increases. I've used a Markov Chain Monte Carlo (MCMC) simulation to determine at what time the frequency changes, and what the frequencies are before and after that time. Given the data and using a Poisson model for counting the number of tweets-per-minute, the MCMC simulation samples the three parameters $$\lambda_1$$ ![](https://latex.codecogs.com/gif.latex?{\color{Blue}&space;\lambda_1})![](https://latex.codecogs.com/gif.latex?\inline&space;{\color{DarkOrange}&space;\lambda_1}), <a href="https://www.codecogs.com/eqnedit.php?latex={\color{DarkOrange}&space;\lambda_2}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?{\color{DarkOrange}&space;\lambda_2}" title="{\color{DarkOrange} \lambda_2}" /></a>, and <a href="https://www.codecogs.com/eqnedit.php?latex={\color{DarkOrange}&space;\tau}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?{\color{DarkOrange}&space;\tau}" title="{\color{DarkOrange} \tau}" /></a> which are the initial tweeting rate, the final tweeting rate, and the time at which they change respectively. The posterior distributions for these parameters are shown in the plot below.

<img src="./assets/images/ECF_game7_stream_poisson_stats.png" alt="tweet histogram" align="right" width="600" height="600" hspace="25" vspace="25"/>

I've taken the most likely values of each of these parameters to plot in the first figure. Each of the frequencies is plotted in green, and the break takes place at the most probable value of <a href="https://www.codecogs.com/eqnedit.php?latex=\tau" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\tau" title="\tau" /></a>.
