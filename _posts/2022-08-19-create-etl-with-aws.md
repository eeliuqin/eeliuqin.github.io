---
title: "Build and Automate a Serverless ETL Pipeline with AWS Lambda [1/2]"
category: posts
excerpt: "Youtube Trending Videos, MySQL, AWS RDS"
---

[![view on github](https://img.shields.io/badge/GitHub-View_on_GitHub-blue?logo=GitHub)](https://github.com/eeliuqin/automate-etl-aws-lambda)

## Introduction

The term **ETL pipeline** refers to the processes of extraction (from the cloud/APIs/databases/files), transforming (the raw data), and loading of data into a database such as a data warehouse.

I'm going to create an ETL pipeline in Python, below is the general idea:

- Extract — extracting information of [**Youtube Trending**](https://www.youtube.com/feed/trending) videos from [**Youtube Search and Download API**](https://rapidapi.com/h0p3rwe/api/youtube-search-and-download/). Those trending videos updates roughly every 15 minutes. With each update, videos may move up, down, or stay in the same position in the list. In Part 1, I will pull the data once and focus on how to create the pipeline. In Part 2, I'm planning to create a scheduled ETL workflow, that is, call the API at regular intervals to get new trending videos.

- Transform — cleaning and formatting data from the API's JSON responses to **Pandas Dataframes**.

- Load — writing the data into **MySQL** in **AWS RDS**.

## Extraction

### Request from the API

Sign up for [Rapid API](https://rapidapi.com), go to the [**Youtube Search and Download API**](https://rapidapi.com/h0p3rwe/api/youtube-search-and-download/) and then hit “Subscribe to test”. It offers multiple pricing plans, select the free "Basic" one:

![Select basic plan](/figs/create-etl-with-aws_files/youtube-api-select-basic.png)


```python
import pandas as pd
import requests
import importlib
# import API keys, MySQL user/password from api_keys.py
import api_keys

def get_video_info(content):
    if "video" in content:
        video = content["video"]
        # remove thumbnails
        video.pop("thumbnails", "No Key Found")
        return video
    else:
        return None

def extract_data():
    """
    Type of trending videos:
    n - now (default)
    mu - music
    mo - movies
    g - gaming
    """
    querystring = {"type":"n","hl":"en","gl":"US"}
    url = "https://youtube-search-and-download.p.rapidapi.com/trending"
    headers = {
        "X-RapidAPI-Key": api_keys.rapid_api_key,
        "X-RapidAPI-Host": "youtube-search-and-download.p.rapidapi.com"
    }
    response = requests.request("GET", url, headers=headers, params=querystring)
    # convert response string to JSON
    response_json = response.json()  
    contents = response_json['contents']
    df = pd.DataFrame([get_video_info(content) for content in contents])
    return df

df_videos = extract_data()
df_videos.head()
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
      <th>channelId</th>
      <th>channelName</th>
      <th>lengthText</th>
      <th>publishedTimeText</th>
      <th>title</th>
      <th>videoId</th>
      <th>viewCountText</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>UCOmHUn--16B90oW2L6FRR3A</td>
      <td>BLACKPINK</td>
      <td>3:14</td>
      <td>21 hours ago</td>
      <td>BLACKPINK - ‘Pink Venom’ M/V</td>
      <td>gQlMMD8auMs</td>
      <td>80,090,195 views</td>
    </tr>
    <tr>
      <th>1</th>
      <td>UCCE5NVlm0FhbunMMBT48WAw</td>
      <td>Fundy</td>
      <td>15:09</td>
      <td>11 hours ago</td>
      <td>So I Made Minecraft More Satisfying...</td>
      <td>1N22qIy_vHs</td>
      <td>753,860 views</td>
    </tr>
    <tr>
      <th>2</th>
      <td>UCJbYdyufHR-cxOuY96KIoqA</td>
      <td>AMP</td>
      <td>45:37</td>
      <td>1 day ago</td>
      <td>AMP GOES TO THERAPY</td>
      <td>6M9UG1ueBZU</td>
      <td>943,982 views</td>
    </tr>
    <tr>
      <th>3</th>
      <td>UCvj3hNvwrEgTRkeut7_cBAQ</td>
      <td>JiDion</td>
      <td>12:15</td>
      <td>1 day ago</td>
      <td>Spending The Day With A Loch Ness Monster Expert!</td>
      <td>WRhCmAgEmYs</td>
      <td>1,740,342 views</td>
    </tr>
    <tr>
      <th>4</th>
      <td>UCISglu-JC04IuYH13mfYHzw</td>
      <td>Dabl</td>
      <td>4:10</td>
      <td>1 day ago</td>
      <td>Undercover Boss Of Checkers Restaurant Is Forc...</td>
      <td>h6dL2un7YGg</td>
      <td>944,094 views</td>
    </tr>
  </tbody>
</table>
</div>



## Transformation

First of all, we need to remove missing values and duplicates.

Besides, We need to convert `lengthText` and `publishedTimeText` to datetime, and convert `viewCountText` to numbers.

Neither channel name nor video title is unique, let's keep channelId and videoId as the identifiers. And we need to record the current ranking in trending.


```python
import re
from datetime import datetime, timedelta

def get_length_minutes(s):
    t = 0
    for u in s.split(':'):
        t = 60 * t + int(u)
    # convert total seconds to minutes
    return round(t/60, 2)
    
def get_published_time(s):    
    unit_list = ["weeks", "days", "hours", "minutes", 
                 "seconds", "milliseconds", "microseconds"]
    now = datetime.now()
    parsed_s = [s.split()[:2]]
    unit = parsed_s[0][1]
    if unit in unit_list:
        time_dict = dict((fmt,float(amount)) for amount,fmt in parsed_s)
    elif unit+"s" in unit_list:
        time_dict = dict((fmt+"s",float(amount)) for amount,fmt in parsed_s)
    elif unit in ["month", "months"]:
        time_dict = dict(("days",float(amount)*30) for amount,fmt in parsed_s)
    elif unit in ["year", "years"]:
        time_dict = dict(("days",float(amount)*365) for amount,fmt in parsed_s)  
    else:
        return now   
    dt = datetime.now() - timedelta(**time_dict)
    return dt
        
def get_views_count(s):
    count = ''.join(re.findall(r'(\d+)', s))
    # get count in millions
    count_m = round(float(count)/1E6, 2)
    return count_m

def transform_data(data):
    # remove duplicates
    data.dropna(inplace=True)
    # remove rows where at least one element is missing 
    data.drop_duplicates(inplace=True)
    # add column of current ranking
    data.index += 1
    data = data.rename_axis('rank').reset_index()
    # add column of extracted at
    data["extracted_at"] = datetime.now()
    # convert text to other formats
    data["length_minutes"] = data["lengthText"].apply(get_length_minutes)
    data["published_time"] = data["publishedTimeText"].apply(get_published_time)
    data["views_millions"] = data["viewCountText"].apply(get_views_count)
    # remove unneeded columns
    data.drop(columns=["lengthText", "publishedTimeText", "viewCountText"], inplace=True)
    
    return data

df_videos_clean = transform_data(df_videos)
```

## Loading

I did the following steps:

- Step 1: sign up for [AWS](https://portal.aws.amazon.com/billing/signup#/start/email).
- Step 2: follow [Tutorial](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MySQL.html) and select "Free Tier" plan to create a MySQL DB instance in AWS RDS.
- Step 3: follow [Instruction](https://aws.amazon.com/premiumsupport/knowledge-center/connect-rds-mysql-workbench/) to connect MySQL Workbench to AWS RDS.
- Step 4: create schema and table `youtube_trending_videos` in the AWS RDS DB instance.

Here is the code to load the dataframe into MySQL.


```python
import pandas as pd
from sqlalchemy import create_engine
import pymysql

def connect():
    user = api_keys.mysql_user
    pwd = api_keys.mysql_pwd
    host= api_keys.aws_endpoint
    port = api_keys.mysql_port
    database= api_keys.mysql_db
    engine = create_engine(f"mysql+pymysql://{user}:{pwd}@{host}:{port}/{database}")
    connection = engine.connect()
    return connection
   
def load_data(data, table):
    connection = connect()
    try:   
        data.to_sql(table, if_exists='append', con=connection, index=False)
        print("Success")
    except:
        print("Found Error")
    connection.close()
        
load_data(df_videos_clean, "youtube_trending_videos")
```

    Success


Double check that table in MySQL Workbench, it did saved successfully.

![videos data saved in mysql](/figs/create-etl-with-aws_files/saved-videos-mysql.png)

## The end

In Part 1, I've created a simple ETL pipeline that can extract the data of Youtube trending videos, transform it with Python libraries, and then load it to AWS RDS. But the script is still running in my laptop.

I'm going to use AWS Lambda to make the pipeline severless and run the code daily in Part 2.
