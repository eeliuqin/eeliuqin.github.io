---
title: "The Most Popular Data Science Questions"
header:
  image: /assets/images/data-world-map.png
  caption: "Photo credit: [**Gerd Altmann**](https://pixabay.com/illustrations/binary-code-globe-africa-1695478/)"
permalink: /portfolio/popular-ds-questions/
toc: true
toc_label: "Contents"
---

<a id="introduction"></a>
## 1. Introduction

In recent years, more and more people are learning professional knowledge through the Internet. [A report in 2017](https://www.techrepublic.com/article/report-59-of-employed-data-scientists-learned-skills-on-their-own-or-via-a-mooc/) showed that up to 32% of full-time data scientists learned data science through online courses. That number should still be rising due to the pandemic.

In this project, there is a fictional company called Datamagician that provides online education about Data Science. To create contents based on what people are most interested in Data Science. They hire us as data analysts and want some advice from us.

They don't have any historical data for analyzing yet, so first and foremost, we need to find a reliable data source. After some investigation, we think **Stack Exchange** is good choice. It's a network of question-and-answer websites, one of its most famous website is Stack Overflow. It employs a reputation award system for its questions and answers. Each post is consists of one question and multiple answers, and question/answer is subject to upvotes and downvotes. This ensures that good posts are easily identifiable. Moreover, it provides a link to query and explore its database.

Given that, we are going to investigate its data sciense site **[Data Science Stack Exchange](https://datascience.stackexchange.com)** to determine what questions/topics are most popular, based on interest by subject. The database can be queried by [this link](https://data.stackexchange.com/datascience/query/new).   

<a name="ask"></a>
## 2. Ask

<a id="business_task"></a>
### 2.1 The Business Task

As data analysts, we need to analyze the data and find out the most popular data science questions, and provide recommendations about what content Datamagician should create. Our report should be backed up with compelling data insights and professional data visualizations.

<a id="key_stakeholder"></a>
### 2.2 Key Stakeholders

- **The analytics team we are working for**: A team of data analysts who are responsible for collecting, analyzing, and reporting data that helps guide Datamagician marketing strategy.
- **Datamagician executive team**: The executive team of our client, Datamagician. They are detail-oriented and will decide whether to approve our recommendations.


<a id="prepare"></a>
## 3. Prepare

<a id="data_source"></a>
### 3.1 Data source

We can run SQL queries to download the dataset from [Data Science Stack Exchange Query](https://data.stackexchange.com/datascience/query/new).

<a id="data_privacy"></a>
#### 3.1.1 Accessibility and privacy
The license can be found [here](https://stackoverflow.com/help/licensing), we can use it to explore the most popular data science questions.

<a id="data_organization"></a>
#### 3.1.2 Data organization

By checking the table names in the dataset link above , the following seems related to our goal:

|Database Schema|
|----|
|Comments|
|Posts|
|PostTags|
|Tags|

Let's double check by selecting a few rows of each table.

**Comments**
```
SELECT TOP 1 * FROM Comments
```

The first row:

|Id||PostId||Score||Text||CreationDate||UserDisplayName||UserId||ContentLicense|
|--||--||--||---||----||----||----||----|
|5||5||9||this is a super theoretical AI question. An interesting discussion! but out of place...||2014-05-14 00:23:15||||34||CC BY-SA 3.0|

**Posts**
```
SELECT TOP 1 * FROM Posts
```
The first row:

|Id||PostTypeId||AcceptedAnswerId||ParentId||CreationDate||DeletionDate||Score||ViewCount||Body||OwnerUserId||OwnerDisplayName||LastEditorUserId||LastEditorDisplayName||LastEditDate||LastActivityDate||Title||Tags||AnswerCount||CommentCount||FavoriteCount||ClosedDate||CommunityOwnedDate||ContentLicense|
|--||--||--||--||--||--||--||--||--||--||--||--||--||--||--||--||--||--||--||--||--||--||--|
|56889||1||||||2019-08-03 12:05:21||||2||185||<p>I'm trying to build a Deep Learning predictor that takes as the input a set of word vectors...</p>||25163||||25163||||2019-08-04 16:06:06||2022-02-27 02:01:30||Hyperbolic coordinates (Poincaré embeddings) as the output of a neural network||\<deep-learning\>\<keras\>\<manifold\>||1||1||||||||CC BY-SA 4.0|

**PostTags**
```
SELECT TOP 1 * FROM PostTags
```
The first row:
    
|PostId||TagId|
|---||---|
|14||1|
    
**Tags**
```
SELECT TOP 1 * FROM Tags
```
The first row:
    
|Id||TagName||Count||ExcerptPostId||WikiPostId||IsModeratorOnly||IsRequired|
|---||---||---||---||---||---||---|
|1||definitions||37||105||104|||||||
    
   

After comparing those tables, `Posts` table is our best option, we can group them by column `Tags`. While the other tables are not that good because:
   
* `Comments` doesn't have any tags so we cannot group them.
* The data of `PostTags` are inplicit in `Posts`.
* `Tags` doesn't have any date info, we cannot find its trend over time.
  And its info is included in table `Posts`
    
As for `Posts` table, its column `PostTypeId` is the `Id` of `PostTypes` table, which is:
    
|Id||Name|
|---||----|
|1||Question|
|2||Answer|
|3||Wiki|
|4||TagWikiExcerpt|
|5||TagWiki|
|6||ModeratorNomination|
|7||WikiPlaceholder|
|8||PrivilegeWiki|
    
Since we are investigating questions, we should only select posts with `PostTypeId` = 1. And we will focus on recent questions, by limiting the `CreationDate` from 2021 to current. Here is the query:
```
SELECT Id, CreationDate,
       Score, ViewCount, Tags,
       AnswerCount, FavoriteCount
FROM Posts
WHERE PostTypeId = 1 AND YEAR(CreationDate) >= 2021;
```
It returns 8137 rows, which should be enough for analyzing. Let's download and save it as CSV file `stackexchange_ds_qs_2021_202205.csv`.

<a id="exploring"></a>
### 3.2 Exploring the data

**Read the data**


```python
# import required libraries
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime

%matplotlib inline

# read the csv
questions = pd.read_csv("stackexchange_ds_qs_2021_202205.csv")

# check the first 5 rows
questions.head()
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
      <th>Id</th>
      <th>CreationDate</th>
      <th>Score</th>
      <th>ViewCount</th>
      <th>Tags</th>
      <th>AnswerCount</th>
      <th>FavoriteCount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>87391</td>
      <td>2021-01-01 03:10:42</td>
      <td>1</td>
      <td>34</td>
      <td>&lt;decision-trees&gt;</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>87392</td>
      <td>2021-01-01 07:28:07</td>
      <td>0</td>
      <td>34</td>
      <td>&lt;machine-learning&gt;&lt;python&gt;&lt;deep-learning&gt;&lt;imag...</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>87393</td>
      <td>2021-01-01 08:07:33</td>
      <td>1</td>
      <td>21</td>
      <td>&lt;neural-network&gt;&lt;deep-learning&gt;&lt;inception&gt;</td>
      <td>0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>87395</td>
      <td>2021-01-01 10:31:51</td>
      <td>1</td>
      <td>45</td>
      <td>&lt;machine-learning&gt;&lt;cloud&gt;&lt;federated-learning&gt;</td>
      <td>1</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>87404</td>
      <td>2021-01-01 18:00:21</td>
      <td>1</td>
      <td>59</td>
      <td>&lt;reinforcement-learning&gt;&lt;openai-gym&gt;</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



**Data size**


```python
questions.shape
```




    (8137, 7)



**Find duplicates**


```python
questions[questions.duplicated(['Id'], keep=False)]
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
      <th>Id</th>
      <th>CreationDate</th>
      <th>Score</th>
      <th>ViewCount</th>
      <th>Tags</th>
      <th>AnswerCount</th>
      <th>FavoriteCount</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>



The dataset has no duplicates.

**Check missing values**


```python
# Run `info()` to get each column's data type and count of NAs.
questions.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 8137 entries, 0 to 8136
    Data columns (total 7 columns):
     #   Column         Non-Null Count  Dtype  
    ---  ------         --------------  -----  
     0   Id             8137 non-null   int64  
     1   CreationDate   8137 non-null   object 
     2   Score          8137 non-null   int64  
     3   ViewCount      8137 non-null   int64  
     4   Tags           8137 non-null   object 
     5   AnswerCount    8137 non-null   int64  
     6   FavoriteCount  650 non-null    float64
    dtypes: float64(1), int64(4), object(2)
    memory usage: 445.1+ KB


All the missing values are in `FavoriteCount` column, what is it? In the first 5 rows, we can see the post with id `87395` has `FavoriteCount = 1` while id `87404` has `FavoriteCount = NaN`. After checking the corresponding post pages [Post 87395](https://datascience.stackexchange.com/questions/87395/dividing-a-dataset-to-parallelize-machine-learning-training-on-the-cloud) and [Post 87404](https://datascience.stackexchange.com/questions/87404/effects-of-slipperiness-in-openai-frozenlake-environment), we can find out FavoriteCount shows how many users have bookmarked that post, while NaN means that post has not bookmarked by any user.

Therefore, we can replace the missing values with `0`. And we can change its data type to int, since count will not be float.

Next, let's see what types the objects in `Tags` are.


```python
questions["Tags"].apply(lambda v: type(v)).unique()
```




    array([<class 'str'>], dtype=object)



Now we know that every value in this column is a string. Later we will need to change its format to make it more readable.

<a id="process"></a>
## 4. Process

As we've mentioned above, we need to fill up `FavoriteCount` and make `Tags` more readable.


```python
# fill missing values with 0
questions.fillna(0, inplace = True)

# convert `FavoriteCount` to int
questions["FavoriteCount"] = questions["FavoriteCount"].astype(int)

# convert Tags to lowercase
questions["Tags"] = questions["Tags"].str.lower()

# make `Tags` more readable
questions["Tags"] = questions["Tags"].str[1:-1].str.split("><")

# verify the first 5 rows
questions.head()
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
      <th>Id</th>
      <th>CreationDate</th>
      <th>Score</th>
      <th>ViewCount</th>
      <th>Tags</th>
      <th>AnswerCount</th>
      <th>FavoriteCount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>87391</td>
      <td>2021-01-01 03:10:42</td>
      <td>1</td>
      <td>34</td>
      <td>[decision-trees]</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>87392</td>
      <td>2021-01-01 07:28:07</td>
      <td>0</td>
      <td>34</td>
      <td>[machine-learning, python, deep-learning, imag...</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>87393</td>
      <td>2021-01-01 08:07:33</td>
      <td>1</td>
      <td>21</td>
      <td>[neural-network, deep-learning, inception]</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>87395</td>
      <td>2021-01-01 10:31:51</td>
      <td>1</td>
      <td>45</td>
      <td>[machine-learning, cloud, federated-learning]</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>87404</td>
      <td>2021-01-01 18:00:21</td>
      <td>1</td>
      <td>59</td>
      <td>[reinforcement-learning, openai-gym]</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



<a id="analyze"></a>
## 5. Analyze and Share

Let's do a basic data analysis by answering below questions:
<a id="q1"></a>
### Question 1: What questions are most popular?

Our goal is to find the most popular questions (posts whose post type is questions), since posts are categoried by tags, we are actually looking for the most popular tags.

However, "popular" is a ambiguous word, we will break it into 3 sub questions.

**Most used tags**


```python
tag_count = dict()

for tags in questions["Tags"]:
    for tag in tags:
        if tag in tag_count:
            tag_count[tag] += 1
        else:
            tag_count[tag] = 1
```

For convenience, we can convert it to a data frame:


```python
tag_count = pd.DataFrame.from_dict(tag_count, orient="index", columns=["Count"])

# sort it by tags frequency ascending
tag_count.sort_values(by=['Count'], ascending=False, inplace=True)
tag_count
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
      <th>Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>machine-learning</th>
      <td>2200</td>
    </tr>
    <tr>
      <th>python</th>
      <td>1457</td>
    </tr>
    <tr>
      <th>deep-learning</th>
      <td>1180</td>
    </tr>
    <tr>
      <th>neural-network</th>
      <td>747</td>
    </tr>
    <tr>
      <th>nlp</th>
      <td>703</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>lsi</th>
      <td>1</td>
    </tr>
    <tr>
      <th>ngboost</th>
      <td>1</td>
    </tr>
    <tr>
      <th>snorkel</th>
      <td>1</td>
    </tr>
    <tr>
      <th>labeling</th>
      <td>1</td>
    </tr>
    <tr>
      <th>odds</th>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>623 rows × 1 columns</p>
</div>



There are 623 tags, and many of them have 1 occurrences only. Let's focus on the top 30 (around 0.5% of 623) tags: 


```python
most_used = tag_count.head(30)
plt.figure(figsize=(12, 8))
sns.barplot(x='Count',
            y=most_used.index,
            data=most_used,
            palette="Blues_r").set(title='Top 30 Most Used Tags')
plt.show()
```


    
![png](/figs/popular-data-science-questions_files/popular-data-science-questions_23_0.png)
    


**Most viewed tags**


```python
tag_view_count = dict()

for index, row in questions.iterrows():
    for tag in row['Tags']:
        if tag in tag_view_count:
            tag_view_count[tag] += row['ViewCount']
        else:
            tag_view_count[tag] = row['ViewCount']
            
tag_view_count = pd.DataFrame.from_dict(tag_view_count, orient="index", columns=["ViewCount"])

# sort it by tags frequency ascending
tag_view_count.sort_values(by=['ViewCount'], ascending=False, inplace=True)

# plot the top 30 most viewed
most_viewed = tag_view_count.head(30)
plt.figure(figsize=(12, 8))
sns.barplot(x='ViewCount',
            y=most_viewed.index,
            data=most_viewed,
            palette="Greens_r").set(title='Top 30 Most Viewed Tags')
plt.show()
```


    
![png](/figs/popular-data-science-questions_files/popular-data-science-questions_25_0.png)
    


**Most favorited tags**


```python
tag_fav_count = dict()

for index, row in questions.iterrows():
    for tag in row['Tags']:
        if tag in tag_fav_count:
            tag_fav_count[tag] += row['FavoriteCount']
        else:
            tag_fav_count[tag] = row['FavoriteCount']
            
tag_fav_count = pd.DataFrame.from_dict(tag_fav_count, orient="index", columns=["FavoriteCount"])

# sort it by tags frequency ascending
tag_fav_count.sort_values(by=['FavoriteCount'], ascending=False, inplace=True)

# plot the top 30
most_favorited = tag_fav_count.head(30)
plt.figure(figsize=(12, 8))
sns.barplot(x='FavoriteCount',
            y=most_favorited.index,
            data=most_favorited,
            palette="Reds_r").set(title='Top 30 Most Favorited Tags')
plt.show()
```


    
![png](/figs/popular-data-science-questions_files/popular-data-science-questions_27_0.png)
    


We've answered the 3 sub-questions by plots, let's put them in one row for comparision:


```python
fig, axes = plt.subplots(nrows=1, ncols=3, figsize=(24,12))
sns.despine(bottom = True, left = True)
sns.barplot(x='Count',
            y=most_used.index,
            data=most_used,
            ax=axes[0],
            palette="Blues_r").set(title='Top 30 Most Used Tags')
sns.barplot(x='ViewCount',
            y=most_viewed.index,
            data=most_viewed,
            ax=axes[1],
            palette="Greens_r").set(title='Top 30 Most Viewed Tags')
sns.barplot(x='FavoriteCount',
            y=most_favorited.index,
            data=most_favorited,
            ax=axes[2],
            palette="Reds_r").set(title='Top 30 Most Favorited Tags')

plt.show()
```


    
![png](/figs/popular-data-science-questions_files/popular-data-science-questions_29_0.png)
    


As we can see from above plot, most tags rank differently in different plots, but we can still find some patterns. For example, `machine-learning`, `python` and `deep-learning` are always the top 3 tags in whichever list.

Next, let's find all tags shared by 3 plots:


```python
# get tags show up in all 3 plots
used_viewed_tags = pd.merge(most_used, most_viewed, how="inner", left_index=True, right_index=True)
top_tags = pd.merge(used_viewed_tags, most_favorited, how="inner", left_index=True, right_index=True)
top_tags.sort_values(by=['Count'], ascending=False, inplace=True)
top_tags
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
      <th>Count</th>
      <th>ViewCount</th>
      <th>FavoriteCount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>machine-learning</th>
      <td>2200</td>
      <td>185735</td>
      <td>248</td>
    </tr>
    <tr>
      <th>python</th>
      <td>1457</td>
      <td>238437</td>
      <td>101</td>
    </tr>
    <tr>
      <th>deep-learning</th>
      <td>1180</td>
      <td>119743</td>
      <td>120</td>
    </tr>
    <tr>
      <th>neural-network</th>
      <td>747</td>
      <td>60335</td>
      <td>60</td>
    </tr>
    <tr>
      <th>nlp</th>
      <td>703</td>
      <td>65026</td>
      <td>60</td>
    </tr>
    <tr>
      <th>classification</th>
      <td>693</td>
      <td>50830</td>
      <td>58</td>
    </tr>
    <tr>
      <th>keras</th>
      <td>603</td>
      <td>95310</td>
      <td>45</td>
    </tr>
    <tr>
      <th>tensorflow</th>
      <td>594</td>
      <td>94016</td>
      <td>39</td>
    </tr>
    <tr>
      <th>time-series</th>
      <td>515</td>
      <td>36275</td>
      <td>35</td>
    </tr>
    <tr>
      <th>scikit-learn</th>
      <td>456</td>
      <td>76995</td>
      <td>35</td>
    </tr>
    <tr>
      <th>regression</th>
      <td>322</td>
      <td>19083</td>
      <td>36</td>
    </tr>
    <tr>
      <th>dataset</th>
      <td>321</td>
      <td>30822</td>
      <td>20</td>
    </tr>
    <tr>
      <th>cnn</th>
      <td>320</td>
      <td>47685</td>
      <td>26</td>
    </tr>
    <tr>
      <th>lstm</th>
      <td>295</td>
      <td>22310</td>
      <td>18</td>
    </tr>
    <tr>
      <th>pandas</th>
      <td>274</td>
      <td>78813</td>
      <td>19</td>
    </tr>
    <tr>
      <th>pytorch</th>
      <td>265</td>
      <td>35947</td>
      <td>18</td>
    </tr>
    <tr>
      <th>machine-learning-model</th>
      <td>253</td>
      <td>25141</td>
      <td>27</td>
    </tr>
    <tr>
      <th>data</th>
      <td>200</td>
      <td>18527</td>
      <td>19</td>
    </tr>
    <tr>
      <th>data-science-model</th>
      <td>188</td>
      <td>18801</td>
      <td>22</td>
    </tr>
    <tr>
      <th>transformer</th>
      <td>177</td>
      <td>37627</td>
      <td>20</td>
    </tr>
    <tr>
      <th>random-forest</th>
      <td>173</td>
      <td>20891</td>
      <td>15</td>
    </tr>
  </tbody>
</table>
</div>



It returns 21 tags that are most used, viewed and favorited by people using Data Science Stack Exchange. We may notice some tags are not classified in a perfect way:

* Some tags have overlap. For example, `machine-learning-model` is a subset of `machine-learning`. 
* Some tags are too general, like `python`.


Given that, we can conclude the most popular questions are related to Machine Learning, because `python` is the most popular programming for machine learning. `deep-learning` is a subfield of machine learning, `neural-network` make up the backbone of deep learning algorithms.

If machine learning is still too broad, to be more specific, we can suggest Datamagician create content focus on **Deep Learning**, using Python. And also suggest them provide practical tutorials for tools like `tensorflow` and `scikit-learn`.

<a id="q2"></a>
### Question 2: Are these questions rising or falling in popularity?

We've known the most popular questions from 2021 to current are about `deep-learning`. We focus on recent data because information in Internet is changing rapidly, questions years ago might not that helpful to us.

However, we do care about the trend. Is the popularity rising or falling? What if it's actually falling quickly when it's still high for now? To answer such questions, we need to get all the historical data.

**Getting All the Historical Data**

Run the query below to get the whole questions dataset.

```
SELECT Id, CreationDate,
       Score, ViewCount, Tags,
       AnswerCount, FavoriteCount
FROM Posts
WHERE PostTypeId = 1;
```

Download and save the file as `stackexchange_ds_qs_all.csv`. Next, we will repeat the process as we did for questions of 2021-2022, and one extra step, we need to parse the column `CreationDate` to datetime since we need to find tags' popularity over time.


```python
# read the csv
all_questions = pd.read_csv("stackexchange_ds_qs_all.csv", parse_dates=["CreationDate"])

# fill missing values 0 and make `FavoriteCount` int
all_questions.fillna(0, inplace = True)
all_questions["FavoriteCount"] = all_questions["FavoriteCount"].astype(int)

# make `Tags` more readable
all_questions["Tags"] = all_questions["Tags"].str[1:-1].str.split("><")

# take a look at the first 5 rows
all_questions.head()
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
      <th>Id</th>
      <th>CreationDate</th>
      <th>Score</th>
      <th>ViewCount</th>
      <th>Tags</th>
      <th>AnswerCount</th>
      <th>FavoriteCount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5</td>
      <td>2014-05-13 23:58:30</td>
      <td>9</td>
      <td>834</td>
      <td>[machine-learning]</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>7</td>
      <td>2014-05-14 00:11:06</td>
      <td>4</td>
      <td>463</td>
      <td>[education, open-source]</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>14</td>
      <td>2014-05-14 01:25:59</td>
      <td>25</td>
      <td>1878</td>
      <td>[data-mining, definitions]</td>
      <td>4</td>
      <td>6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>2014-05-14 01:41:23</td>
      <td>2</td>
      <td>650</td>
      <td>[databases]</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>16</td>
      <td>2014-05-14 01:57:56</td>
      <td>17</td>
      <td>424</td>
      <td>[machine-learning, bigdata, libsvm]</td>
      <td>2</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



The data is as early as 2014, that's 8 years ago. The time span is 96 months, around 3000 days, apparently neither month nor day is a good choice for x axis. Instead, we can use quarts(every 3 months). And we can also group them by month and day of week, and see if people asked more questions on specific months or days.

**Converting CreationDate to Quarter, Month and Day of Week**


```python
# Remove the data of current quarter 2022 Q2 because it's incomplete
to_date = datetime.strptime("03/31/2022", '%m/%d/%Y')
all_questions = all_questions[all_questions["CreationDate"] <= to_date]
all_questions["Month"] =  all_questions["CreationDate"].dt.month
all_questions["DOW"] = all_questions["CreationDate"].dt.strftime("%A")
```

Then create a function to convert datetime to quarter.


```python
def get_quarter(datetime):
    year = str(datetime.year)[-2:]
    quarter = str(((datetime.month-1) // 3) + 1)
    return f"{year}Q{quarter}"

all_questions["Quarter"] = all_questions["CreationDate"].apply(get_quarter)
all_questions.sample(3)
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
      <th>Id</th>
      <th>CreationDate</th>
      <th>Score</th>
      <th>ViewCount</th>
      <th>Tags</th>
      <th>AnswerCount</th>
      <th>FavoriteCount</th>
      <th>Month</th>
      <th>DOW</th>
      <th>Quarter</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1016</th>
      <td>28922</td>
      <td>2018-03-10 17:14:12</td>
      <td>0</td>
      <td>62</td>
      <td>[machine-learning, data-cleaning, preprocessing]</td>
      <td>2</td>
      <td>0</td>
      <td>3</td>
      <td>Saturday</td>
      <td>18Q1</td>
    </tr>
    <tr>
      <th>28144</th>
      <td>25253</td>
      <td>2017-11-30 14:08:58</td>
      <td>1</td>
      <td>257</td>
      <td>[machine-learning, classification, text]</td>
      <td>1</td>
      <td>0</td>
      <td>11</td>
      <td>Thursday</td>
      <td>17Q4</td>
    </tr>
    <tr>
      <th>4764</th>
      <td>87033</td>
      <td>2020-12-22 20:06:15</td>
      <td>1</td>
      <td>60</td>
      <td>[machine-learning, python, nlp, recommender-sy...</td>
      <td>1</td>
      <td>0</td>
      <td>12</td>
      <td>Tuesday</td>
      <td>20Q4</td>
    </tr>
  </tbody>
</table>
</div>



**Distinguishing Tag `deep-learning` from Others**

Then we need to list all unqiue tags and then find out which belongs to `deep-learning`.


```python
tag_list = []

for tags in all_questions["Tags"]:
    for tag in tags:
        if tag not in tag_list:
            tag_list.append(tag)
print(sorted(tag_list))           
```

    ['.net', '3d-object-detection', '3d-reconstruction', 'ab-test', 'accuracy', 'activation-function', 'active-learning', 'activity-recognition', 'actor-critic', 'adaboost', 'adversarial-ml', 'agglomerative', 'aggregation', 'ai', 'alex-net', 'algorithms', 'allennlp', 'amazon-ml', 'anaconda', 'ann', 'annotation', 'anomaly', 'anomaly-detection', 'anonymization', 'anova', 'apache-hadoop', 'apache-kafka', 'apache-mahout', 'apache-pig', 'apache-spark', 'api', 'arima', 'association-rules', 'attention-mechanism', 'auc', 'audio-recognition', 'autoencoder', 'automatic-summarization', 'automation', 'automl', 'aws', 'aws-lambda', 'azure-ml', 'backpropagation', 'bag-of-words', 'bagging', 'bahdanau', 'bar-chart', 'bart', 'batch-normalization', 'bayes-error', 'bayesian', 'bayesian-networks', 'bayesian-nonparametric', 'beginner', 'bernoulli', 'bert', 'best-practice', 'bias', 'bigdata', 'binary', 'binary-classification', 'bioinformatics', 'bokeh', 'books', 'boosting', 'bootstraping', 'boruta', 'c', 'c++', 'caffe', 'career', 'caret', 'cart', 'catboost', 'categorical-data', 'categorical-encoding', 'causalimpact', 'cause-and-effect', 'chatbot', 'chi-square-test', 'churn', 'class-imbalance', 'classes', 'classification', 'classifier', 'cloud', 'cloud-computing', 'clustering', 'cnn', 'code', 'coherence', 'colab', 'collinearity', 'column-name', 'community', 'competitions', 'computer-vision', 'concept-drift', 'conda', 'confidence', 'config', 'confusion-matrix', 'consumerweb', 'context-vector', 'contrastive', 'convergence', 'convnet', 'convolution', 'convolutional-neural-network', 'corpus', 'correlation', 'cosine-distance', 'cost-function', 'counts', 'coursera', 'crawling', 'cross-entropy', 'cross-validation', 'cs231n', 'csv', 'cuda', 'custom-layer', 'cyclegan', 'data', 'data-analysis', 'data-augmentation', 'data-cleaning', 'data-drift', 'data-engineering', 'data-formats', 'data-imputation', 'data-indexing-techniques', 'data-leakage', 'data-mining', 'data-product', 'data-quality', 'data-science-model', 'data-stream-mining', 'data-table', 'data-wrangling', 'databases', 'dataframe', 'dataset', 'dbscan', 'decision-trees', 'deep-learning', 'deepmind', 'definitions', 'density-estimation', 'density-plot', 'derivation', 'descriptive-statistics', 'difference', 'dimensionality-reduction', 'dirichlet', 'discounted-reward', 'discriminant-analysis', 'discriminative-models', 'distance', 'distributed', 'distribution', 'doc2vec', 'document-term-matrix', 'document-understanding', 'domain-adaptation', 'dplyr', 'dqn', 'dropout', 'dummy-variables', 'dynamic-programming', 'dynamic-time-warping', 'early-stopping', 'ecdf', 'education', 'efficiency', 'elastic-net', 'elastic-search', 'embeddings', 'encoder', 'encoding', 'ensemble', 'ensemble-learning', 'ensemble-modeling', 'entity-linking', 'epochs', 'error-handling', 'esl', 'estimation', 'estimators', 'ethics', 'etl', 'evaluation', 'evolutionary-algorithms', 'excel', 'expectation-maximization', 'experiments', 'explainable-ai', 'exploratory-factor-analysis', 'f1score', 'facebook', 'fastai', 'faster-rcnn', 'fasttext', 'feature-construction', 'feature-engineering', 'feature-extraction', 'feature-importances', 'feature-interaction', 'feature-map', 'feature-reduction', 'feature-scaling', 'feature-selection', 'features', 'featurization', 'federated-learning', 'few-shot-learning', 'field-aware-factorization-machines', 'finance', 'finetuning', 'finite-precision', 'flask', 'forecast', 'forecasting', 'freebase', 'frequency-encoding', 'functional-api', 'functions', 'fuzzy-classification', 'fuzzy-logic', 'game', 'gan', 'gaussian', 'gaussian-process', 'gbm', 'generalization', 'generative-models', 'genetic', 'genetic-algorithms', 'genetic-programming', 'gensim', 'geospatial', 'ggplot2', 'gini-index', 'glm', 'glorot-initialization', 'gmm', 'gnn', 'goodness-of-fit', 'google', 'google-bigquery', 'google-cloud', 'google-cloud-platform', 'google-data-studio', 'google-prediction-api', 'gpu', 'gradient', 'gradient-boosting-decision-trees', 'gradient-descent', 'grammar-inference', 'graph-neural-network', 'graphical-model', 'graphs', 'graphviz', 'grid-search', 'gridsearchcv', 'groupby', 'gru', 'h2o', 'hardware', 'hashing', 'hashing-trick', 'hashingvectorizer', 'hbase', 'heatmap', 'hierarchical-data-format', 'hinge-loss', 'histogram', 'historgram', 'history', 'hive', 'hog', 'homework', 'hpc', 'huggingface', 'hydra', 'hyperparameter', 'hyperparameter-tuning', 'hypothesis-testing', 'ibm-watson', 'image', 'image-classification', 'image-preprocessing', 'image-recognition', 'image-segmentation', 'image-size', 'imbalance', 'imbalanced-data', 'imbalanced-learn', 'implementation', 'inception', 'inceptionresnetv2', 'indexing', 'inference', 'infographics', 'information-extraction', 'information-retrieval', 'information-theory', 'interpolation', 'interpretation', 'intuition', 'ipython', 'isolation-forest', 'jaccard-coefficient', 'java', 'javascript', 'json', 'julia', 'jupyter', 'k-means', 'k-nn', 'kaggle', 'kendalls-tau-coefficient', 'keras', 'keras-rl', 'kernel', 'knime', 'knowledge-base', 'knowledge-graph', 'labeling', 'labelling', 'labels', 'language-model', 'lasso', 'latent-truth', 'lda', 'lda-classifier', 'learning', 'learning-rate', 'learning-to-rank', 'least-squares-svm', 'library', 'libsvm', 'lightgbm', 'lime', 'linear-algebra', 'linear-models', 'linear-programming', 'linear-regression', 'linearly-separable', 'linux', 'logarithmic', 'logistic', 'logistic-regression', 'loss', 'loss-function', 'lsi', 'lstm', 'machine-learning', 'machine-learning-model', 'machine-translation', 'management', 'manhattan', 'manifold', 'map-reduce', 'market-basket-analysis', 'marketing', 'markov', 'markov-hidden-model', 'markov-process', 'masking', 'mathematics', 'matlab', 'matplotlib', 'matrix', 'matrix-factorisation', 'mcmc', 'mean', 'mean-shift', 'memory', 'meta-learning', 'metadata', 'metaheuristics', 'methodology', 'methods', 'metric', 'mini-batch-gradient-descent', 'missing-data', 'mlflow', 'mlops', 'mlp', 'mnist', 'model-evaluations', 'model-selection', 'momentum', 'mongodb', 'monte-carlo', 'movielens', 'mse', 'multi-instance-learning', 'multi-output', 'multiclass-classification', 'multilabel-classification', 'multitask-learning', 'multivariate-distribution', 'mutual-information', 'mxnet', 'naive-bayes-algorithim', 'naive-bayes-classifier', 'named-entity-recognition', 'natural-gradient-boosting', 'ndcg', 'neo4j', 'nestedcv', 'networkx', 'neural', 'neural-network', 'neural-style-transfer', 'ngboost', 'ngrams', 'nlg', 'nlp', 'nltk', 'noise', 'noisification', 'non-parametric', 'normal', 'normal-equation', 'normalization', 'nosql', 'notation', 'numerical', 'numpy', 'nvidia', 'object-detection', 'object-recognition', 'objective-function', 'ocr', 'octave', 'omegaconf', 'one-class-classification', 'one-hot-encoding', 'one-shot-learning', 'online-learning', 'open-source', 'openai-gpt', 'openai-gym', 'opencv', 'optimization', 'orange', 'orange3', 'outlier', 'overfitting', 'oversampling', 'pac-learning', 'pacf', 'pandas', 'parallel', 'parameter', 'parameter-estimation', 'parsing', 'partial-dependence-plot', 'partial-least-squares', 'pasting', 'pattern-recognition', 'pca', 'pcamixdata', 'pearsons-correlation-coefficient', 'perceptron', 'performance', 'permutation-test', 'perplexity', 'pgm', 'pickle', 'pip', 'pipelines', 'plotly', 'plotting', 'poisson', 'policy-gradients', 'pooling', 'powerbi', 'predict', 'prediction', 'predictive-modeling', 'predictor-importance', 'preprocessing', 'pretraining', 'privacy', 'probabilistic-programming', 'probability', 'probability-calibration', 'processing', 'programming', 'project-planning', 'pruning', 'pvalue', 'pybrain', 'pymc3', 'pyspark', 'python', 'python-2.7', 'python-3.x', 'pytorch', 'pytorch-geometric', 'q-learning', 'question-answering', 'r', 'r-squared', 'random-forest', 'randomized-algorithms', 'ranking', 'rapidminer', 'rasa-nlu', 'rattle', 'rbf', 'rbm', 'real-ml-usecase', 'recommender-system', 'recurrent-neural-network', 'redshift', 'reference-request', 'regex', 'regression', 'regularization', 'reinforcement-learning', 'relational-dbms', 'representation', 'research', 'reshape', 'reward', 'rfe', 'ridge-regression', 'rmse', 'rnn', 'roc', 'rstudio', 'sagemaker', 'sampling', 'sas', 'scala', 'scalability', 'scatter-index', 'scikit-learn', 'scipy', 'score', 'scoring', 'scraping', 'seaborn', 'search', 'search-engine', 'seed', 'self-driving', 'self-study', 'semantic-segmentation', 'semantic-similarity', 'semi-supervised-learning', 'sensors', 'sentiment-analysis', 'sequence', 'sequence-to-sequence', 'sequential-pattern-mining', 'serialisation', 'sgd', 'shap', 'siamese-networks', 'sigmoid', 'similar-documents', 'similarity', 'simulation', 'sliq', 'smote', 'smotenc', 'snorkel', 'social-network-analysis', 'softmax', 'software-development', 'software-recommendation', 'spacy', 'sparse', 'sparsity', 'spatial-transformer', 'spearmans-rank-correlation', 'spectral-clustering', 'speech-to-text', 'sports', 'sprint', 'spss', 'spyder', 'sql', 'stacked-lstm', 'stacking', 'stanford-nlp', 'stata', 'state-of-the-art', 'statistics', 'statsmodels', 'stemming', 'structural-equation-modelling', 'structured-data', 'supervised-learning', 'survival-analysis', 'svm', 'svr', 'tableau', 'target-encoding', 'tensorboard', 'tensorflow', 'tensorflow-serving', 'terminology', 'tesseract', 'test', 'text', 'text-classification', 'text-filter', 'text-generation', 'text-mining', 'text-processing', 'tfidf', 'tflearn', 'theano', 'theory', 'time', 'time-complexity', 'time-series', 'tokenization', 'tools', 'topic-model', 'torch', 'torchvision', 'tpu', 'training', 'tranformation', 'transfer-learning', 'transformation', 'transformer', 'tsne', 'twitter', 'uncertainty', 'universal-approximation-theorem', 'unsupervised-learning', 'usecase', 'vae', 'validation', 'variance', 'vc-theory', 'vector-space-models', 'version-control', 'vgg16', 'visualization', 'vowpal-wabbit', 'wasserstein', 'web-scraping', 'weight-initialization', 'weighted-data', 'weka', 'wikipedia', 'windows', 'wolfram-language', 'word', 'word-embeddings', 'word2vec', 'xgboost', 'yolo']


Here is the list of tags belong to `deep-learning`:

* `cnn`: Convolutional Neural Networks
* `lstm`: Long Short Term Memory Networks
* `rnn`: Recurrent Neural Networks
* `gan`: Generative Adversarial Networks 
* `mlp`: Multilayer Perceptrons
* `rbm`: Restricted Boltzmann Machines
* `autoencoder`: a type of neural network
* `scikit-learn`: a machine learning(and deep-learning) library for the Python programming language
* `tensorflow`: an open-source library developed by Google primarily for deep learning applications
* `keras`: a deep learning API written in Python
* `neural-network`: a subset of machine learning and are at the heart of deep learning algorithms
* `deep-learning`

Next, let's create a function that assigns 1 to deep learning questions and 0 otherwise.


```python
deep_learning_tags = [
    'cnn',
    'lstm',
    'rnn',
    'gan',
    'mlp',
    'rbm',
    'autoencoder',
    'scikit-learn',
    'tensorflow',
    'keras',
    'neural-network',
    'deep-learning',
    ]
def belongs_deep_learning(tags):
    for tag in tags:
        if tag in deep_learning_tags:
            return 1
    return 0

# Add a new column `DeepLearning`
all_questions["DeepLearning"] = all_questions["Tags"].apply(belongs_deep_learning)

all_questions.sample(3)
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
      <th>Id</th>
      <th>CreationDate</th>
      <th>Score</th>
      <th>ViewCount</th>
      <th>Tags</th>
      <th>AnswerCount</th>
      <th>FavoriteCount</th>
      <th>Month</th>
      <th>DOW</th>
      <th>Quarter</th>
      <th>DeepLearning</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2690</th>
      <td>85331</td>
      <td>2020-11-13 03:40:38</td>
      <td>1</td>
      <td>15</td>
      <td>[machine-learning, data, data-science-model, m...</td>
      <td>0</td>
      <td>0</td>
      <td>11</td>
      <td>Friday</td>
      <td>20Q4</td>
      <td>0</td>
    </tr>
    <tr>
      <th>28816</th>
      <td>79799</td>
      <td>2020-08-05 06:09:15</td>
      <td>0</td>
      <td>87</td>
      <td>[python, deep-learning, scikit-learn, statisti...</td>
      <td>0</td>
      <td>0</td>
      <td>8</td>
      <td>Wednesday</td>
      <td>20Q3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>15271</th>
      <td>14834</td>
      <td>2016-10-30 19:28:26</td>
      <td>0</td>
      <td>506</td>
      <td>[algorithms, optimization]</td>
      <td>1</td>
      <td>1</td>
      <td>10</td>
      <td>Sunday</td>
      <td>16Q4</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



**Tracking Its Popularity Quarterly**

Let's create a summarized dataframe grouped by quarter.


```python
q_grouped = all_questions.groupby('Quarter')
dl_grouped = q_grouped["DeepLearning"]
dl_df = dl_grouped.agg(["size", "sum"])
dl_df.columns = ["TotalQuestions", "DLQuestions"]
dl_df["NonDLQuestions"] = dl_df["TotalQuestions"] - dl_df["DLQuestions"]
dl_df["DLQuestionPercentage"] = round(dl_df["DLQuestions"] / dl_df["TotalQuestions"] * 100, 2)
dl_df.reset_index(inplace=True)
dl_df.tail()
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
      <th>Quarter</th>
      <th>TotalQuestions</th>
      <th>DLQuestions</th>
      <th>NonDLQuestions</th>
      <th>DLQuestionPercentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>27</th>
      <td>21Q1</td>
      <td>1289</td>
      <td>486</td>
      <td>803</td>
      <td>37.70</td>
    </tr>
    <tr>
      <th>28</th>
      <td>21Q2</td>
      <td>1677</td>
      <td>646</td>
      <td>1031</td>
      <td>38.52</td>
    </tr>
    <tr>
      <th>29</th>
      <td>21Q3</td>
      <td>1593</td>
      <td>597</td>
      <td>996</td>
      <td>37.48</td>
    </tr>
    <tr>
      <th>30</th>
      <td>21Q4</td>
      <td>1473</td>
      <td>474</td>
      <td>999</td>
      <td>32.18</td>
    </tr>
    <tr>
      <th>31</th>
      <td>22Q1</td>
      <td>1448</td>
      <td>517</td>
      <td>931</td>
      <td>35.70</td>
    </tr>
  </tbody>
</table>
</div>



Visualize it by stacked bar chart and line chart.


```python
# plot bars in stack manner
fig, axes = plt.subplots(nrows=1, ncols=2)
for i in range(0,2):
    for pos in ['right', 'top']: 
        axes[i].spines[pos].set_visible(False)
fig.set_size_inches((20, 8))

dl_df[['Quarter', 'DLQuestions', 'NonDLQuestions']].plot(
    x='Quarter',
    kind='bar',
    stacked=True,
    ax=axes[0],
    rot=45,
    color=['royalblue', 'lightskyblue'],
    title='Quartly Frequency of Tag Deep Learning',
    )
axes[0].set_ylabel('Occurrences of Tags')

ax2 = dl_df.plot(
    x='Quarter',
    y='DLQuestionPercentage',
    ax=axes[1],
    rot=45,
    kind='line',
    color='darkblue',
    title='Quartly Percentage of Tag Deep Learning ',
    )
axes[1].set_ylabel('Percentage(%)')
axes[1].get_legend().remove()

```


    
![png](/figs/popular-data-science-questions_files/popular-data-science-questions_46_0.png)
    


As we can see from above plots, the percentage of deep learning questions increased rapidly since the start of Data Science Stack Exchange, and begins to flatten out in recent 2 years. It dropped a little in 2021 Q4 but it's back up in next quarter and still keep high proportion at around 40%.

We insist our previous conclusion, i.e., we suggest the company Datamagician create content focus on **Deep Learning**.

<a id="q3"></a>
### Question 3: When are these questions asked the most?

What if Datamagician wants to offer promotions about their online deep learning courses, is there any month or day of week that attracts more people? We can use the popularity as the proxy, because the more people ask questions, the more interested they are, the more likely they are to choose related courses. We can answer that question by tracking the popularity by month and day of week.

**Tracking its popularity by month**

Let's create a summarized dataframe grouped by month.


```python
q_grouped_month = all_questions.groupby('Month')
dl_grouped_month = q_grouped_month["DeepLearning"]
dl_df_month = dl_grouped_month.agg(["size", "sum"])
dl_df_month.columns = ["TotalQuestions", "DLQuestions"]
dl_df_month["DLQuestionPercentage"] = round(dl_df_month["DLQuestions"] / dl_df_month["TotalQuestions"] * 100, 2)
dl_df_month
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
      <th>TotalQuestions</th>
      <th>DLQuestions</th>
      <th>DLQuestionPercentage</th>
    </tr>
    <tr>
      <th>Month</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>2937</td>
      <td>1112</td>
      <td>37.86</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2815</td>
      <td>1085</td>
      <td>38.54</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3081</td>
      <td>1135</td>
      <td>36.84</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2502</td>
      <td>948</td>
      <td>37.89</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2930</td>
      <td>1096</td>
      <td>37.41</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2755</td>
      <td>932</td>
      <td>33.83</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2846</td>
      <td>1077</td>
      <td>37.84</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2751</td>
      <td>1052</td>
      <td>38.24</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2402</td>
      <td>877</td>
      <td>36.51</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2372</td>
      <td>851</td>
      <td>35.88</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2536</td>
      <td>894</td>
      <td>35.25</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2535</td>
      <td>982</td>
      <td>38.74</td>
    </tr>
  </tbody>
</table>
</div>



**Tracking its popularity by day of week**


```python
q_grouped_dow = all_questions.groupby("DOW")
dl_grouped_dow = q_grouped_dow["DeepLearning"]
dl_df_dow = dl_grouped_dow.agg(["size", "sum"])
dl_df_dow.columns = ["TotalQuestions", "DLQuestions"]
dl_df_dow["DLQuestionPercentage"] = round(dl_df_dow["DLQuestions"] / dl_df_dow["TotalQuestions"] * 100, 2)
days = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
dl_df_dow = dl_df_dow.reindex(days)
dl_df_dow
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
      <th>TotalQuestions</th>
      <th>DLQuestions</th>
      <th>DLQuestionPercentage</th>
    </tr>
    <tr>
      <th>DOW</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Monday</th>
      <td>5093</td>
      <td>1903</td>
      <td>37.37</td>
    </tr>
    <tr>
      <th>Tuesday</th>
      <td>5236</td>
      <td>1917</td>
      <td>36.61</td>
    </tr>
    <tr>
      <th>Wednesday</th>
      <td>5387</td>
      <td>1974</td>
      <td>36.64</td>
    </tr>
    <tr>
      <th>Thursday</th>
      <td>5271</td>
      <td>1936</td>
      <td>36.73</td>
    </tr>
    <tr>
      <th>Friday</th>
      <td>4788</td>
      <td>1763</td>
      <td>36.82</td>
    </tr>
    <tr>
      <th>Saturday</th>
      <td>3211</td>
      <td>1238</td>
      <td>38.55</td>
    </tr>
    <tr>
      <th>Sunday</th>
      <td>3476</td>
      <td>1310</td>
      <td>37.69</td>
    </tr>
  </tbody>
</table>
</div>




```python
fig, axes = plt.subplots(nrows=1, ncols=2)
for i in range(0,2):
    for pos in ['right', 'top']: 
        axes[i].spines[pos].set_visible(False)
fig.set_size_inches((16, 5))

# by month
sns.barplot(x=dl_df_month.index, y="DLQuestionPercentage", data=dl_df_month, ax=axes[0], color="yellowgreen")
sns.pointplot(x=dl_df_month.index, y='DLQuestionPercentage', data=dl_df_month, ax=axes[0], color="black", scale=0.7)

# by day of week
sns.barplot(x=dl_df_dow.index, y="DLQuestionPercentage", data=dl_df_dow, ax=axes[1], color="orchid")
sns.pointplot(x=dl_df_dow.index, y='DLQuestionPercentage', data=dl_df_dow, ax=axes[1], color="black", scale=0.7)

plt.show()
```


    
![png](/figs/popular-data-science-questions_files/popular-data-science-questions_52_0.png)
    


#### Heatmap of Popularity


```python
# group by day of week and month
dl_df_hm = all_questions.groupby(["DOW", "Month"])
dl_df_hm = dl_df_hm["DeepLearning"].agg(["size", "sum"])
dl_df_hm.columns = ["TotalQuestions", "DLQuestions"]
dl_df_hm["DLQuestionPercentage"] = round(dl_df_hm["DLQuestions"] / dl_df_hm["TotalQuestions"] * 100, 2)
dl_df_hm = dl_df_hm.reset_index()

# create the pivot table
dl_df_pivot = dl_df_hm.pivot("DOW", "Month", "DLQuestionPercentage")
dl_df_pivot = dl_df_pivot.reindex(index = ["Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"])

# create the heatmap
plt.figure(figsize=(10, 6))
sns.heatmap(dl_df_pivot, annot=True, cmap="YlGnBu", cbar=False).set(title="Percentage of Tag Deep Learning")
plt.yticks(rotation=0)
plt.show()
```


    
![png](/figs/popular-data-science-questions_files/popular-data-science-questions_54_0.png)
    


According to above plots, the percentage of deep learning questions are higher in February, August and December. As for day of week, Saturday has the highest percentage of deep learning questions, followed by Sunday. More specifically, the highest share happen on Sunday of July and December.

<a id="q4"></a>
### Question 4: Is R potentially popular?

We've known the most popular programming language in Data Science Stack Exchange is `python`, but we also see `r` in Top 30 Most Used Tags and Most Favorited Tags. Some [articles](https://www.springboard.com/blog/data-science/best-language-for-machine-learning/) claim that R is one of the best languages for machine learning.
Should we also recommend R?

First of all, we need to figure out what tags come together with R. Here is the dataframe shows the relationships between r and other tags.


```python
# all tags in stackexchange_ds_qs_2021_2205.csv
tag_list_20_21 = list(tag_count.index)

for tags in questions["Tags"]:
    for tag in tags:
        if tag not in tag_list_20_21:
            tag_list_20_21.append(tag)

# the dataframe contains all tags
tags_relationships = pd.DataFrame(index=tag_list_20_21, columns=tag_list_20_21)
tags_relationships.fillna(0, inplace=True)

for tags in questions["Tags"]:
    tags_relationships.loc[tags, tags] += 1

# we only care about the relationships between r and others
tags_relationships = tags_relationships["r"]
tags_relationships.drop(index='r', inplace=True)

# 0 means no relationship, remove them
tags_relationships = tags_relationships[tags_relationships > 0]

# calculate the sum of all deep-learning related tags
sum_count = 0
for tag in deep_learning_tags:
    if tag in tags_relationships.index:
        sum_count += tags_relationships[tag]
tags_relationships["deep-learning"] = sum_count

tags_relationships.sort_values(ascending=False)
```




    machine-learning        48
    deep-learning           19
    python                  16
    statistics              16
    time-series             15
                            ..
    distance                 1
    image                    1
    graph-neural-network     1
    topic-model              1
    caret                    1
    Name: r, Length: 148, dtype: int64



`r` was most used together with `machine-learning`, followed by `deep-learning`, but seems the count 48 and 19 are not convincing enough. Is it possible that the popularity of `r` is rising even though r questions are fewer than python? Let's investigate that question step by step.

#### Group by quarter


```python
# check if the tags contain `r`
def belongs_r(tags):
    for tag in tags:
        if tag == "r":
            return 1
    return 0

# check if the tags contain `python`
def belongs_python(tags):
    for tag in tags:
        if tag == "python":
            return 1
    return 0

all_questions["RQuestions"] = all_questions["Tags"].apply(belongs_r)
all_questions["PythonQuestions"] = all_questions["Tags"].apply(belongs_python)

q_grouped = all_questions.groupby('Quarter')
r_grouped = q_grouped[["RQuestions", "PythonQuestions"]]
r_df = r_grouped.agg(["size", "sum"])
r_df.columns = ["TotalQuestions", "RQuestions", "TotalQuestions2", "PythonQuestions"]
r_df.drop(columns=["TotalQuestions2"], inplace=True)
r_df["RPercentage"] = round(r_df["RQuestions"] / r_df["TotalQuestions"] * 100, 2)
r_df["PythonPercentage"] = round(r_df["PythonQuestions"] / r_df["TotalQuestions"] * 100, 2)
r_df.reset_index(inplace=True)
r_df.head()
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
      <th>Quarter</th>
      <th>TotalQuestions</th>
      <th>RQuestions</th>
      <th>PythonQuestions</th>
      <th>RPercentage</th>
      <th>PythonPercentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>14Q2</td>
      <td>157</td>
      <td>10</td>
      <td>12</td>
      <td>6.37</td>
      <td>7.64</td>
    </tr>
    <tr>
      <th>1</th>
      <td>14Q3</td>
      <td>188</td>
      <td>22</td>
      <td>14</td>
      <td>11.70</td>
      <td>7.45</td>
    </tr>
    <tr>
      <th>2</th>
      <td>14Q4</td>
      <td>214</td>
      <td>22</td>
      <td>17</td>
      <td>10.28</td>
      <td>7.94</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15Q1</td>
      <td>188</td>
      <td>24</td>
      <td>15</td>
      <td>12.77</td>
      <td>7.98</td>
    </tr>
    <tr>
      <th>4</th>
      <td>15Q2</td>
      <td>284</td>
      <td>37</td>
      <td>36</td>
      <td>13.03</td>
      <td>12.68</td>
    </tr>
  </tbody>
</table>
</div>



#### Data visualization


```python
ax1 = r_df.plot(
    x='Quarter',
    y='RPercentage',
    kind='line',
    marker='o',
    color='darkorange',
    figsize=(14, 8)
    )

ax2 = r_df.plot(
    x='Quarter',
    y='PythonPercentage',
    kind='line',
    marker='o',
    color='b',
    ax=ax1,
    secondary_y=True,
    rot=45
    )

ax1.set_ylim(0, 25)
ax2.set_ylim(0, 25)

for ax in (ax1, ax2):
    for where in ("top", "right"):
        ax.spines[where].set_visible(False)
        ax.tick_params(right=False, labelright=False)

```


    
![png](/figs/popular-data-science-questions_files/popular-data-science-questions_62_0.png)
    


From 2014 Q2 to 2015 Q3, R was getting more and more popular and its popularity peaked in 2015 Q3, while Python was less popular than it. However, since then R's popularity was gradually declining, meanwhile Python's popularity was rising up. Since 2018, the percentage of R questions begins to flatten out and keeps around 3%, but Python is still higher than 15%.

We cannot conclude that R is potentially popular in the future according the line plots, further investigation and more factors are needed to understand R's popularity. For now, we will not recommend Datamagician to create contents using R, if they still want to, we suggest it should be some general knowledge in `machine-learning`.

<a id="act"></a>
## 6. Act & Recommendations

* The most popular data science questions are related to Machine Learning, or more specically, Deep Learning. We suggest creating practical tutorials for tools like `tensorflow` and `scikit-learn` that focused on **Deep Learning** , using **Python**.

* **February** and **December**, **Saturday** and **Sunday** are months and days that have highest percentage of deeping learning questions, respectively. We suggest providing more coupons or discounts during that period to attract customers.

* We recommend Python rather than R given Python's popularity. If R tutorial is a must-have, we suggest focusing on general knowledge about **Machine Learning**.
