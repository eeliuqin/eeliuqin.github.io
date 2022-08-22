---
title: "Build and Automate a Serverless ETL Pipeline with AWS Lambda [2/2]"
category: posts
excerpt: "Python, AWS Lambda, Amazon EventBridge"
---

In [Part 1](/create-etl-with-aws), I created a Python script to collect data from Youtube trending videos and save it to AWS RDS MySQL instance. However, that script was running in my laptop, it's time to make it serverless!

I'm going to automate the ETL Pipeline in [**AWS Lambda**](https://aws.amazon.com/lambda/) and schedule to run it daily. The code can be found in my [github](https://github.com/eeliuqin/automate-etl-aws-lambda).

Amazon provides well-documented [tutorial](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html), besides, there are many videos, for instance, [How to perform ETL with AWS lambda using Python](https://www.youtube.com/watch?v=cFO2-gs56d8) that can show the process in a quick and clear way. Basically, here are the steps:

## Create Lambda function

### lambda_function.py

![create lambda function](/figs/create-etl-with-aws_files/create-function.png)

### Add Layers

The script requries 5 Python packages: Pandas, Numpy, Requests, SQLAlchemy, and PyMySQL. There are 3 ways to add a layer, I think it's convenient to specifiy a layer by providing the ARN. For instance, specify an ARN for Pandas:
![Add layers to AWS Lambda](/figs/create-etl-with-aws_files/add-layer.png)

### Test

Click "Test" button to execute the code, check "Execution results".

![Add layers to AWS Lambda](/figs/create-etl-with-aws_files/test-result.png)
There is no errors.

Double check MySQL, table `youtube_trending_videos` did update.
![Add layers to AWS Lambda](/figs/create-etl-with-aws_files/new-sql-records.png)

## Automate AWS Lambda function using Amazon EventBridge CloudWatch

### Event schedule

Follow this [tutorial](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-get-started.html) to create a rule in Amazon EventBridge. Set the cron expression to `0 18 * * ? *`, it will run at 18:00 UTC (11:00 AM PST).

![schedule cron](/figs/create-etl-with-aws_files/rule-cron.png)

### Targets

Set the Lambda function I created earlier as the target.

![schedule cron](/figs/create-etl-with-aws_files/lambda-as-target.png)

## Summary

In Part 1 & Part 2, I have implemented the following concepts:

- Creation of AWS RDS MySQL instance.
- Creation & configuration of AWS Lambda function.
- Automation of AWS Lambda function with Amazon EventBridge.

With this ETL pipeline, I just need to wait for enough data of Youtube trending videos for future analysis. 
