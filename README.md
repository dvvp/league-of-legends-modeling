# Predicting Wins and Losses (League of Legends Modeling)
Created by Devin Pham and Minh Vo

Our exploratory data analysis on this dataset can be found [here](https://dvvp.github.io/league-of-legends-eda/).

---

### Framing the Problem

In our [exploratory data analysis](https://dvvp.github.io/league-of-legends-eda/) done previously, we found that killing more dragons in [League of Legends](https://en.wikipedia.org/wiki/League_of_Legends) correlated to higher team kills. This time, we will be answering a binary classification problem, and using match wins as our response variable. 

Question we plan on investigating:

- How can we predict wins and losses for League of Legends matches?

We chose this response variable because it is the most important when it comes to playing in a League of Legends tournament. The main goal for playing in a League of Legends tournament is to win which means we must discover how to predict League of Legends match outcomes.

Since the classes are balanced (i.e., predicting a win and predicting a loss is of equal importance), we will choose accuracy as our evaluation metric.

At the time of prediction (before the winner is announced), we should be able to have data for both MOB and player kills, given that this data is being actively collected. This includes whether a team achieved an objective kill or simply the number of kills of each respective MOB and player. Because of this, we will be using a combination of these features in our model.

We will use our cleaned ```teams``` DataFrame retrieved from our EDA to build our model. Here are the first 5 rows from that dataset:

|                               | datacompleteness   |   url | league   |   year | split   | playoffs   | date                |   game |   patch |   participantid | side   | position   | teamname                      | teamid                                  | ban1    | ban2         | ban3         | ban4     | ban5    |   gamelength | result   |   kills |   deaths |   assists |   teamkills |   teamdeaths |   doublekills |   triplekills |   quadrakills |   pentakills |   firstblood |   team kpm |   ckpm |   firstdragon |   dragons |   opp_dragons |   elementaldrakes |   opp_elementaldrakes |   infernals |   mountains |   clouds |   oceans |   chemtechs |   hextechs |   dragons (type unknown) |   elders |   opp_elders |   firstherald |   heralds |   opp_heralds |   firstbaron |   barons |   opp_barons |   firsttower |   towers |   opp_towers |   firstmidtower |   firsttothreetowers |   turretplates |   opp_turretplates |   inhibitors |   opp_inhibitors |   damagetochampions |     dpm |   damagetakenperminute |   damagemitigatedperminute |   wardsplaced |    wpm |   wardskilled |   wcpm |   controlwardsbought |   visionscore |   vspm |   totalgold |   earnedgold |   earned gpm |   goldspent |       gspd |   minionkills |   monsterkills |   monsterkillsownjungle |   monsterkillsenemyjungle |    cspm |   goldat10 |   xpat10 |   csat10 |   opp_goldat10 |   opp_xpat10 |   opp_csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |   killsat10 |   assistsat10 |   deathsat10 |   opp_killsat10 |   opp_assistsat10 |   opp_deathsat10 |   goldat15 |   xpat15 |   csat15 |   opp_goldat15 |   opp_xpat15 |   opp_csat15 |   golddiffat15 |   xpdiffat15 |   csdiffat15 |   killsat15 |   assistsat15 |   deathsat15 |   opp_killsat15 |   opp_assistsat15 |   opp_deathsat15 |
|:------------------------------|:-------------------|------:|:---------|-------:|:--------|:-----------|:--------------------|-------:|--------:|----------------:|:-------|:-----------|:------------------------------|:----------------------------------------|:--------|:-------------|:-------------|:---------|:--------|-------------:|:---------|--------:|---------:|----------:|------------:|-------------:|--------------:|--------------:|--------------:|-------------:|-------------:|-----------:|-------:|--------------:|----------:|--------------:|------------------:|----------------------:|------------:|------------:|---------:|---------:|------------:|-----------:|-------------------------:|---------:|-------------:|--------------:|----------:|--------------:|-------------:|---------:|-------------:|-------------:|---------:|-------------:|----------------:|---------------------:|---------------:|-------------------:|-------------:|-----------------:|--------------------:|--------:|-----------------------:|---------------------------:|--------------:|-------:|--------------:|-------:|---------------------:|--------------:|-------:|------------:|-------------:|-------------:|------------:|-----------:|--------------:|---------------:|------------------------:|--------------------------:|--------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------------:|-------------:|-------------:|------------:|--------------:|-------------:|----------------:|------------------:|-----------------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------------:|-------------:|-------------:|------------:|--------------:|-------------:|----------------:|------------------:|-----------------:|
| ('ESPORTSTMNT01_2690210', 10) | complete           |   nan | LCK CL   |   2022 | Spring  | False      | 2022-01-10 07:44:08 |      1 |   12.01 |             100 | Blue   | team       | Fredit BRION Challengers      | oe:team:68911b3329146587617ab2973106e23 | Karma   | Caitlyn      | Syndra       | Thresh   | Lulu    |         1713 | False    |       9 |       19 |        19 |           9 |           19 |             0 |             0 |             0 |            0 |            1 |     0.3152 | 0.9807 |             0 |         1 |             3 |                 1 |                     3 |           0 |           0 |        0 |        0 |           0 |          1 |                      nan |        0 |            0 |             1 |         2 |             0 |            0 |        0 |            0 |            1 |        3 |            6 |               1 |                    1 |              5 |                  0 |            0 |                1 |               56560 | 1981.09 |                3537.2  |                    2364.73 |            74 | 2.5919 |            51 | 1.7863 |                   33 |           197 | 6.9002 |       47070 |        28222 |      988.511 |       44570 | -0.0283123 |           680 |            160 |                     nan |                       nan | 29.4221 |      16218 |    18213 |      322 |          14695 |        18076 |          330 |           1523 |          137 |           -8 |           3 |             5 |            0 |               0 |                 0 |                3 |      24806 |    28001 |      487 |          24699 |        29618 |          510 |            107 |        -1617 |          -23 |           5 |            10 |            6 |               6 |                18 |                5 |
| ('ESPORTSTMNT01_2690210', 11) | complete           |   nan | LCK CL   |   2022 | Spring  | False      | 2022-01-10 07:44:08 |      1 |   12.01 |             200 | Red    | team       | Nongshim RedForce Challengers | oe:team:d2dc3681437e2beb2bb4742477108ff | Lee Sin | Twisted Fate | Zoe          | Nautilus | Rell    |         1713 | True     |      19 |        9 |        62 |          19 |            9 |             6 |             0 |             0 |            0 |            0 |     0.6655 | 0.9807 |             1 |         3 |             1 |                 3 |                     1 |           2 |           1 |        0 |        0 |           0 |          0 |                      nan |        0 |            0 |             0 |         0 |             2 |            0 |        0 |            0 |            0 |        6 |            3 |               0 |                    0 |              0 |                  5 |            1 |                0 |               79912 | 2799.02 |                3009.67 |                    2872.33 |            93 | 3.2574 |            51 | 1.7863 |                   45 |           205 | 7.1804 |       52617 |        33769 |     1182.8   |       45850 |  0.0283123 |           792 |            184 |                     nan |                       nan | 34.1856 |      14695 |    18076 |      330 |          16218 |        18213 |          322 |          -1523 |         -137 |            8 |           0 |             0 |            3 |               3 |                 5 |                0 |      24699 |    29618 |      510 |          24806 |        28001 |          487 |           -107 |         1617 |           23 |           6 |            18 |            5 |               5 |                10 |                6 |
| ('ESPORTSTMNT01_2690219', 22) | complete           |   nan | LCK CL   |   2022 | Spring  | False      | 2022-01-10 08:38:24 |      1 |   12.01 |             100 | Blue   | team       | T1 Challengers                | oe:team:6dcacec00a6ba7576c5ab7f30c995cd | Sona    | Jarvan IV    | Caitlyn      | Lulu     | Lucian  |         2114 | False    |       3 |       16 |         7 |           3 |           16 |             0 |             0 |             0 |            0 |            0 |     0.0851 | 0.5393 |             0 |         1 |             4 |                 1 |                     4 |           0 |           1 |        0 |        0 |           0 |          0 |                      nan |        0 |            0 |             1 |         1 |             1 |            0 |        0 |            2 |            0 |        3 |           11 |               0 |                    0 |              2 |                  3 |            0 |                2 |               59579 | 1690.98 |                2984.02 |                    3109.61 |           119 | 3.3775 |            55 | 1.561  |                   47 |           277 | 7.8619 |       57629 |        34688 |      984.522 |       53945 | -0.207137  |           994 |            215 |                     nan |                       nan | 34.3141 |      14939 |    17462 |      317 |          16558 |        19048 |          344 |          -1619 |        -1586 |          -27 |           1 |             1 |            3 |               3 |                 3 |                1 |      23522 |    28848 |      533 |          25285 |        29754 |          555 |          -1763 |         -906 |          -22 |           1 |             1 |            3 |               3 |                 3 |                1 |
| ('ESPORTSTMNT01_2690219', 23) | complete           |   nan | LCK CL   |   2022 | Spring  | False      | 2022-01-10 08:38:24 |      1 |   12.01 |             200 | Red    | team       | Liiv SANDBOX Challengers      | oe:team:5380cdbc2ad2b8082624f48f99f6672 | LeBlanc | Yuumi        | Twisted Fate | Karma    | Alistar |         2114 | True     |      16 |        3 |        39 |          16 |            3 |             1 |             0 |             0 |            0 |            1 |     0.4541 | 0.5393 |             1 |         4 |             1 |                 4 |                     1 |           0 |           2 |        1 |        0 |           0 |          1 |                      nan |        0 |            0 |             0 |         1 |             1 |            1 |        2 |            0 |            1 |       11 |            3 |               1 |                    1 |              3 |                  2 |            2 |                0 |               74855 | 2124.55 |                2745.72 |                    2868.42 |           129 | 3.6613 |            70 | 1.9868 |                   65 |           346 | 9.8202 |       71004 |        48063 |     1364.13  |       66410 |  0.207137  |          1013 |            244 |                     nan |                       nan | 35.6764 |      16558 |    19048 |      344 |          14939 |        17462 |          317 |           1619 |         1586 |           27 |           3 |             3 |            1 |               1 |                 1 |                3 |      25285 |    29754 |      555 |          23522 |        28848 |          533 |           1763 |          906 |           22 |           3 |             3 |            1 |               1 |                 1 |                3 |
| ('ESPORTSTMNT01_2690227', 34) | complete           |   nan | LCK CL   |   2022 | Spring  | False      | 2022-01-10 09:51:16 |      1 |   12.01 |             100 | Blue   | team       | KT Rolster Challengers        | oe:team:b9733b8e8aa341319bbaf1035198a28 | Syndra  | Caitlyn      | Karma        | Gragas   | Vex     |         1972 | True     |      14 |        5 |        42 |          14 |            5 |             3 |             1 |             0 |            0 |            0 |     0.426  | 0.5781 |             1 |         4 |             1 |                 4 |                     1 |           0 |           1 |        0 |        1 |           0 |          2 |                      nan |        0 |            0 |             0 |         1 |             1 |            1 |        1 |            0 |            1 |       11 |            2 |               1 |                    1 |              1 |                  4 |            2 |                0 |               67376 | 2049.98 |                2327.89 |                    1776.27 |           119 | 3.6207 |            51 | 1.5517 |                   68 |           264 | 8.0325 |       62868 |        41372 |     1258.78  |       57615 |  0.165672  |           874 |            269 |                     nan |                       nan | 34.7769 |      15466 |    19600 |      368 |          15569 |        18787 |          355 |           -103 |          813 |           13 |           0 |             0 |            1 |               1 |                 1 |                0 |      24795 |    31342 |      560 |          23604 |        29044 |          545 |           1191 |         2298 |           15 |           3 |             8 |            1 |               1 |                 1 |                3 |

### Baseline Model

For our baseline model, we decided to include number of baron and dragons kills. Because killing barons and dragons give you extra buffs in the game, we hypothesized that these buffs will help players become stronger which will give them advantage over the opposing team. 

For our model, we removed all unecessary features by getting a subset of the data with columns ```dragons```, ```barons```, and ```results```.

We then decided to binarize our two features since the range of these two features were so small. The distribution of each feature is shown below:

<iframe src="assets/dragons-dist.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/barons-dist.html" width=800 height=600 frameBorder=0></iframe>

Looking at the two distributions, we can observe that the distribution of dragon kills begin to dip with 3 or more dragon kills while team baron kills begin to dip with 2 or more baron kills. Thus, we decided to binarize ```dragons``` with a threshold of 2 and ```barons``` with a threshold of 1. 

Fitting and testing this model, we got an accuracy of ~77% on the training data and ~76% on the testing data. These results lead us to believe that our current model is good since it has a relatively high testing accuracy, and the testing accuracy is not alarmingly lower than the training accuracy.

### Final Models

In an attempt to create a better model, we decided to also incorporate objective kills ```firstdragon``` and ```firstbaron```. We chose these two features because they are closely related to the features we chose in our baseline model. We will not need to transform these values since they are already binary encoded.

To include these new features, we trained our model using a bigger subset of our data with columns ```dragons```, ```barons```, ```firstdragon```, ```firstbaron```, and ```result```.

Fitting and testing this model, we got an accuracy of ~84% on the training data and ~84% on the testing data which is a vast improvement. It does seem like mob kills does seem to influence win rate.

To try and improve our model even more, we will now also incorporate non-mob related features to see if our model  can perform any better. We will now include ```kills``` and ```assists``` into our data. Having more kills in the game could potentially mean your team is doing well in the game, and having more assists could potentially mean your team is effectively working together. We chose to use a standard scaler transformer to transform our data so that it fits a normal distribution.

To include these new features, we trained our model using an even bigger subset of our data with columns ```dragons```, ```barons```, ```firstdragon```, ```firstbaron```, ```kills```, ```assists```, ```result```.

Fitting and testing this model, we got an accuracy of ~93% on the training data and ~85% on the testing data. We have improved our testing data by just a little bit, but it seems that we now overfitted to our training data.

In an attempt to resolve this, we can try to optimize our classifier's hyperparameters using grid search cross-validation. 

Instantiating and fitting our grid search cross-validation object using a decision tree classifier object, we found that the best hyperparameters to use were ```max_depth```=7, ```max_leaf_nodes```=None, and ```min_samples_split```=10. 

Creating a new pipeline with these new hyperparameters, we got an accuracy of ~89% on the training data and ~88% on the testing data. Compared to baseline mode's performance, this is a vast improvement as we improved testing accuracy by roughly 12%.

### Fairness Analysis


