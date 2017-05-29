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

Game 7 of the Eastern Conference Finals was one of the most stressful games I've ever watched as a Penguins fan. It took the Penguins two heart-palpitation-inducing overtime periods to score a goal by Chris Kunitz earning them a trip to the Stanley Cup Finals for the second year in a row. No team has won back to back Stanley Cups in the salary cap era, and the Penguins stand a chance to be the first when they faceoff against the Nashville Predators. The last team to do it was the Detroit Red Wings in 97-98. Before that, the Pittsburgh Penguins in 91-92. 

I live streamed tweets before and during the game, amassing a total of 15,208 tweets (64MB) with keywords: 'sidney crosby', 'pittsburgh penguins', 'ottawa senators', 'nhl playoffs', and 'stanley cup.'

<img src="./assets/images/ECF_game7_stream_hist.png" alt="tweet histogram" align="right" width="600" height="400" hspace="25" vspace="25"/>

The streaming began about an hour before the first faceoff and from the histogram of tweets-per-minute, we can see that tweeting begins to pick up about 20 or 30 minutes before gametime and remains fairly constant for most of the game. It's pretty clear that towards the end of the game tweet frequency increases. I used a Markov Chain Monte Carlo (MCMC) simulation to determine at what time the frequency changes, and what the frequencies are before and after that time. Given the data and using a Poisson model for counting the number of tweets-per-minute, the MCMC simulation samples the three parameters $$ \lambda_1 $$, $$ \lambda_2 $$, and $$ \tau $$  which are the initial tweeting rate, the final tweeting rate, and the time at which they change respectively. The posterior distributions for these parameters are shown in the plot below.

<img src="./assets/images/ECF_game7_stream_poisson_stats.png" alt="tweet histogram" align="right" width="600" height="600" hspace="25" vspace="25"/>

I took the most likely values of each of these parameters to plot in the first figure. Each of the $$ \lambda_i $$ frequencies is plotted in green, and the break takes place at the most probable value of $$ \tau $$. The MCMC simulation looks to be confident that the frequency of tweeting changed at $$ t = 196 $$ minutes. This is right when the Penguins scored in the third period to make it 2-1, where it seemed that the Pens would be able to shut the game down and get a regulation victory. Ottawa came back a few minutes later to tie the game and send it into overtime. We can see the spikes in tweets that occurred right after each of these goals. There were also huge spikes during the intermissions that followed. After the third period is where we see the biggest spike, other than after the gamewinner in 2OT.

I also looked at the sentiments of tweets using the python textblob package that's built on top of the nltk package. In the first plot, both positive tweets and total tweets are shown. TextBlob also classifies some tweets to be neutral, and so the difference between total and positive tweets is not just negative tweets. I wanted to look at some of the most negative and positive tweets after the 3rd period and before the first OT since there was so much activity, and probably nerves as well. Apparently there was a lot of angst towards the NBA, and possibly Taylor Swift.

## [](#header-2)Negative Tweets

*   "RT @ArdaOcalTV: The Ottawa Senators would make the worst pro wrestler: they no sell everything, kick out of every finish.... #StanleyCup"
*   "NHL playoffs is so insane!"
*   "@RealMikeWilbon NBA playoffs have been boring. NHL is where the excitement always is."
*   "RT @SeanPooley: Anyone watching yet another BORING 30 point blowout in the NBA Playoffs need to switch and watch #game7 OT in the @NHL Play…"
*   "NBA playoffs are absolutely boring compared to the NHL playoffs"
*   "OT in Game 7 to go to the Stanley Cup Championship. I'm going to have a heart attack.... #LETSGOPENS!!!!!"
*   "@TAFFA_44 You know what else we hate? The penguins, and Sidney Crosby"
*   "Screw politics, game 7 NHL playoffs!!! https://t.co/72VwRYWTUg"
*   "Haters gonna hate https://t.co/CHLpxnanZn"
*   "@domluszczyszyn crazy how the stanley cup finals will turn out, the team your model hated on vs the team that could… https://t.co/sAKQp2NrnI"

## [](#header-2)Positive Tweets

*   "The NHL playoffs are the best.  The best part of them? Games 7. The best of them? OT!  @NBCSN @Senators vs @penguins #StanleyCup"
*   "The best moment in sports: Gm 7 Stanley Cup playoffs. Let's go Pens!!!!"
*   "@Buccigross THE GREATEST PLAYER IN THE WORLD SIDNEY CROSBY #bucciovertimechallenge"
*   "The Taylor Swift Stanley Cup promo is the best of all time."
*   "NHL playoffs used to have the best commercials.  Now they use Taylor Swift."
*   "@helenenothelen @penguins @Senators Another example why Stanley Cup Playoff hockey is just the best!"
*   "@Notorious_RJG I've always said the NHL is the best playoffs. Problem is they're shitty at marketing their players… https://t.co/QlyiSQG6ly"
*   "@HeydtScott this NHL Playoffs has been excellent viewing #GoSmashville"
*   "Games like this are why the @NHL playoffs are the best thing in sports. #Game7"
*   "Oh #Ottawa we stay awake for you! Win it in overtime! Go @Senators"
*   "Magnificent series between @Senators and @penguins, no matter the end! But Ottawa, let's bring this thing home! @NHL #StanleyCup #OTTvsPIT"
