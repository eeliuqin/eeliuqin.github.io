---
title: "Analyzing Strategy Games on Apple App Store"
header:
  image: /assets/images/games-ipad.png
  caption: "Photo credit: [**Pixabay**](https://www.pexels.com/photo/apple-office-internet-ipad-38544/)"
permalink: /portfolio/mobile-strategy-games/
toc: true
toc_label: "Contents"
---

<a id="introduction"></a>
## 1. Introduction

A fictional company called Gamemagician decides to create an app that focus on **Strategy Games**. We are hired as data analysts to do an exploratory data analysis, to help them get familiar with the current market and provide some suggestions.

<a id="ask"></a>
## 2. Ask

<a id="business_task"></a>
### 2.1 The business task

As data analysts, we need to analyze the strategy games based on the following sections:
- Classifications
- Price
- User Ratings
- Competitors

Finally, we will provide recommendations about what kind of strategy games Gamemagician should create. Our report should be backed up with compelling data insights and professional data visualizations.

<a id="key_stakeholder"></a>
### 2.2 Key stakeholders

- **The analytics team we are working for**: A team of data analysts who are responsible for collecting, analyzing, and reporting data that helps guide Gamemagician marketing strategy.
- **Gamemagician executive team**: The executive team of our client, Gamemagician. They are detail-oriented and will decide whether to approve our recommendations.


<a id="prepare"></a>
## 3. Prepare
<a id="data_used"></a>
### 3.1 Data used

We're going to use the [17K Mobile Strategy Games dataset](https://www.kaggle.com/datasets/tristan581/17k-apple-app-store-strategy-games?select=appstore_games.csv) found in Kaggle. It's the data of 17007 strategy games on the Apple App Store, collected on Aug 03, 2019, using the iTunes API and the [App Store Sitemap](https://apps.apple.com/us/genre/ios-games/id6014).

<a id="importing"></a>
### 3.2 Importing the data

We need to download and import the csv file `appstore_games.csv` from the kaggle link.


```python
# import required libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

%matplotlib inline

# read the csv
games = pd.read_csv("appstore_games.csv")

# check first 5 rows
games.head()
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
      <th>URL</th>
      <th>ID</th>
      <th>Name</th>
      <th>Subtitle</th>
      <th>Icon URL</th>
      <th>Average User Rating</th>
      <th>User Rating Count</th>
      <th>Price</th>
      <th>In-app Purchases</th>
      <th>Description</th>
      <th>Developer</th>
      <th>Age Rating</th>
      <th>Languages</th>
      <th>Size</th>
      <th>Primary Genre</th>
      <th>Genres</th>
      <th>Original Release Date</th>
      <th>Current Version Release Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>https://apps.apple.com/us/app/sudoku/id284921427</td>
      <td>284921427</td>
      <td>Sudoku</td>
      <td>NaN</td>
      <td>https://is2-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>4.0</td>
      <td>3553.0</td>
      <td>2.99</td>
      <td>NaN</td>
      <td>Join over 21,000,000 of our fans and download ...</td>
      <td>Mighty Mighty Good Games</td>
      <td>4+</td>
      <td>DA, NL, EN, FI, FR, DE, IT, JA, KO, NB, PL, PT...</td>
      <td>15853568.0</td>
      <td>Games</td>
      <td>Games, Strategy, Puzzle</td>
      <td>11/07/2008</td>
      <td>30/05/2017</td>
    </tr>
    <tr>
      <th>1</th>
      <td>https://apps.apple.com/us/app/reversi/id284926400</td>
      <td>284926400</td>
      <td>Reversi</td>
      <td>NaN</td>
      <td>https://is4-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>3.5</td>
      <td>284.0</td>
      <td>1.99</td>
      <td>NaN</td>
      <td>The classic game of Reversi, also known as Oth...</td>
      <td>Kiss The Machine</td>
      <td>4+</td>
      <td>EN</td>
      <td>12328960.0</td>
      <td>Games</td>
      <td>Games, Strategy, Board</td>
      <td>11/07/2008</td>
      <td>17/05/2018</td>
    </tr>
    <tr>
      <th>2</th>
      <td>https://apps.apple.com/us/app/morocco/id284946595</td>
      <td>284946595</td>
      <td>Morocco</td>
      <td>NaN</td>
      <td>https://is5-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>3.0</td>
      <td>8376.0</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>Play the classic strategy game Othello (also k...</td>
      <td>Bayou Games</td>
      <td>4+</td>
      <td>EN</td>
      <td>674816.0</td>
      <td>Games</td>
      <td>Games, Board, Strategy</td>
      <td>11/07/2008</td>
      <td>5/09/2017</td>
    </tr>
    <tr>
      <th>3</th>
      <td>https://apps.apple.com/us/app/sudoku-free/id28...</td>
      <td>285755462</td>
      <td>Sudoku (Free)</td>
      <td>NaN</td>
      <td>https://is3-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>3.5</td>
      <td>190394.0</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>Top 100 free app for over a year.\nRated "Best...</td>
      <td>Mighty Mighty Good Games</td>
      <td>4+</td>
      <td>DA, NL, EN, FI, FR, DE, IT, JA, KO, NB, PL, PT...</td>
      <td>21552128.0</td>
      <td>Games</td>
      <td>Games, Strategy, Puzzle</td>
      <td>23/07/2008</td>
      <td>30/05/2017</td>
    </tr>
    <tr>
      <th>4</th>
      <td>https://apps.apple.com/us/app/senet-deluxe/id2...</td>
      <td>285831220</td>
      <td>Senet Deluxe</td>
      <td>NaN</td>
      <td>https://is1-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>3.5</td>
      <td>28.0</td>
      <td>2.99</td>
      <td>NaN</td>
      <td>"Senet Deluxe - The Ancient Game of Life and A...</td>
      <td>RoGame Software</td>
      <td>4+</td>
      <td>DA, NL, EN, FR, DE, EL, IT, JA, KO, NO, PT, RU...</td>
      <td>34689024.0</td>
      <td>Games</td>
      <td>Games, Strategy, Board, Education</td>
      <td>18/07/2008</td>
      <td>22/07/2018</td>
    </tr>
  </tbody>
</table>
</div>



<a id="exploring"></a>
### 3.3 Exploring the data

#### Data size


```python
# data size
games.shape
```




    (17007, 18)



#### Find duplicates


```python
games[games.duplicated(keep=False)]
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
      <th>URL</th>
      <th>ID</th>
      <th>Name</th>
      <th>Subtitle</th>
      <th>Icon URL</th>
      <th>Average User Rating</th>
      <th>User Rating Count</th>
      <th>Price</th>
      <th>In-app Purchases</th>
      <th>Description</th>
      <th>Developer</th>
      <th>Age Rating</th>
      <th>Languages</th>
      <th>Size</th>
      <th>Primary Genre</th>
      <th>Genres</th>
      <th>Original Release Date</th>
      <th>Current Version Release Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>15</th>
      <td>https://apps.apple.com/us/app/awele-oware-manc...</td>
      <td>289217958</td>
      <td>Awele/Oware - Mancala HD</td>
      <td>NaN</td>
      <td>https://is3-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>3.0</td>
      <td>112.0</td>
      <td>0.00</td>
      <td>0.99</td>
      <td>Awele/Oware is the oldest African board game a...</td>
      <td>SOLILAB</td>
      <td>4+</td>
      <td>EN, FR, DE, IT, ES</td>
      <td>122826752.0</td>
      <td>Games</td>
      <td>Games, Strategy, Board</td>
      <td>31/08/2008</td>
      <td>6/04/2015</td>
    </tr>
    <tr>
      <th>16</th>
      <td>https://apps.apple.com/us/app/awele-oware-manc...</td>
      <td>289217958</td>
      <td>Awele/Oware - Mancala HD</td>
      <td>NaN</td>
      <td>https://is3-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>3.0</td>
      <td>112.0</td>
      <td>0.00</td>
      <td>0.99</td>
      <td>Awele/Oware is the oldest African board game a...</td>
      <td>SOLILAB</td>
      <td>4+</td>
      <td>EN, FR, DE, IT, ES</td>
      <td>122826752.0</td>
      <td>Games</td>
      <td>Games, Strategy, Board</td>
      <td>31/08/2008</td>
      <td>6/04/2015</td>
    </tr>
    <tr>
      <th>56</th>
      <td>https://apps.apple.com/us/app/shogi-kifu/id302...</td>
      <td>302532668</td>
      <td>Shogi Kifu</td>
      <td>NaN</td>
      <td>https://is2-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3.99</td>
      <td>NaN</td>
      <td>This application is to record Shogi (Japanese ...</td>
      <td>Yoshikazu Kakinoki</td>
      <td>4+</td>
      <td>EN, JA</td>
      <td>29797376.0</td>
      <td>Games</td>
      <td>Games, Strategy, Board</td>
      <td>14/07/2011</td>
      <td>13/10/2018</td>
    </tr>
    <tr>
      <th>57</th>
      <td>https://apps.apple.com/us/app/shogi-kifu/id302...</td>
      <td>302532668</td>
      <td>Shogi Kifu</td>
      <td>NaN</td>
      <td>https://is2-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3.99</td>
      <td>NaN</td>
      <td>This application is to record Shogi (Japanese ...</td>
      <td>Yoshikazu Kakinoki</td>
      <td>4+</td>
      <td>EN, JA</td>
      <td>29797376.0</td>
      <td>Games</td>
      <td>Games, Strategy, Board</td>
      <td>14/07/2011</td>
      <td>13/10/2018</td>
    </tr>
    <tr>
      <th>123</th>
      <td>https://apps.apple.com/us/app/checkers/id32102...</td>
      <td>321026028</td>
      <td>Checkers</td>
      <td>Checkers (Draughts) &amp; puzzles.</td>
      <td>https://is3-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>4.5</td>
      <td>36581.0</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>Checkers (also called "Draughts") is challengi...</td>
      <td>Vintolo Ltd</td>
      <td>4+</td>
      <td>EN</td>
      <td>69393408.0</td>
      <td>Games</td>
      <td>Games, Entertainment, Board, Strategy</td>
      <td>4/07/2009</td>
      <td>14/06/2019</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>16513</th>
      <td>https://apps.apple.com/us/app/fire-boy-water-g...</td>
      <td>1460730256</td>
      <td>Fire Boy - Water Girl</td>
      <td>Run, jump &amp; hop on platforms</td>
      <td>https://is4-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>Start your island world adventure with the cra...</td>
      <td>GUENNOUNI Othmane</td>
      <td>4+</td>
      <td>EN</td>
      <td>171115520.0</td>
      <td>Games</td>
      <td>Games, Adventure, Strategy, Entertainment</td>
      <td>26/04/2019</td>
      <td>29/06/2019</td>
    </tr>
    <tr>
      <th>16560</th>
      <td>https://apps.apple.com/us/app/kiloton/id146259...</td>
      <td>1462595486</td>
      <td>Kiloton</td>
      <td>Intelligence Learning Lab</td>
      <td>https://is4-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>"KILOTON is a logic puzzle game, in the vein o...</td>
      <td>V. Kevin Russ</td>
      <td>4+</td>
      <td>EN</td>
      <td>80356352.0</td>
      <td>Games</td>
      <td>Games, Strategy, Puzzle</td>
      <td>10/05/2019</td>
      <td>20/05/2019</td>
    </tr>
    <tr>
      <th>16561</th>
      <td>https://apps.apple.com/us/app/kiloton/id146259...</td>
      <td>1462595486</td>
      <td>Kiloton</td>
      <td>Intelligence Learning Lab</td>
      <td>https://is4-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>"KILOTON is a logic puzzle game, in the vein o...</td>
      <td>V. Kevin Russ</td>
      <td>4+</td>
      <td>EN</td>
      <td>80356352.0</td>
      <td>Games</td>
      <td>Games, Strategy, Puzzle</td>
      <td>10/05/2019</td>
      <td>20/05/2019</td>
    </tr>
    <tr>
      <th>16564</th>
      <td>https://apps.apple.com/us/app/idle-bomber-idle...</td>
      <td>1462678041</td>
      <td>Idle Bomber - Idle &amp; Clicker</td>
      <td>Boom Boom Boom</td>
      <td>https://is3-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>1.99</td>
      <td>Boom! Boom! Boom!\n\nIdle Bomber - Idle &amp; Clic...</td>
      <td>Rafael Sian de Freitas</td>
      <td>4+</td>
      <td>EN</td>
      <td>113328128.0</td>
      <td>Games</td>
      <td>Games, Strategy, Casual</td>
      <td>8/05/2019</td>
      <td>15/06/2019</td>
    </tr>
    <tr>
      <th>16565</th>
      <td>https://apps.apple.com/us/app/idle-bomber-idle...</td>
      <td>1462678041</td>
      <td>Idle Bomber - Idle &amp; Clicker</td>
      <td>Boom Boom Boom</td>
      <td>https://is3-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>1.99</td>
      <td>Boom! Boom! Boom!\n\nIdle Bomber - Idle &amp; Clic...</td>
      <td>Rafael Sian de Freitas</td>
      <td>4+</td>
      <td>EN</td>
      <td>113328128.0</td>
      <td>Games</td>
      <td>Games, Strategy, Casual</td>
      <td>8/05/2019</td>
      <td>15/06/2019</td>
    </tr>
  </tbody>
</table>
<p>320 rows × 18 columns</p>
</div>



Duplicates exist, we need to remove them later.

#### Check missing values


```python
# get the type and count of missing values of each column 
games.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 17007 entries, 0 to 17006
    Data columns (total 18 columns):
     #   Column                        Non-Null Count  Dtype  
    ---  ------                        --------------  -----  
     0   URL                           17007 non-null  object 
     1   ID                            17007 non-null  int64  
     2   Name                          17007 non-null  object 
     3   Subtitle                      5261 non-null   object 
     4   Icon URL                      17007 non-null  object 
     5   Average User Rating           7561 non-null   float64
     6   User Rating Count             7561 non-null   float64
     7   Price                         16983 non-null  float64
     8   In-app Purchases              7683 non-null   object 
     9   Description                   17007 non-null  object 
     10  Developer                     17007 non-null  object 
     11  Age Rating                    17007 non-null  object 
     12  Languages                     16947 non-null  object 
     13  Size                          17006 non-null  float64
     14  Primary Genre                 17007 non-null  object 
     15  Genres                        17007 non-null  object 
     16  Original Release Date         17007 non-null  object 
     17  Current Version Release Date  17007 non-null  object 
    dtypes: float64(4), int64(1), object(13)
    memory usage: 2.3+ MB


What we can learn:

- There are some columns with plenty of missing values. Among them, we don't care about `URL` and `Icon URL`. `ID` and `Subtitle` is not important since we've known the name and genres of the game. We can drop those columns later.

- Rating related columns like `Average User Rating` and `User Rating Count` are critical, we will remove those rows without ratings.

- Given such large value of `Size` and randomly checking some apps on app store, we can know the unit of `Size` should be bytes. To make it more readable, we will need to convert it to MB.

- `Primary Genre` of the first 5 rows are all "Games", but we need to take care if it's not.

- `Genres` consist of multiple genre string, it contains the primary genre "Game", but other than "Strategy", it also contains genres like "Board", "Puzzle", etc., we need to deal with that later.

- `Original Release Date` and `Current Version Release Date` are objects, but what types of objects? String or datetime?

First of all, let's check the unique values of `Primary Genre`


```python
games['Primary Genre'].unique()
```




    array(['Games', 'Entertainment', 'Finance', 'Sports', 'Reference',
           'Medical', 'Education', 'Utilities', 'Book', 'Travel',
           'Productivity', 'Lifestyle', 'Business', 'News',
           'Social Networking', 'Health & Fitness', 'Music', 'Stickers',
           'Food & Drink', 'Shopping', 'Navigation'], dtype=object)



Some apps are not "Games", or at least not pure games. What are they?


```python
# randomly check 10 apps that are not games
games[games['Primary Genre'] != "Games"].sample(5, random_state=1)
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
      <th>URL</th>
      <th>ID</th>
      <th>Name</th>
      <th>Subtitle</th>
      <th>Icon URL</th>
      <th>Average User Rating</th>
      <th>User Rating Count</th>
      <th>Price</th>
      <th>In-app Purchases</th>
      <th>Description</th>
      <th>Developer</th>
      <th>Age Rating</th>
      <th>Languages</th>
      <th>Size</th>
      <th>Primary Genre</th>
      <th>Genres</th>
      <th>Original Release Date</th>
      <th>Current Version Release Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1213</th>
      <td>https://apps.apple.com/us/app/rock-paper-sciss...</td>
      <td>509936274</td>
      <td>Rock, Paper, Scissors!!!</td>
      <td>NaN</td>
      <td>https://is4-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>The game we know today as "Rock, Paper, Scisso...</td>
      <td>Limpossible Technologies</td>
      <td>4+</td>
      <td>EN, ES</td>
      <td>1617130.0</td>
      <td>Entertainment</td>
      <td>Entertainment, Simulation, Strategy, Games</td>
      <td>19/03/2012</td>
      <td>29/06/2012</td>
    </tr>
    <tr>
      <th>1234</th>
      <td>https://apps.apple.com/us/app/hangman-en/id511...</td>
      <td>511938214</td>
      <td>Hangman EN</td>
      <td>NaN</td>
      <td>https://is2-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>1.99</td>
      <td>Hangman - Search the word!\n\nHangman - The cl...</td>
      <td>Dominik Walleser</td>
      <td>4+</td>
      <td>CS, NL, EN, FR, DE, IT, JA, KO, PL, PT, RU, ZH...</td>
      <td>38514688.0</td>
      <td>Entertainment</td>
      <td>Entertainment, Word, Games, Strategy</td>
      <td>26/03/2012</td>
      <td>23/07/2015</td>
    </tr>
    <tr>
      <th>5236</th>
      <td>https://apps.apple.com/us/app/hacker-tanks/id9...</td>
      <td>955093715</td>
      <td>Hacker Tanks</td>
      <td>NaN</td>
      <td>https://is4-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>0.99, 2.99, 7.99, 29.99, 49.99, 99.99, 0.99, 4...</td>
      <td>Release Your Hacker Skills!!\n\nHave you ever ...</td>
      <td>Koichi Ishida</td>
      <td>4+</td>
      <td>EN</td>
      <td>59027456.0</td>
      <td>Education</td>
      <td>Education, Strategy, Games</td>
      <td>25/01/2015</td>
      <td>25/01/2015</td>
    </tr>
    <tr>
      <th>15599</th>
      <td>https://apps.apple.com/us/app/letscode/id14437...</td>
      <td>1443741838</td>
      <td>LetsCode</td>
      <td>Strengthen your child\u2019s logic</td>
      <td>https://is1-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9.99</td>
      <td>NaN</td>
      <td>Let\u2019s Code is the newest fun game which w...</td>
      <td>Anurag Rastogi</td>
      <td>4+</td>
      <td>AR, HY, CA, CS, DA, NL, EN, FI, FR, DE, EL, HE...</td>
      <td>139212800.0</td>
      <td>Education</td>
      <td>Education, Games, Strategy, Puzzle</td>
      <td>31/12/2018</td>
      <td>31/12/2018</td>
    </tr>
    <tr>
      <th>201</th>
      <td>https://apps.apple.com/us/app/pok%C3%A9-dexter...</td>
      <td>339451567</td>
      <td>Pok\xe9 Dexter - Generation I to VI</td>
      <td>NaN</td>
      <td>https://is1-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>3.0</td>
      <td>1471.0</td>
      <td>0.99</td>
      <td>NaN</td>
      <td>Novices and experts Pok\xe9mon, this new Pok\x...</td>
      <td>iVenus</td>
      <td>4+</td>
      <td>EN, FR</td>
      <td>331237376.0</td>
      <td>Reference</td>
      <td>Reference, Games, Strategy, Adventure</td>
      <td>25/11/2009</td>
      <td>21/09/2015</td>
    </tr>
  </tbody>
</table>
</div>



Those apps' primary goals are not being game apps, eventhough they can play like games. Therefore, we will remove those apps from our dataframe.

Next, let's take a closer look at `Genres`.


```python
# get all unique genres
genres = games['Genres'].str.split(',', expand=True).stack().str.strip()
genres.unique()
```




    array(['Games', 'Strategy', 'Puzzle', 'Board', 'Education',
           'Entertainment', 'Casual', 'Action', 'Card', 'Simulation',
           'Finance', 'Word', 'Role Playing', 'Sports', 'Adventure', 'Family',
           'Travel', 'Casino', 'Business', 'Navigation', 'Reference',
           'Lifestyle', 'Social Networking', 'Medical', 'Utilities', 'Trivia',
           'Racing', 'Books', 'Music', 'Productivity', 'Health & Fitness',
           'Food & Drink', 'News', 'Photo & Video', 'Stickers',
           'Emoji & Expressions', 'Sports & Activities', 'Gaming',
           'Comics & Cartoons', 'Animals & Nature', 'People', 'Shopping',
           'Kids & Family', 'Art', 'Places & Objects', 'Weather',
           'Magazines & Newspapers'], dtype=object)



Some genres like "Finance", "News", "Weather", etc., are obviously not game apps. Does that mean the dataset is incorrect?
Let's check some of them:


```python
games[games['Genres'].str.contains('Finance|News|Weather')].sample(9, random_state=1)
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
      <th>URL</th>
      <th>ID</th>
      <th>Name</th>
      <th>Subtitle</th>
      <th>Icon URL</th>
      <th>Average User Rating</th>
      <th>User Rating Count</th>
      <th>Price</th>
      <th>In-app Purchases</th>
      <th>Description</th>
      <th>Developer</th>
      <th>Age Rating</th>
      <th>Languages</th>
      <th>Size</th>
      <th>Primary Genre</th>
      <th>Genres</th>
      <th>Original Release Date</th>
      <th>Current Version Release Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>7196</th>
      <td>https://apps.apple.com/us/app/e-play-online/id...</td>
      <td>1053445072</td>
      <td>E-PLAY online</td>
      <td>NaN</td>
      <td>https://is4-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>E-PLAY Online to wszechstronna aplikacja media...</td>
      <td>E-Play</td>
      <td>17+</td>
      <td>EN, PL</td>
      <td>17842176.0</td>
      <td>Games</td>
      <td>Games, Strategy, News, Casino</td>
      <td>22/11/2015</td>
      <td>22/11/2015</td>
    </tr>
    <tr>
      <th>13576</th>
      <td>https://apps.apple.com/us/app/gazua-cryptocurr...</td>
      <td>1340419209</td>
      <td>GAZUA-Cryptocurrency Simulator</td>
      <td>Cryptocurrency Simulator</td>
      <td>https://is5-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.99, 3.99, 7.99, 39.99, 89.99</td>
      <td>Cryptocurrency Trading Online Simulator Game!\...</td>
      <td>ho seok Bang</td>
      <td>4+</td>
      <td>EN</td>
      <td>129117184.0</td>
      <td>Games</td>
      <td>Games, Finance, Simulation, Strategy</td>
      <td>26/03/2018</td>
      <td>7/05/2019</td>
    </tr>
    <tr>
      <th>4067</th>
      <td>https://apps.apple.com/us/app/wall-street-onli...</td>
      <td>889869710</td>
      <td>Wall Street Online</td>
      <td>NaN</td>
      <td>https://is2-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>3.0</td>
      <td>9.0</td>
      <td>0.0</td>
      <td>3.99, 1.99, 44.99</td>
      <td>Wall Street Online is the most complete game s...</td>
      <td>Gennaro Coda</td>
      <td>4+</td>
      <td>EN</td>
      <td>10908672.0</td>
      <td>Games</td>
      <td>Games, Finance, Simulation, Strategy</td>
      <td>8/01/2016</td>
      <td>13/05/2016</td>
    </tr>
    <tr>
      <th>15819</th>
      <td>https://apps.apple.com/us/app/venture-game/id1...</td>
      <td>1447789189</td>
      <td>Venture Game</td>
      <td>Stock market simulator</td>
      <td>https://is1-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>Welcome to the Venture Game. Venture game is a...</td>
      <td>Qingfeng Yang</td>
      <td>4+</td>
      <td>EN</td>
      <td>18383872.0</td>
      <td>Games</td>
      <td>Games, Finance, Strategy, Simulation</td>
      <td>4/01/2019</td>
      <td>1/02/2019</td>
    </tr>
    <tr>
      <th>13556</th>
      <td>https://apps.apple.com/us/app/trading-games/id...</td>
      <td>1338852251</td>
      <td>Trading Games</td>
      <td>For beginning traders</td>
      <td>https://is2-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>4.5</td>
      <td>16.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>"Does stock trading scare you? Try the Stock E...</td>
      <td>Boris Nekrushev</td>
      <td>4+</td>
      <td>EN, FR, DE, IT, PT, RU, ZH, ES, TR, UK</td>
      <td>195098624.0</td>
      <td>Games</td>
      <td>Games, Simulation, Strategy, Finance</td>
      <td>31/01/2018</td>
      <td>8/07/2019</td>
    </tr>
    <tr>
      <th>8801</th>
      <td>https://apps.apple.com/us/app/political-pitfal...</td>
      <td>1122713737</td>
      <td>Political Pitfalls - Path to the White House</td>
      <td>NaN</td>
      <td>https://is4-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>5.0</td>
      <td>16.0</td>
      <td>0.0</td>
      <td>1.99</td>
      <td>The reviews are in, players love Political Pit...</td>
      <td>Audacity Games Inc</td>
      <td>9+</td>
      <td>EN</td>
      <td>261175296.0</td>
      <td>Games</td>
      <td>Games, Strategy, News, Puzzle</td>
      <td>30/06/2016</td>
      <td>25/08/2016</td>
    </tr>
    <tr>
      <th>12223</th>
      <td>https://apps.apple.com/us/app/bitcoin-mining-l...</td>
      <td>1257909712</td>
      <td>Bitcoin mining: life simulator</td>
      <td>tap idle miner tycoon, empire</td>
      <td>https://is2-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>4.0</td>
      <td>57.0</td>
      <td>0.0</td>
      <td>3.99, 6.99, 0.99, 0.99, 14.99, 3.99, 1.99, 24....</td>
      <td>"Did you twist the spinners? So it's time for ...</td>
      <td>Aliaksandr Prakarym</td>
      <td>4+</td>
      <td>EN, FR, DE, IT, PT, RU, ES</td>
      <td>261438464.0</td>
      <td>Games</td>
      <td>Games, Simulation, Strategy, Finance</td>
      <td>14/07/2017</td>
      <td>9/07/2019</td>
    </tr>
    <tr>
      <th>10192</th>
      <td>https://apps.apple.com/us/app/fortune-city-exp...</td>
      <td>1172713884</td>
      <td>Fortune City - Expense Tracker</td>
      <td>Cute game for expense tracking</td>
      <td>https://is3-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>4.5</td>
      <td>3293.0</td>
      <td>0.0</td>
      <td>2.99, 4.99, 1.99, 19.99, 1.99, 1.99, 49.99, 99.99</td>
      <td>Track your spending, grow a city! Fortune City...</td>
      <td>Fourdesire</td>
      <td>4+</td>
      <td>EN, JA, KO, ZH, ZH</td>
      <td>325504000.0</td>
      <td>Finance</td>
      <td>Finance, Games, Strategy, Simulation</td>
      <td>9/02/2017</td>
      <td>27/07/2019</td>
    </tr>
    <tr>
      <th>10371</th>
      <td>https://apps.apple.com/us/app/miner-build-your...</td>
      <td>1180413293</td>
      <td>Miner - build your own mining company</td>
      <td>NaN</td>
      <td>https://is5-ssl.mzstatic.com/image/thumb/Purpl...</td>
      <td>3.5</td>
      <td>13.0</td>
      <td>0.0</td>
      <td>4.99</td>
      <td>"Miner: The Love of Gold is a fiendishly cleve...</td>
      <td>Roman Barzyczak</td>
      <td>4+</td>
      <td>EN, PL</td>
      <td>62228480.0</td>
      <td>Games</td>
      <td>Games, Puzzle, Finance, Strategy</td>
      <td>12/01/2017</td>
      <td>17/01/2017</td>
    </tr>
  </tbody>
</table>
</div>



It turns out those apps are game apps, indeed. For example, "Wall Street Online" is not only a strategy game, but also a simulation game whose theme is finance related. For those apps, it's easier to understand what the app is by looking at its genres that are listed in [App Store Sitemap](https://apps.apple.com/us/genre/ios-games/id6014). Comparing all unique genres of the dataset with the sitemap, there is no "Dice" genre, and "Educational" is actually "Education". At last, here is the full list of all existing genres:

- Action
- Adventure
- Board
- Card
- Casino
- Casual
- Education
- Family
- Music
- Puzzle
- Racing
- Role Playing
- Simulation
- Sports
- Strategy
- Trivia
- Word


Our analysis will focus on above genres only, and later we will separate those apps by the list.

Finally, let's check these 2 columns related to date:


```python
print(games["Original Release Date"].apply(lambda v: type(v)).unique())
print(games["Current Version Release Date"].apply(lambda v: type(v)).unique())
```

    [<class 'str'>]
    [<class 'str'>]


They are string objects, we will need to convert them to datetime.

<a id="process"></a>
## 4. Process

In this step, we need to do data cleaning and manipulation.


```python
# drop duplicates
games.drop_duplicates(inplace=True)

# drop unneeded columns
games.drop(columns=["URL", "ID", "Subtitle", "Icon URL"], inplace=True)

# make the column names more code-friendly
games.columns = [
    'name',
    'avg_user_rating',
    'user_rating_count',
    'price',
    'in_app_purchase',
    'description',
    'developer',
    'age_rating',
    'languages',
    'size',
    'primary_genre',
    'genres',
    'release_date',
    'current_version_date',
    ]

# remove data without ratings
games = games[games['avg_user_rating'].notna() & games['user_rating_count'].notna()]

# remove data whose primary genre is not "Games"
games = games[games['primary_genre'] == 'Games']

games_genre_list = [
    'Action',
    'Adventure',
    'Board',
    'Card',
    'Casino',
    'Casual',
    'Education',
    'Family',
    'Music',
    'Puzzle',
    'Racing',
    'Role Playing',
    'Simulation',
    'Sports',
    'Strategy',
    'Trivia',
    'Word'
]

# convert genres to list
def get_valid_list(str):
    valid_str_list = []
    str_list = str.split(',')
    for s in str_list:
        s = s.strip()
        if s in games_genre_list and s not in valid_str_list:
            valid_str_list.append(s)
    return valid_str_list

games['genres'] = games['genres'].apply(get_valid_list)

# convert to MB
games['size'] = round(games['size'] / 1e6, 1)
games.rename(columns={'size': 'size_mb'}, inplace=True)

# convert to datetime
games['release_date'] = pd.to_datetime(games['release_date'], format='%d/%m/%Y')
games['current_version_date'] = pd.to_datetime(games['current_version_date'], format='%d/%m/%Y')

games.sample(5, random_state=1)
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
      <th>name</th>
      <th>avg_user_rating</th>
      <th>user_rating_count</th>
      <th>price</th>
      <th>in_app_purchase</th>
      <th>description</th>
      <th>developer</th>
      <th>age_rating</th>
      <th>languages</th>
      <th>size_mb</th>
      <th>primary_genre</th>
      <th>genres</th>
      <th>release_date</th>
      <th>current_version_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6904</th>
      <td>Rust Bucket</td>
      <td>4.5</td>
      <td>1096.0</td>
      <td>0.00</td>
      <td>3.99, 0.99, 1.99, 3.99</td>
      <td>Rust Bucket is a turn based dungeon crawler th...</td>
      <td>Nitrome</td>
      <td>9+</td>
      <td>EN</td>
      <td>115.7</td>
      <td>Games</td>
      <td>[Puzzle, Strategy]</td>
      <td>2015-12-16</td>
      <td>2018-11-14</td>
    </tr>
    <tr>
      <th>14922</th>
      <td>Tic Tac toe puzzle game</td>
      <td>4.5</td>
      <td>89.0</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>Play Tic Tac Toe on your iPhone and iPad. No n...</td>
      <td>Abdelmajid Rajad</td>
      <td>4+</td>
      <td>AR, HR, DA, EN, FI, FR, DE, IT, KO, PT, RU, ZH...</td>
      <td>29.6</td>
      <td>Games</td>
      <td>[Strategy]</td>
      <td>2018-08-20</td>
      <td>2019-04-06</td>
    </tr>
    <tr>
      <th>6810</th>
      <td>Re:Monster</td>
      <td>4.0</td>
      <td>94.0</td>
      <td>0.00</td>
      <td>79.99, 33.99, 17.99, 3.99, 0.99, 7.99, 44.99, ...</td>
      <td>A original novel printed of 650,000 copies has...</td>
      <td>AlphaGames Inc.</td>
      <td>12+</td>
      <td>EN</td>
      <td>240.1</td>
      <td>Games</td>
      <td>[Role Playing, Strategy]</td>
      <td>2016-02-18</td>
      <td>2019-07-24</td>
    </tr>
    <tr>
      <th>12463</th>
      <td>Duck Warfare</td>
      <td>4.5</td>
      <td>36.0</td>
      <td>0.99</td>
      <td>NaN</td>
      <td>A game by Kenny Park.\n\nThe G\xdcSCO (pronoun...</td>
      <td>allen park</td>
      <td>9+</td>
      <td>EN</td>
      <td>853.0</td>
      <td>Games</td>
      <td>[Strategy]</td>
      <td>2017-08-14</td>
      <td>2017-08-16</td>
    </tr>
    <tr>
      <th>4044</th>
      <td>Retro-Connecting Box</td>
      <td>4.5</td>
      <td>16.0</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>Retro game is a very simple and fun filled add...</td>
      <td>Superdik B.V.</td>
      <td>4+</td>
      <td>EN</td>
      <td>13.4</td>
      <td>Games</td>
      <td>[Strategy, Board]</td>
      <td>2014-06-23</td>
      <td>2014-07-23</td>
    </tr>
  </tbody>
</table>
</div>



Data cleaning is done, after checking 5 samples, we can find there are still some `NaN` values in `in_app_purchase`, but that's because they don't offer in-app purchases.
Let's visualize if there are more missing values.


```python
import missingno as msno

msno.matrix(games, labels=True, sort="descending",figsize=(7,5))
plt.show()
```


    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_24_0.png)
    


From the plot we can see that almost all missing values are from `in_app_purchase`, we can leave as it is.

<a id="analyze"></a>
## 5. Analyze and Share


### Question 1: What kind of strategy games are the most?

#### Genre
In the section of exploring the data, most strategy apps also belong to other game genres. What genres show up most frequently with strategy? Which age_rating is used the most? Let's get the relationships between genres and age_rating:


```python
# get all values of age_rating
age_rating_list = games['age_rating'].unique().tolist()

# create a dataframe to display the relationships between genres and age_rating
genre_relationships = pd.DataFrame(index=games_genre_list, columns=age_rating_list)
genre_relationships.fillna(0, inplace=True)

# get the frequency
for row in games.itertuples():        
    genre_relationships.loc[row.genres, row.age_rating] += 1

genre_relationships
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
      <th>4+</th>
      <th>9+</th>
      <th>12+</th>
      <th>17+</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Action</th>
      <td>237</td>
      <td>344</td>
      <td>324</td>
      <td>83</td>
    </tr>
    <tr>
      <th>Adventure</th>
      <td>169</td>
      <td>96</td>
      <td>59</td>
      <td>11</td>
    </tr>
    <tr>
      <th>Board</th>
      <td>625</td>
      <td>85</td>
      <td>90</td>
      <td>14</td>
    </tr>
    <tr>
      <th>Card</th>
      <td>220</td>
      <td>58</td>
      <td>54</td>
      <td>11</td>
    </tr>
    <tr>
      <th>Casino</th>
      <td>10</td>
      <td>0</td>
      <td>15</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Casual</th>
      <td>363</td>
      <td>91</td>
      <td>41</td>
      <td>18</td>
    </tr>
    <tr>
      <th>Education</th>
      <td>192</td>
      <td>19</td>
      <td>19</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Family</th>
      <td>235</td>
      <td>39</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Music</th>
      <td>31</td>
      <td>7</td>
      <td>4</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Puzzle</th>
      <td>1135</td>
      <td>101</td>
      <td>61</td>
      <td>26</td>
    </tr>
    <tr>
      <th>Racing</th>
      <td>42</td>
      <td>3</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Role Playing</th>
      <td>170</td>
      <td>281</td>
      <td>294</td>
      <td>31</td>
    </tr>
    <tr>
      <th>Simulation</th>
      <td>661</td>
      <td>246</td>
      <td>256</td>
      <td>47</td>
    </tr>
    <tr>
      <th>Sports</th>
      <td>129</td>
      <td>13</td>
      <td>15</td>
      <td>5</td>
    </tr>
    <tr>
      <th>Strategy</th>
      <td>4231</td>
      <td>1437</td>
      <td>1283</td>
      <td>268</td>
    </tr>
    <tr>
      <th>Trivia</th>
      <td>56</td>
      <td>6</td>
      <td>2</td>
      <td>5</td>
    </tr>
    <tr>
      <th>Word</th>
      <td>35</td>
      <td>6</td>
      <td>3</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



Let's visualize their relationships by a heatmap.


```python
# create a heatmap
plt.figure(figsize=(8, 8))
sns.heatmap(genre_relationships, cmap="BuPu")
plt.show()
```


    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_29_0.png)
    


As we can see from the heatmap, the most used genre is **Strategy**, which is normal since we're analyzing strategy games. For those multi-genres games, the most frequently show up genre is **Puzzle**, followed by Stimulation, while Casino, Music, Racing and Word have much lower frequency.

As for the age rating, **4+** is most common, but "17+" is least common.

#### Name


```python
from wordcloud import WordCloud

wordcloud_name = WordCloud(max_words=100,
                           background_color='white'
                           ).generate(' '.join(games['name']))
plt.figure(figsize=(10, 10))
plt.imshow(wordcloud_name, interpolation="bilinear")
plt.axis("off")
plt.show()
```


    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_31_0.png)
    


The most often used names are "War", "Battle", and "Defense", which mean war is the most popular theme of strategy games. Many apps have features like "Free", "Lite" and "HD".

#### Price


```python
games.hist(column='price', bins=30, grid=False, figsize=(6,4), color='#4aa564')
```




    array([[<AxesSubplot:title={'center':'price'}>]], dtype=object)




    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_34_1.png)
    


The price is highly skewed, most games are less than $5. Let's take a closer look at the distribution below 5:


```python
games[games['price'] <= 5].hist(column='price', bins=12, grid=False, figsize=(6,4), color='#4aa564')
```




    array([[<AxesSubplot:title={'center':'price'}>]], dtype=object)




    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_36_1.png)
    


Most games are free, and a small proportion is very close to $1. Let's divide the price to different groups, so we can use a pie chart to show their proportions:


```python
def get_price_group(price):
    if price == 0:
        return 'free'
    elif ((price > 0) & (price <= 1)):
        return 'between 0 and 1'
    elif ((price > 1) & (price <= 2)):
        return 'between 1 and 2'    
    elif ((price > 2) & (price <= 3)):
        return 'between 2 and 3'
    elif ((price > 3) & (price <= 4)):
        return 'between 3 and 4'
    elif ((price > 4) & (price <= 5)):
        return 'between 4 and 5'
    else:
        return 'higher than 5'
    
# Add column `price_group`
games['price_group'] = games['price'].apply(get_price_group)
counts = games.groupby('price_group').size()

# draw a pie chart
plt.figure(figsize=(8, 8))
palette_color = sns.color_palette('Set2')
plt.pie(counts.values, labels=counts.index, startangle=0, colors=palette_color, autopct='%1.1f%%')
plt.title('Price Distribution of Strategy Game Apps')
plt.show()
```


    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_38_0.png)
    



```python
counts
```




    price_group
    between 0 and 1     332
    between 1 and 2     189
    between 2 and 3     237
    between 3 and 4      95
    between 4 and 5     177
    free               6058
    higher than 5       132
    dtype: int64



Actually, some of free apps offer in app purchase, but the in app purchase have so many choices, it's inconvenient to group the free apps by those choices. So we keep free apps as one group.

To better understand the price distribution, we still need to calculate the percentage of those in app purchase apps.


```python
iap_free_games_cnt = games[(games['price_group'] == 'free') & (games['in_app_purchase'].notna())].shape[0]
free_games_cnt = games[games['price_group'] == 'free'].shape[0]
iap_ratio = round(iap_free_games_cnt / free_games_cnt * 100, 1)
iap_ratio
```




    67.9



As high as 67.9% of free apps offer in app purchase, i.e., 57% of total apps are free but may need you pay in app.

### Question 2: What kind of strategy games have the highest price?

In Question 1 we've known the frequency of each genre, but we don't know the average price of each genre. This time we will seperate games that have `Strategy` genre only, from multiple genres games.

**Games with Highest Price**


```python
game_prices = pd.DataFrame(columns = ['avg_price'], index = [x for x in games_genre_list])
for g in games_genre_list:
    if g != 'Strategy': # apps with multiple genres
        mask = games['genres'].apply(lambda x: g in x)
    else: # apps with one genre `Strategy` only
        mask = games['genres'].apply(lambda x: g == ''.join(x))
    selected_genre_df = games[mask]
    count = selected_genre_df.shape[0]
    sum_price = selected_genre_df['price'].sum()
    avg_price = round(sum_price / count, 2)
    
    # add to the game_prices dataframe
    game_prices.loc[g] = [avg_price]
    
game_prices = game_prices.sort_values(by=['avg_price'], ascending=False)
game_prices
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
      <th>avg_price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Board</th>
      <td>1.25</td>
    </tr>
    <tr>
      <th>Education</th>
      <td>0.83</td>
    </tr>
    <tr>
      <th>Card</th>
      <td>0.73</td>
    </tr>
    <tr>
      <th>Simulation</th>
      <td>0.7</td>
    </tr>
    <tr>
      <th>Role Playing</th>
      <td>0.66</td>
    </tr>
    <tr>
      <th>Adventure</th>
      <td>0.57</td>
    </tr>
    <tr>
      <th>Casino</th>
      <td>0.41</td>
    </tr>
    <tr>
      <th>Action</th>
      <td>0.38</td>
    </tr>
    <tr>
      <th>Puzzle</th>
      <td>0.32</td>
    </tr>
    <tr>
      <th>Sports</th>
      <td>0.29</td>
    </tr>
    <tr>
      <th>Music</th>
      <td>0.29</td>
    </tr>
    <tr>
      <th>Strategy</th>
      <td>0.28</td>
    </tr>
    <tr>
      <th>Family</th>
      <td>0.24</td>
    </tr>
    <tr>
      <th>Word</th>
      <td>0.2</td>
    </tr>
    <tr>
      <th>Racing</th>
      <td>0.15</td>
    </tr>
    <tr>
      <th>Casual</th>
      <td>0.14</td>
    </tr>
    <tr>
      <th>Trivia</th>
      <td>0.09</td>
    </tr>
  </tbody>
</table>
</div>



Its bar plot:


```python
# get average price of all apps
avg_price = round(games['price'].mean(), 2)

# create a horizontal bar plot
palette = sns.color_palette("Blues",n_colors=20)
palette.reverse()
plt.figure(figsize=(12, 8))
sns.barplot(x='avg_price',
            y=game_prices.index,
            data=game_prices,
            palette=palette)
 
# create a vertical line to show the total average price
plt.axvline(x = avg_price, color = 'r')
plt.text(avg_price + 0.05, 12, f"total average price: ${avg_price}")
plt.xlabel('Price')
plt.ylabel('Genre')
plt.title("Average Price Per Genre")
plt.show()
```


    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_46_0.png)
    


Those games that belong to both strategy and **Board** have highest average price. The price of pure strategy games are lower than total average price.

### Question 3: What kind of strategy games have the highest rating?

#### User Rating vs. Genre


```python
# get dictionary of each genre's ratings
ratings_dict = dict()
for g in games_genre_list:
    if g != 'Strategy': # apps with multiple genres
        mask = games['genres'].apply(lambda x: g in x)
    else: # apps with one genre `Strategy` only
        mask = games['genres'].apply(lambda x: g == ''.join(x))
    selected_genre_df = games[mask]
    ratings_dict[g] = selected_genre_df['avg_user_rating'].tolist()

# create boxplots
fig, ax = plt.subplots(figsize=(16,6))
ax.boxplot(ratings_dict.values())
ax.set_xticklabels(ratings_dict.keys())
fig.suptitle('Average User Ratings')
plt.show()
```


    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_49_0.png)
    


The max and 75th percentile of all genres look very similar, but the minimum ratings and the outliers lower the min values are different. Casino and Music have higher minimum average user ratings and less outliers, which means they have less "unhappy" customers, but that's normal, since in Question 1 we've known that they have much lower occurences than other genres.

If we ignore those outliers, and set highest median and minimum ratings as our goal. The following is the list of "well performed" genres, that is, all ratings are higher than 3.5 and half user ratings are higher than 4.5:

- Action
- Casual
- Family
- Music
- Puzzle
- Role Playing
- Strategy(as the only genre)

The following is the list of "under behaved" genres, that is, a 1/4 ratings are lower than 3.5, and minimum ratings are as low as 2.0:

- Adventure
- Board
- Education
- Racing
- Simulation
- Sports
- Trivia
- Word

Next, let's check if other features, like `price`, `age_rating` and `size`, may influence the average user rating.

#### User Rating vs. Price Group


```python
plt.figure(figsize=(12, 6))
sns.boxplot(y='avg_user_rating',
            x='price_group', 
            data=games,
            order = [
                'free',
                'between 0 and 1',
                'between 1 and 2',
                'between 2 and 3',
                'between 3 and 4',
                'between 4 and 5',
                'higher than 5'
            ],
            palette='Set2',
            medianprops=dict(color="red"),
            hue='price_group')
plt.legend([],[], frameon=False)
plt.show()
```


    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_52_0.png)
    


In general, apps whose prices are higher than `$3` have higher ratings, especially the apps between `$3-$5`, half the ratings are higher than 4.5, and their minimum ratings are still higher than 3.5. 
As for those lower than `$3`, their performances are similar and are not that good.

#### User Rating vs. Age Rating


```python
plt.figure(figsize=(12, 6))
sns.boxplot(y='avg_user_rating', x='age_rating', 
                 data=games, 
                 palette='Set2',
                 medianprops=dict(color="red"),
                 hue='age_rating')
plt.legend([],[], frameon=False)
plt.show()
```


    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_55_0.png)
    


In general, users of apps with age rating "9+" and "12+" tend to give high ratings.

#### User Rating vs. Size of App

To group those apps by size, first of all, let's check its distribution by a histogram.


```python
sns.histplot(data=games, x="size_mb", bins=30)
plt.show()
```


    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_58_0.png)
    


Based on its histogram, most apps are smaller than 125 MB. They can be divied into 4 groups:
- 0 - 125 MB
- 125 - 500 MB
- 500 MB - 1 GB
- Above 1 GB


```python
def get_size_group(size):
    if size <= 125:
        return '0 - 125 MB'
    elif ((size > 125) & (size <= 500)):
        return '125 - 500 MB'
    elif ((size > 500) & (size <= 1000)):
        return '500 MB - 1 GB'    
    else:
        return 'Above 1 GB'
    
# Add column `size_group` to the dataframe
games['size_group'] = games['size_mb'].apply(get_size_group)

# create a boxplot
plt.figure(figsize=(12, 6))
sns.boxplot(y='avg_user_rating',
            x='size_group', 
            data=games,
            palette='Paired',
            hue='price_group',
            order = [
                '0 - 125 MB',
                '125 - 500 MB',
                '500 MB - 1 GB',
                'Above 1 GB'
            ],
            hue_order = [
                'free',
                'between 0 and 1',
                'between 1 and 2',
                'between 2 and 3',
                'between 3 and 4',
                'between 4 and 5',
                'higher than 5'
            ],            
            medianprops=dict(color="orange"))
plt.show()
```


    
![png](/figs/mobile-strategy-games_files/mobile-strategy-games_60_0.png)
    


What we can learn from above plot:
- for apps smaller than 125 MB, those more expensive than \$3 have higher user ratings. 
- for apps 125 - 500 MB, those cheaper than \$3 have higher user ratings.
- for apps 500 MB - 1 GB, those free apps have higher user ratings.
- for apps larger than 1 GB, those more expensive than \$5 or free have higher user ratings.

### Question 4: Who are the main competitors?

First of all, we need to group the games by developer so we can know the number of games each developer created.


```python
# grouped by developer
devs = games.groupby('developer')

# get number of apps per developer
devs_app_size = devs.size().sort_values(ascending=False)

# get average user ratings per developer
devs_user_rating = devs.agg(col_name=('avg_user_rating', 'mean'))

# merge them
devs_merged = pd.merge(devs_app_size.to_frame(), devs_user_rating, left_index = True, right_index = True)
devs_merged.reset_index(inplace=True)
devs_merged.columns = ['developer', 'app_count', 'avg_user_rating']
devs_merged
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
      <th>developer</th>
      <th>app_count</th>
      <th>avg_user_rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Tapps Tecnologia da Informa\xe7\xe3o Ltda.</td>
      <td>113</td>
      <td>4.424779</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Detention Apps</td>
      <td>38</td>
      <td>4.447368</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Qumaron</td>
      <td>36</td>
      <td>3.666667</td>
    </tr>
    <tr>
      <th>3</th>
      <td>EASY Inc.</td>
      <td>35</td>
      <td>3.985714</td>
    </tr>
    <tr>
      <th>4</th>
      <td>HexWar Games Ltd</td>
      <td>32</td>
      <td>3.390625</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>4074</th>
      <td>Infivention Inc.</td>
      <td>1</td>
      <td>3.500000</td>
    </tr>
    <tr>
      <th>4075</th>
      <td>Injoylabs Pty. Ltd.</td>
      <td>1</td>
      <td>4.500000</td>
    </tr>
    <tr>
      <th>4076</th>
      <td>Inkcadre Inc</td>
      <td>1</td>
      <td>4.500000</td>
    </tr>
    <tr>
      <th>4077</th>
      <td>Inner Hero LLC</td>
      <td>1</td>
      <td>4.500000</td>
    </tr>
    <tr>
      <th>4078</th>
      <td>zurab inaishvili</td>
      <td>1</td>
      <td>4.500000</td>
    </tr>
  </tbody>
</table>
<p>4079 rows × 3 columns</p>
</div>



The developer that created most number of apps has a strange name "Tapps Tecnologia da Informa\xe7\xe3o Ltda", which might blame to special characters. After searching online, we can find its correct name "Tapps Tecnologia da Informação Ltda".


```python
devs_merged.at[0, 'developer'] = 'Tapps Tecnologia da Informação Ltda'
```

Here we define competitors as developers who have created most apps. and still have average user rating higher than 4.0, let's get the top 10 list.


```python
# get the competitors
competitors = devs_merged[devs_merged['avg_user_rating'] > 4].reset_index(drop=True)
competitors.head(10)
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
      <th>developer</th>
      <th>app_count</th>
      <th>avg_user_rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Tapps Tecnologia da Informação Ltda</td>
      <td>113</td>
      <td>4.424779</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Detention Apps</td>
      <td>38</td>
      <td>4.447368</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Kairosoft Co.,Ltd</td>
      <td>21</td>
      <td>4.333333</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Evolution Games GmbH</td>
      <td>21</td>
      <td>4.690476</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Psycho Bear Studios</td>
      <td>19</td>
      <td>4.289474</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Flipline Studios</td>
      <td>19</td>
      <td>4.526316</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Romit Dodhia</td>
      <td>18</td>
      <td>4.444444</td>
    </tr>
    <tr>
      <th>7</th>
      <td>MOBIRIX</td>
      <td>18</td>
      <td>4.250000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Chen Yong Min</td>
      <td>18</td>
      <td>4.750000</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Ninja Kiwi</td>
      <td>17</td>
      <td>4.294118</td>
    </tr>
  </tbody>
</table>
</div>



<a id="summary"></a>
## 6. Summary & Recommendations

### Summary
**Most Common**:

- Genre: Games that belong to both Strategy and Puzzle.
- Age Rating: Games whose age rating are "4+".
- Price: Games that are free but offer in app purchase.
- Name: Games whose names contain themes like "War", "Battle" or features like "Free", "Lite" and "HD".
- Size: Below 125 MB.

**Highest Price**:

- Games that belong to both Strategy and Board have highest average price, but the price of pure strategy games are lower than total average price.

**Highest Rating**

- Genre: Action, Casual, Family, Music, Puzzle, Role Playing, and Strategy alone, have all ratings >= 3.5 and 50% of ratings higher than 4.5.
- Age Rating: "9+" and "12+"
- Price: Between \$3 - \$5
- Size: 
    - Below 125 MB: Price higher than \$3 
    - 125 - 500 MB: Price lower than \$3
    - Above 500 MB: Free apps  
    
### Recommendations

- For less competition, creating Action\Casual\Family\Music\Role Playing games, those genres also tend to have high user ratings.
- Focus on apps smaller than 125 MB, between \$3 and \$5, and have "9+" or "12+" age rating.
- Take a case study of Tecnologia da Informação Ltda and find out why it is so productive and has a high user rating.
