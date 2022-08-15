---
title: "Online Course Notes: Udacity A/B Testing Final Project"
category: posts
excerpt: "click-through-probability"
---

## <a id="introduction">1. Introduction</a>

This is the final project of the online course [Udacity A/B Testing by Google](https://www.udacity.com/course/ab-testing--ud257). It's based on an actual experiment that was run by Udacity. The specific numbers have been changed, but the patterns have not. Let me walk you through about how to design the experiment, analyze the results, and propose a high-level follow-on experiment. The [project instructions](https://docs.google.com/document/u/1/d/1aCquhIqsUApgsxQ8-SQBAigFDcfWVVohLEXcV6jWbdI/pub) contain more details.

### <a id="overview">1.1 Experiment Overview</a>

You can find the detailed overview in above instruction link, here let me introduce it briefly.

**Current**

At the time of this experiment, Udacity courses currently have two options on the course overview page: **start free trial**, and **access course materials**. If the student clicks **start free trial**, they will be asked to enter their credit card information, and then they will be enrolled in a free trial for the paid version of the course. After 14 days, they will automatically be charged unless they cancel first. If the student clicks **access course materials**, they will be able to view the videos and take the quizzes for free, but they will not receive coaching support or a verified certificate, and they will not submit their final project for feedback.

**Changes that require A/B Testing**

In the experiment, Udacity tested a change where if the student clicked **start free trial**, they saw a pop up modal that asked them how much time they had available to devote to the course. The critical value was 5 hours:
- If the student indicated 5 or more hours per week, they would be taken through the checkout process as usual. 
- If they indicated fewer than 5 hours per week, a message would appear indicating that Udacity courses usually require a greater time commitment for successful completion, and suggesting that the student might like to access the course materials for free. At this point, the student would have the option to continue enrolling in the free trial, or access the course materials for free instead.

The screenshot below shows what the experiment looks like:

<img src="/figs/udacity-ab-testing/Udacity-AB-Testing-UI-Change.png" style="width:65%; margin:auto; display:block"/>

Here are the flow diagrams:

<img src="/figs/udacity-ab-testing/Udacity-AB-Testing-User-Flow.png" style="width:65%; margin:auto; display:block"/>

**Hypothesis**

The hypothesis was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time—without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.

### <a id="dataset">1.2 Datasets</a>

[Rough Estimates of the Baseline Values](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0): This spreadsheet contains rough estimates of the baseline values, we are going to use it for pre-test analysis, in other words, we will design the A/B test based on its data.

[Test Results](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0): This spreadsheet contains the test results, we are going to analyze it to decide if the change has any effect.

## <a id="design">3. Experiment Design</a>

According to the instructions, the unit of diversion of this experiment is a **cookie**, although if the student enrolls in the free trial, they are tracked by user-id from that point forward. The same user-id cannot enroll in the free trial twice. For users that do not enroll, their user-id is not tracked in the experiment, even if they were signed in when they visited the course overview page.

Our next step is to /figsure out the **Invariant Metrics** and **Evaluation Metrics**. The following are the possible metrics:

- **Number of cookies**: That is, number of unique cookies to view the course overview page. ($$d_{min} = 3000$$)
- **Number of user-ids**: That is, number of users who enroll in the free trial. ($$d_{min} = 50$$)
- **Number of clicks**: That is, number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger). ($$d_{min} = 240$$)
- **Click-through-probability**: That is, number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page. ($$d_{min} = 0.01$$)
- **Gross conversion**: That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. ($$d_{min} = 0.01$$)
- **Retention**: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of user-ids to complete checkout. ($$d_{min} = 0.01$$)
- **Net conversion**: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. ($$d_{min} = 0.0075$$)

### <a id="invariant">3.1 Choosing Invariant Metrics</a>
Invariant metrics shouldn’t change because of the experiment change, so we must select metrics that happen before the experiment change is trigger. In other words, the good invariant metrics are those happen before seeing the pop up modal.

Here is the table of those metrics and their baseline values:

|Invariant Metric|Formula|$$d_{min}$$|Baseline Value|
|---|---|---|---|
|Number of cookies|$$C_{cookies}$$ = # of unique cookies to view the course overview page|3000 |40000|
|Number of clicks|$$C_{clicks}$$ = # of unique cookies to click the "Start free trial" button|240 |3200|
|Click-through-probability|CTP = $$\displaystyle \frac{C_{clicks}}{C_{cookies}}$$|0.01|0.08|

### <a id="evaluation">3.2 Choosing Evaluation Metrics</a>
In contrast to invariant metrics, evaluation metrics will be affected by the change of experiment and vary between control and treatment group.

Here is the table of those evaluation metrics and their baseline values:

|Metric|<p align="left">Formula</p>|dmin|Baseline Value|
|-----------|------------|---------|---------|
|Gross Conversion|<p align="left">$$\displaystyle{GC = }\frac{C_{enrollments}}{C_{clicks}}$$</p>|0.01|0.20625|
|Retention|<p align="left">$$\displaystyle{R = }\frac{C_{payments}}{C_{enrollments}}$$</p>|0.01|0.53| 
|Net Conversion|<p align="left">$$\displaystyle{NC = }\frac{C_{payments}}{C_{clicks}}$$</p>|0.0075|0.1093125|

Actually, $$\displaystyle \text{Retention} = \frac{\text{Net conversion}}{\text{Gross conversion}}$$, so we probably don't need all 3 metrics, knowing any 2 metrics can calculate the 3rd one. Let's dig deeper and decide which one to be ignored.

### <a id="se">3.3 Calculating Standard Error</a>

**Requirement**

For each metric we selected as an evaluation metric, make an analytic estimate of its standard error, given a sample size of **5000** cookies visiting the course overview page. Enter each estimate in the appropriate box to 4 decimal places

**Hint 1**: Make sure use only the information given in the [Rough Estimates of the Baseline Values](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0). Do not use [Test Results](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0), since this step should be done before the experiment is run.

**Hint 2**: Make sure to find out how many units of analysis will correspond to **5000** pageviews for each metric. Again, use the given [Rough Estimates of the Baseline Values](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0).

**Formula**

The formula is

$$\displaystyle SE = \sqrt{\frac{p \cdot (1-p)}{n}}$$

Where $$p$$ is proportion of successes in the sample, $$n$$ is the number of observations in the sample, both values can be found in [Rough Estimates of the Baseline Values](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0).

**Read baseline values**


```python
import pandas as pd
import numpy as np
import math
from scipy.stats import norm

baseline = pd.read_csv("data/Udacity-AB-Testing-Final-Project-Baseline-Values.csv")
baseline
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
      <th>Metric</th>
      <th>Baseline Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Unique cookies to view course overview page pe...</td>
      <td>40000.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Unique cookies to click "Start free trial" per...</td>
      <td>3200.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Enrollments per day:</td>
      <td>660.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Click-through-probability on "Start free trial":</td>
      <td>0.080000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Probability of enrolling, given click:</td>
      <td>0.206250</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Probability of payment, given enroll:</td>
      <td>0.530000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Probability of payment, given click</td>
      <td>0.109313</td>
    </tr>
  </tbody>
</table>
</div>



Our next step is to create functions to calculate standard errors, based on above baseline values.


```python
# Baseline values of those metrics
cookies = 40000
clicks = 3200
enrollments = 660
CTP = 0.08
gross_conversion = 0.20625
retention = 0.53
net_conversion = 0.1093125

# Dictonary of Gross conversion
GC = {}
GC['p'] = gross_conversion
GC['d_min'] = 0.01
GC['n'] = clicks

# Dictonary of Retention
R = {}
R['p'] = retention
R['d_min'] = 0.01
R['n'] = enrollments

# Dictonary of Net Conversion
NC = {}
NC['p'] = net_conversion
NC['d_min'] = 0.0075
NC['n'] = clicks

# Create the function to calculate Standard Error
def calculate_se(p, n):
    SE = np.sqrt(p * (1-p) / n)
    return round(SE, 4)

GC['SE'] = calculate_se(GC['p'], GC['n'])
R['SE'] = calculate_se(R['p'], R['n'])
NC['SE'] = calculate_se(NC['p'], NC['n'])

evaluation_metrics = pd.DataFrame(
    {
        "Standard Error (n=40000)": [
            calculate_se(GC["p"], GC["n"]),
            calculate_se(R["p"], R["n"]),
            calculate_se(NC["p"], NC["n"])
        ]
    },
    index=["Gross conversion", "Retention", "Net conversion"]
)

evaluation_metrics
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
      <th>Standard Error (n=40000)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Gross conversion</th>
      <td>0.0072</td>
    </tr>
    <tr>
      <th>Retention</th>
      <td>0.0194</td>
    </tr>
    <tr>
      <th>Net conversion</th>
      <td>0.0055</td>
    </tr>
  </tbody>
</table>
</div>



**Scaling**

We've known the standard errors for 40000 cookies (see the spreadsheet of baseline values ). But the requirement is to get the standard error when n=5000 cookies, according to the formula, standard error is proportional to $$\displaystyle \sqrt{\frac{1}{n}}$$, so the standard errors of n=5000 will be larger than n = 40000, with a ratio $$\displaystyle \sqrt{\frac{40000}{5000}}$$.


```python
evaluation_metrics['Standard Error (n=5000)'] = round(evaluation_metrics['Standard Error (n=40000)'] * np.sqrt(40000/5000), 4)

evaluation_metrics
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
      <th>Standard Error (n=40000)</th>
      <th>Standard Error (n=5000)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Gross conversion</th>
      <td>0.0072</td>
      <td>0.0204</td>
    </tr>
    <tr>
      <th>Retention</th>
      <td>0.0194</td>
      <td>0.0549</td>
    </tr>
    <tr>
      <th>Net conversion</th>
      <td>0.0055</td>
      <td>0.0156</td>
    </tr>
  </tbody>
</table>
</div>



### <a id="sample_size">3.4 Calculating Sample Size</a>

In order to obtain meaningful results, we want our test to have sufficient statistical power ($$1- \beta$$). As sample size increases, the statistical power increases, but the trade-off is the probability of getting Type I error increases as sample size increases. Therefore, it's important to choose appropriate sample size to get a balance.

In this project, $$\alpha = 0.05, \beta = 0.2$$, they are the most common Type I error rate and Type II error rate (Reference [Type I and Type II errors](https://en.wikipedia.org/wiki/Type_I_and_type_II_errors)).

There are multiple methods to calculate the sample size, here we will try 2 of them.

**Method 1:  Formula**

The formula for estimate minimum sample size of each group (two-sided test) is as follows:

$$\displaystyle N = \frac{(z_{1-\alpha/2} + z_{1-\beta})^2 \cdot (\sigma_{1}^2 + \sigma_{2}^2)}{\delta^2}$$

where $$\sigma = \left\lvert{u_{2} - u_{1}}\right\rvert$$, the means and variances of the two respective groups are ($$u_{1}, \sigma_{1}^2$$) and ($$u_{2}, \sigma_{2}^2$$)

Above is the general formula, in the pre-analysis of A/B testing, we can use the following values:
- $$u_{1} = p_{baseline}$$
- $$u_{2} = p_{baseline} + d_{min}$$
- $$\delta = d_{min}$$
- $$\sigma_{1}^2 = p_{baseline} \cdot (1- p_{baseline})$$
- $$\sigma_{2}^2 = (p_{baseline} + d_{min}) \cdot (1- (p_{baseline} + d_{min}))$$

Let's calculate the sample size by the following code.


```python
def get_z_score(cd):
    # cd is the cumulative density
    return norm.ppf(cd)

def sample_size(p1, p2, alpha=0.05, beta=0.2):
    z1 = get_z_score(1 - alpha/2)
    z2 = get_z_score(1 - beta)
    d = p2 - p1
    z = z1 + z2
    v1 = p1 * (1-p1)
    v2 = p2 * (1-p2)
    v = v1 + v2
    n = z**2 * v / d**2

    return int(math.ceil(n))

# calculate sample sizes
GC['Sample Size'] = sample_size(p1=GC['p'], p2=GC['p']+GC['d_min'])
R['Sample Size'] = sample_size(p1=R['p'], p2=R['p']+R['d_min'])
NC['Sample Size'] = sample_size(p1=NC['p'], p2=NC['p']+NC['d_min'])

evaluation_metrics["Sample Size (Method 1)"] = [
    GC["Sample Size"],
    R["Sample Size"],
    NC["Sample Size"]
]

evaluation_metrics
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
      <th>Standard Error (n=40000)</th>
      <th>Standard Error (n=5000)</th>
      <th>Sample Size (Method 1)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Gross conversion</th>
      <td>0.0072</td>
      <td>0.0204</td>
      <td>26153</td>
    </tr>
    <tr>
      <th>Retention</th>
      <td>0.0194</td>
      <td>0.0549</td>
      <td>39049</td>
    </tr>
    <tr>
      <th>Net conversion</th>
      <td>0.0055</td>
      <td>0.0156</td>
      <td>27982</td>
    </tr>
  </tbody>
</table>
</div>



**Method 2: Sample Size Calculator**

One of the most common online calculator is [Sample Size Calculator by Evan Miller](https://www.evanmiller.org/ab-testing/sample-size.html).

For example, below is the screen shot of Gross conversion's sample size:

<img src="/figs/udacity-ab-testing/Udacity-AB-Testing-Sample-Size-Calculator.png" style="width:65%; margin:auto; display:block"/>

Similarly, we can get the sample sizes of the other 2 metrics, let's manually add them to the `evaluation_metrics` dataframe.


```python
evaluation_metrics["Sample Size (Method 2)"] = [25835, 39115, 27413]

evaluation_metrics
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
      <th>Standard Error (n=40000)</th>
      <th>Standard Error (n=5000)</th>
      <th>Sample Size (Method 1)</th>
      <th>Sample Size (Method 2)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Gross conversion</th>
      <td>0.0072</td>
      <td>0.0204</td>
      <td>26153</td>
      <td>25835</td>
    </tr>
    <tr>
      <th>Retention</th>
      <td>0.0194</td>
      <td>0.0549</td>
      <td>39049</td>
      <td>39115</td>
    </tr>
    <tr>
      <th>Net conversion</th>
      <td>0.0055</td>
      <td>0.0156</td>
      <td>27982</td>
      <td>27413</td>
    </tr>
  </tbody>
</table>
</div>



As we can see, the sample sizes of Method 1 and Method 2 are very close, which means our calculation is correct. In later analysis, let's use the sample sizes of Method 2.

#### <a id="pageview">3.4.1 How Many Pageviews are Required?</a>

Knowing the sample size is not the end, because the unit of the 3 metrics are different.
Gross conversion and Net conversion are based on # of clicks, while Retention is based on # of enrollment, none of them is unit of the diversion, i.e., the cookie that viewing course overview page. 

According to the baseline values, their relationships are:
- every 40000 cookies that view course overview page $$\Rightarrow$$ 3200 clicks

so the ratio of gross conversion and net conversion is 40000/3200.

- every 40000 cookies that view course overview page $$\Rightarrow$$ 660 enrollment

so the ratio of retention is 40000/660.


```python
evaluation_metrics["Required pageviews (2 groups)"] = 2* evaluation_metrics["Sample Size (Method 2)"] * 40000/3200
evaluation_metrics.loc["Retention", "Required pageviews (2 groups)"] = 2* evaluation_metrics.loc["Retention", "Sample Size (Method 2)"] * 40000/660
evaluation_metrics["Required pageviews (2 groups)"] = evaluation_metrics["Required pageviews (2 groups)"].astype(int)
evaluation_metrics
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
      <th>Standard Error (n=40000)</th>
      <th>Standard Error (n=5000)</th>
      <th>Sample Size (Method 1)</th>
      <th>Sample Size (Method 2)</th>
      <th>Required pageviews (2 groups)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Gross conversion</th>
      <td>0.0072</td>
      <td>0.0204</td>
      <td>26153</td>
      <td>25835</td>
      <td>645875</td>
    </tr>
    <tr>
      <th>Retention</th>
      <td>0.0194</td>
      <td>0.0549</td>
      <td>39049</td>
      <td>39115</td>
      <td>4741212</td>
    </tr>
    <tr>
      <th>Net conversion</th>
      <td>0.0055</td>
      <td>0.0156</td>
      <td>27982</td>
      <td>27413</td>
      <td>685325</td>
    </tr>
  </tbody>
</table>
</div>



The maximum number of required pageview is 4741212, from Retention, it's 7 times of the other 2 metrics, which means it will take 7 times longer to finish the test. As we've mentioned in <a href="#evaluation">3.2 Choosing Evaluation Metrics</a>, $$\displaystyle \text{Retention} = \frac{\text{Net conversion}}{\text{Gross conversion}}$$, we can test the other 2 metrics instead.

So here we select 685325 as the required # of pageviews.

### <a id="duration">3.5 Choosing Duration and Exposure</a>

We've known 685325 pageviews are required in this experiment. Our next step is to decide what fraction of Udacity's traffic is divert to this experiment, and then calculate the length of the experiment.

**Fraction of traffic exposed**

It's a new change that affects the functionality of the "start free trial" button, we shouldn't expose all the users to this experiment. On the other hand, the baseline value of Click-through-probability is 0.08, which means 8% of users at most will be affected when the button is not working, seems not too terrible.

Here, let's expose 80% of the population to this experiment.

**Duration**


```python
fraction_exposed = 0.8
daily_baseline_pageviews = 40000 
daily_pageviews = fraction_exposed * daily_baseline_pageviews
evaluation_metrics["Duration (days)"] = evaluation_metrics["Required pageviews (2 groups)"]/daily_pageviews
evaluation_metrics["Duration (days)"] = evaluation_metrics["Duration (days)"].apply(np.ceil).astype(int)
evaluation_metrics
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
      <th>Standard Error (n=40000)</th>
      <th>Standard Error (n=5000)</th>
      <th>Sample Size (Method 1)</th>
      <th>Sample Size (Method 2)</th>
      <th>Required pageviews (2 groups)</th>
      <th>Duration (days)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Gross conversion</th>
      <td>0.0072</td>
      <td>0.0204</td>
      <td>26153</td>
      <td>25835</td>
      <td>645875</td>
      <td>21</td>
    </tr>
    <tr>
      <th>Retention</th>
      <td>0.0194</td>
      <td>0.0549</td>
      <td>39049</td>
      <td>39115</td>
      <td>4741212</td>
      <td>149</td>
    </tr>
    <tr>
      <th>Net conversion</th>
      <td>0.0055</td>
      <td>0.0156</td>
      <td>27982</td>
      <td>27413</td>
      <td>685325</td>
      <td>22</td>
    </tr>
  </tbody>
</table>
</div>



As we can learn from above table, the experiment duration to measure Retention is as long as 149 days, so we can insist our decision to remove that metric from our list.

The other 2 metrics have very close duration, here we select the longer one, 22 days, as the final duration.

### <a id="summary_design">3.6 Summary of the Design</a>

Now we've finished the design of the experiment, below is the summary:

- **Evaluation Metrics**: Gross conversion and Net conversion
- **Required # of pageviews in total**: 685325
- **Length of the experiment**: 22 days

## <a id="analyze">4. Experiment Results</a>

The data for you to analyze is [Test Results](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0). This data contains the raw information needed to compute the above metrics, broken down day by day. Note that there are two sheets within the spreadsheet - one for the experiment group, and one for the control group.


The meaning of each column is:

- **Pageviews**: Number of unique cookies to view the course overview page that day.
- **Clicks**: Number of unique cookies to click the course overview page that day.
- **Enrollments**: Number of user-ids to enroll in the free trial that day.
- **Payments**: Number of user-ids who who enrolled on that day to remain enrolled for 14 days and thus make a payment. (Note that the date for this column is the start date, that is, the date of enrollment, rather than the date of the payment. The payment happened 14 days later. Because of this, the enrollments and payments are tracked for 14 fewer days than the other columns.)


### <a id="sanity">4.1 Sanity Checks</a>

We need to do some sanity checks to make sure this experiment are designed properly. Let's start by checking whether invariant metrics are equivalent between the two groups.

**Requirement**

For each metric that we choose as an invariant metric, compute a **95% confidence interval** for the value we expect to observe. Enter the upper and lower bounds, and the observed value, all to 4 decimal places.

**Formula**

For **Pageviews** and **Clicks**, they have binomial distribution, as we've mentioned in <a href="#se">3.3 Calculating Standard Error</a>, the formula of standard error is:

$$\displaystyle SE = \sqrt{\frac{p \cdot (1-p)}{n}}$$

The formula of confidence interval:

$$\displaystyle CI = [p - Z_{1-\alpha/2} \cdot SE, p + Z_{1-\alpha/2} \cdot SE]$$

where $$\alpha$$ is the Type I error rate, it's 0.05 when the confidence level is 95%. The null hypothesis is there is no difference between the two groups, so p is 0.5.

As for **CTP**, there are two probabilities and we need to know the difference between them is significant or not, here is the formula of the standard error of the difference:

$$\displaystyle SE_{p_{2} - p_{1}} = \sqrt{SE_{p_{1}}^2 + SE_{p_{2}}^2} = \sqrt{\frac{p_{1}\cdot (1-p_{1})}{n_{1}} + \frac{p_{2}\cdot (1-p_{2})}{n_{2}}}$$

We can improve the estimate of SE, when we expect null hypothesis is true, $$p_{1} = p_{2}$$, so we can use the pooled estimate probability instead. The modified formula is:

$$\displaystyle SE_{p_{2} - p_{1}} = \sqrt{p_{pooled}\cdot (1-p_{pooled})\cdot (\frac{1}{n_{1}} + \frac{1}{n_{2}})}$$


**Import test results**

For ease of calculation, the results of the experiment and the control group have been combined into a single file `Udacity-AB-Testing-Final-Project-Experiment-Results.csv`, the suffixes `_con` and `_exp` represent the result groups of the control group and the experiment, respectively.


```python
# read test results
results = pd.read_csv('data/Udacity-AB-Testing-Final-Project-Experiment-Results.csv')

results.head()
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
      <th>date</th>
      <th>pageviews_con</th>
      <th>clicks_con</th>
      <th>enrollments_con</th>
      <th>payments_con</th>
      <th>pageviews_exp</th>
      <th>clicks_exp</th>
      <th>enrollments_exp</th>
      <th>payments_exp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sat, Oct 11</td>
      <td>7723</td>
      <td>687</td>
      <td>134.0</td>
      <td>70.0</td>
      <td>7716</td>
      <td>686</td>
      <td>105.0</td>
      <td>34.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Sun, Oct 12</td>
      <td>9102</td>
      <td>779</td>
      <td>147.0</td>
      <td>70.0</td>
      <td>9288</td>
      <td>785</td>
      <td>116.0</td>
      <td>91.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Mon, Oct 13</td>
      <td>10511</td>
      <td>909</td>
      <td>167.0</td>
      <td>95.0</td>
      <td>10480</td>
      <td>884</td>
      <td>145.0</td>
      <td>79.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Tue, Oct 14</td>
      <td>9871</td>
      <td>836</td>
      <td>156.0</td>
      <td>105.0</td>
      <td>9867</td>
      <td>827</td>
      <td>138.0</td>
      <td>92.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Wed, Oct 15</td>
      <td>10014</td>
      <td>837</td>
      <td>163.0</td>
      <td>64.0</td>
      <td>9793</td>
      <td>832</td>
      <td>140.0</td>
      <td>94.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
results.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 37 entries, 0 to 36
    Data columns (total 9 columns):
     #   Column           Non-Null Count  Dtype  
    ---  ------           --------------  -----  
     0   date             37 non-null     object 
     1   pageviews_con    37 non-null     int64  
     2   clicks_con       37 non-null     int64  
     3   enrollments_con  23 non-null     float64
     4   payments_con     23 non-null     float64
     5   pageviews_exp    37 non-null     int64  
     6   clicks_exp       37 non-null     int64  
     7   enrollments_exp  23 non-null     float64
     8   payments_exp     23 non-null     float64
    dtypes: float64(4), int64(4), object(1)
    memory usage: 2.7+ KB


**Enrollments** and **Payments** have missing values, they only have data from Oct 11 to Nov 2. We need to keep that in mind, in later's analysis that related to Gross conversion or Net conversion, we need to make sure to use the data during that period.

**Sanity Check of Pageviews and Clicks**


```python
# get z-score when alpha = 0.05
alpha = 0.05
z_score = get_z_score(1 - alpha/2)

# initialize Pageviews and Clicks, with each group's sum results
for group in ["con", "exp"]:
    cookie_count = results[f"pageviews_{group}"].sum()
    clicks_count = results[f"clicks_{group}"].sum()
    if group == "con":
        control_list = [cookie_count, clicks_count]
    else:
        experiment_list = [cookie_count, clicks_count]

pageview_click_results = pd.DataFrame(
    {
        "Control": control_list,
        "Experiment": experiment_list
    },
    index=["Pageviews", "Clicks"]
)

# get total value of 2 groups
pageview_click_results["Total"] = pageview_click_results["Control"] + pageview_click_results["Experiment"]

# get expected/observed probability
pageview_click_results.loc[["Pageviews", "Clicks"], "Control Probability Observed"] = pageview_click_results["Control"]/pageview_click_results["Total"]
pageview_click_results.loc[["Pageviews", "Clicks"], "Control Probability Expected"] = 0.5

# get standard error
se_pageviews_expected = calculate_se(0.5, pageview_click_results.loc["Pageviews", "Total"])
se_clicks_expected = calculate_se(0.5, pageview_click_results.loc["Clicks", "Total"])

# get lower bound and upper bound of confidence interval
pageview_click_results["Standard Error"] = [se_pageviews_expected, se_clicks_expected]
margin = pageview_click_results["Standard Error"] * z_score
pageview_click_results["CI Lower Bound"] = pageview_click_results["Control Probability Expected"] - margin
pageview_click_results["CI Upper Bound"] = pageview_click_results["Control Probability Expected"] + margin
pageview_click_results = round(pageview_click_results, 4)

# check if the metric has statistical significance
def has_statistical_significance(observed, lower_bound, upper_bound):
    if lower_bound <= observed <= upper_bound:
        return False
    else:
        return True
    
pageview_click_results["Statistical Significant"] = pageview_click_results.apply(
    lambda x: has_statistical_significance(
        x["Control Probability Observed"],
        x["CI Lower Bound"],
        x["CI Upper Bound"],
    ),
    axis=1,
)

# create function to highlight True or False
def highlight_boolean(x):
    if x is True:
        color = 'lightgreen'
    elif x is False:
        color = 'pink'
    else:
        color = 'none'
    return 'background-color: %s' % color
    

pageview_click_results.style.applymap(highlight_boolean)
```




<style type="text/css">
#T_7dd54_row0_col0, #T_7dd54_row0_col1, #T_7dd54_row0_col2, #T_7dd54_row0_col3, #T_7dd54_row0_col4, #T_7dd54_row0_col5, #T_7dd54_row0_col6, #T_7dd54_row0_col7, #T_7dd54_row1_col0, #T_7dd54_row1_col1, #T_7dd54_row1_col2, #T_7dd54_row1_col3, #T_7dd54_row1_col4, #T_7dd54_row1_col5, #T_7dd54_row1_col6, #T_7dd54_row1_col7 {
  background-color: none;
}
#T_7dd54_row0_col8, #T_7dd54_row1_col8 {
  background-color: pink;
}
</style>
<table id="T_7dd54">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_7dd54_level0_col0" class="col_heading level0 col0" >Control</th>
      <th id="T_7dd54_level0_col1" class="col_heading level0 col1" >Experiment</th>
      <th id="T_7dd54_level0_col2" class="col_heading level0 col2" >Total</th>
      <th id="T_7dd54_level0_col3" class="col_heading level0 col3" >Control Probability Observed</th>
      <th id="T_7dd54_level0_col4" class="col_heading level0 col4" >Control Probability Expected</th>
      <th id="T_7dd54_level0_col5" class="col_heading level0 col5" >Standard Error</th>
      <th id="T_7dd54_level0_col6" class="col_heading level0 col6" >CI Lower Bound</th>
      <th id="T_7dd54_level0_col7" class="col_heading level0 col7" >CI Upper Bound</th>
      <th id="T_7dd54_level0_col8" class="col_heading level0 col8" >Statistical Significant</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_7dd54_level0_row0" class="row_heading level0 row0" >Pageviews</th>
      <td id="T_7dd54_row0_col0" class="data row0 col0" >345543</td>
      <td id="T_7dd54_row0_col1" class="data row0 col1" >344660</td>
      <td id="T_7dd54_row0_col2" class="data row0 col2" >690203</td>
      <td id="T_7dd54_row0_col3" class="data row0 col3" >0.500600</td>
      <td id="T_7dd54_row0_col4" class="data row0 col4" >0.500000</td>
      <td id="T_7dd54_row0_col5" class="data row0 col5" >0.000600</td>
      <td id="T_7dd54_row0_col6" class="data row0 col6" >0.498800</td>
      <td id="T_7dd54_row0_col7" class="data row0 col7" >0.501200</td>
      <td id="T_7dd54_row0_col8" class="data row0 col8" >False</td>
    </tr>
    <tr>
      <th id="T_7dd54_level0_row1" class="row_heading level0 row1" >Clicks</th>
      <td id="T_7dd54_row1_col0" class="data row1 col0" >28378</td>
      <td id="T_7dd54_row1_col1" class="data row1 col1" >28325</td>
      <td id="T_7dd54_row1_col2" class="data row1 col2" >56703</td>
      <td id="T_7dd54_row1_col3" class="data row1 col3" >0.500500</td>
      <td id="T_7dd54_row1_col4" class="data row1 col4" >0.500000</td>
      <td id="T_7dd54_row1_col5" class="data row1 col5" >0.002100</td>
      <td id="T_7dd54_row1_col6" class="data row1 col6" >0.495900</td>
      <td id="T_7dd54_row1_col7" class="data row1 col7" >0.504100</td>
      <td id="T_7dd54_row1_col8" class="data row1 col8" >False</td>
    </tr>
  </tbody>
</table>




**Sanity Check of CTP**


```python
# get standard error of the difference of CTP
def calculate_diff_se(p_pooled, n1, n2):
    SE = np.sqrt(p_pooled * (1-p_pooled) * (1/ n1 + 1/ n2))
    return round(SE, 4)
    
ctp_results = pd.DataFrame(
    {
        "Control": [pageview_click_results.loc["Clicks", "Control"]/pageview_click_results.loc["Pageviews", "Control"]],
        "Experiment": [pageview_click_results.loc["Clicks", "Experiment"]/pageview_click_results.loc["Pageviews", "Experiment"]],
        "Total": [pageview_click_results.loc["Clicks", "Total"]/pageview_click_results.loc["Pageviews", "Total"]]
    },
    index=["CTP"]
)
ctp_results["Diff Expected"] = 0
ctp_results["Diff Observed"] = ctp_results["Experiment"] - ctp_results["Control"]

# get standard error
ctp_results["Standard Error"] = calculate_diff_se(
    ctp_results["Total"],
    pageview_click_results.loc["Pageviews", "Control"],
    pageview_click_results.loc["Pageviews", "Experiment"],
)

# get confidence interval
margin = ctp_results["Standard Error"] * z_score
ctp_results["CI Lower Bound"] = ctp_results["Diff Expected"] - margin
ctp_results["CI Upper Bound"] = ctp_results["Diff Expected"] + margin

# check if the difference has statistical significance
ctp_results["Statistical Significant"] = ctp_results.apply(
    lambda x: has_statistical_significance(
        x["Diff Observed"],
        x["CI Lower Bound"],
        x["CI Upper Bound"],
    ),
    axis=1,
)
ctp_results = round(ctp_results, 5)
ctp_results.style.applymap(highlight_boolean)
```




<style type="text/css">
#T_2c26f_row0_col0, #T_2c26f_row0_col1, #T_2c26f_row0_col2, #T_2c26f_row0_col3, #T_2c26f_row0_col4, #T_2c26f_row0_col5, #T_2c26f_row0_col6, #T_2c26f_row0_col7 {
  background-color: none;
}
#T_2c26f_row0_col8 {
  background-color: pink;
}
</style>
<table id="T_2c26f">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_2c26f_level0_col0" class="col_heading level0 col0" >Control</th>
      <th id="T_2c26f_level0_col1" class="col_heading level0 col1" >Experiment</th>
      <th id="T_2c26f_level0_col2" class="col_heading level0 col2" >Total</th>
      <th id="T_2c26f_level0_col3" class="col_heading level0 col3" >Diff Expected</th>
      <th id="T_2c26f_level0_col4" class="col_heading level0 col4" >Diff Observed</th>
      <th id="T_2c26f_level0_col5" class="col_heading level0 col5" >Standard Error</th>
      <th id="T_2c26f_level0_col6" class="col_heading level0 col6" >CI Lower Bound</th>
      <th id="T_2c26f_level0_col7" class="col_heading level0 col7" >CI Upper Bound</th>
      <th id="T_2c26f_level0_col8" class="col_heading level0 col8" >Statistical Significant</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_2c26f_level0_row0" class="row_heading level0 row0" >CTP</th>
      <td id="T_2c26f_row0_col0" class="data row0 col0" >0.082130</td>
      <td id="T_2c26f_row0_col1" class="data row0 col1" >0.082180</td>
      <td id="T_2c26f_row0_col2" class="data row0 col2" >0.082150</td>
      <td id="T_2c26f_row0_col3" class="data row0 col3" >0</td>
      <td id="T_2c26f_row0_col4" class="data row0 col4" >0.000060</td>
      <td id="T_2c26f_row0_col5" class="data row0 col5" >0.000700</td>
      <td id="T_2c26f_row0_col6" class="data row0 col6" >-0.001370</td>
      <td id="T_2c26f_row0_col7" class="data row0 col7" >0.001370</td>
      <td id="T_2c26f_row0_col8" class="data row0 col8" >False</td>
    </tr>
  </tbody>
</table>




**Summary**

None of them is statistical significant, which means all sanity checks of invariant metrics passed! We are good to move to the next step.

### <a id="result_analysis">4.2 Result Analysis</a>

#### <a id="effect_size">4.2.1 Effect Size Tests</a>

In this section, we need to check whether the test results of evaluation metrics are statistically and practically significant or not. 

**Null Hypothesis**: Control group and Experiment group have same probability.

**Alternative Hypothesis**: Control group and Experiment group have different probability.

Similar to the previous step, we will need to compute a 95% confidence interval around the difference. As we've mentioned in <a href="#summary_design">3.6 Summary of the Design</a>, the evaluation metrics are Gross conversion and Net conversion. The formulas:
- $$\displaystyle {\text{Gross conversion(GC)} = }\frac{C_{enrollments}}{C_{clicks}}$$
- $$\displaystyle{\text{Net conversion(NC)} = }\frac{C_{payments}}{C_{clicks}}$$
- $$\displaystyle SE_{p_{2} - p_{1}} = \sqrt{p_{pooled}\cdot (1-p_{pooled})\cdot (\frac{1}{n_{1}} + \frac{1}{n_{2}})}$$


And don't forget enrollments and payments have valid data during Oct 11 - Nov 2(the first 23 days) only.


```python
# data from test results
sum_results = results.iloc[:23, :].sum()
control_clicks = sum_results.at['clicks_con']
experiment_clicks = sum_results.at['clicks_exp']
total_clicks = control_clicks + experiment_clicks
total_enrollments = sum_results.at['enrollments_con'] + sum_results.at['enrollments_exp']
total_payments = sum_results.at['payments_con'] + sum_results.at['payments_exp']

# create a dataframe of the 2 metrics
conversion_results = pd.DataFrame(
    {
        "Control": [sum_results.at['enrollments_con']/control_clicks,
                    sum_results.at['payments_con']/control_clicks],
        "Experiment": [sum_results.at['enrollments_exp']/experiment_clicks,
                    sum_results.at['payments_exp']/experiment_clicks],
        "Total": [total_enrollments/total_clicks,
                  total_payments/total_clicks],    
    },
    index=["Gross conversion", "Net conversion"]
)

# get dmin from section "Designing the Experiment"
conversion_results["dmin"] = [0.01, 0.0075]

# check if the metric has practical significance, in this project, experiment group 
# value is the observed value, control group value is the expected value (null hypothesis) 
def has_practical_significance(observed, expected, dmin):
    if abs(observed-expected) >= dmin:
        return True
    else:
        return False


# calculate standard error, SE = np.sqrt(p_pooled * (1-p_pooled) * (1/ n1 + 1/ n2))
conversion_results["SE"] = np.sqrt(conversion_results["Total"]*(1-conversion_results["Total"])*(1/control_clicks+1/experiment_clicks))
margin = conversion_results["SE"] * z_score
conversion_results["CI Lower Bound"] = conversion_results["Control"] - margin
conversion_results["CI Upper Bound"] = conversion_results["Control"] + margin

# statistical significance
conversion_results["Statistical Significant"] = conversion_results.apply(
    lambda x: has_statistical_significance(
        x["Experiment"],
        x["CI Lower Bound"],
        x["CI Upper Bound"]
    ),
    axis=1
)

# practical significance
conversion_results["Practical Significant"] = conversion_results.apply(
    lambda x: has_practical_significance(
        x["Experiment"],
        x["Control"],
        x["dmin"]
    ),
    axis=1
)

conversion_results.style.applymap(highlight_boolean)
```




<style type="text/css">
#T_6d9a9_row0_col0, #T_6d9a9_row0_col1, #T_6d9a9_row0_col2, #T_6d9a9_row0_col3, #T_6d9a9_row0_col4, #T_6d9a9_row0_col5, #T_6d9a9_row0_col6, #T_6d9a9_row1_col0, #T_6d9a9_row1_col1, #T_6d9a9_row1_col2, #T_6d9a9_row1_col3, #T_6d9a9_row1_col4, #T_6d9a9_row1_col5, #T_6d9a9_row1_col6 {
  background-color: none;
}
#T_6d9a9_row0_col7, #T_6d9a9_row0_col8 {
  background-color: lightgreen;
}
#T_6d9a9_row1_col7, #T_6d9a9_row1_col8 {
  background-color: pink;
}
</style>
<table id="T_6d9a9">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_6d9a9_level0_col0" class="col_heading level0 col0" >Control</th>
      <th id="T_6d9a9_level0_col1" class="col_heading level0 col1" >Experiment</th>
      <th id="T_6d9a9_level0_col2" class="col_heading level0 col2" >Total</th>
      <th id="T_6d9a9_level0_col3" class="col_heading level0 col3" >dmin</th>
      <th id="T_6d9a9_level0_col4" class="col_heading level0 col4" >SE</th>
      <th id="T_6d9a9_level0_col5" class="col_heading level0 col5" >CI Lower Bound</th>
      <th id="T_6d9a9_level0_col6" class="col_heading level0 col6" >CI Upper Bound</th>
      <th id="T_6d9a9_level0_col7" class="col_heading level0 col7" >Statistical Significant</th>
      <th id="T_6d9a9_level0_col8" class="col_heading level0 col8" >Practical Significant</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_6d9a9_level0_row0" class="row_heading level0 row0" >Gross conversion</th>
      <td id="T_6d9a9_row0_col0" class="data row0 col0" >0.218875</td>
      <td id="T_6d9a9_row0_col1" class="data row0 col1" >0.198320</td>
      <td id="T_6d9a9_row0_col2" class="data row0 col2" >0.208607</td>
      <td id="T_6d9a9_row0_col3" class="data row0 col3" >0.010000</td>
      <td id="T_6d9a9_row0_col4" class="data row0 col4" >0.004372</td>
      <td id="T_6d9a9_row0_col5" class="data row0 col5" >0.210306</td>
      <td id="T_6d9a9_row0_col6" class="data row0 col6" >0.227443</td>
      <td id="T_6d9a9_row0_col7" class="data row0 col7" >True</td>
      <td id="T_6d9a9_row0_col8" class="data row0 col8" >True</td>
    </tr>
    <tr>
      <th id="T_6d9a9_level0_row1" class="row_heading level0 row1" >Net conversion</th>
      <td id="T_6d9a9_row1_col0" class="data row1 col0" >0.117562</td>
      <td id="T_6d9a9_row1_col1" class="data row1 col1" >0.112688</td>
      <td id="T_6d9a9_row1_col2" class="data row1 col2" >0.115127</td>
      <td id="T_6d9a9_row1_col3" class="data row1 col3" >0.007500</td>
      <td id="T_6d9a9_row1_col4" class="data row1 col4" >0.003434</td>
      <td id="T_6d9a9_row1_col5" class="data row1 col5" >0.110831</td>
      <td id="T_6d9a9_row1_col6" class="data row1 col6" >0.124293</td>
      <td id="T_6d9a9_row1_col7" class="data row1 col7" >False</td>
      <td id="T_6d9a9_row1_col8" class="data row1 col8" >False</td>
    </tr>
  </tbody>
</table>




We can learn from above table that **Gross conversion** is different between the 2 groups, experiment group has significant lower Gross conversion than control group. But Net conversion has no difference. 

Next, let's try another test and see if we will get the same results.

#### <a id="sign_test">4.2.2 Sign Tests</a>

The [sign test](https://en.wikipedia.org/wiki/Sign_test) is a statistical method to check whether the signs of the difference of the metrics between the experiment and control groups agree with the confidence interval of the difference. When the value of experiment group is larger than that of control group, the sign is positive, otherwise it's negative.


**Requirement**

Run sign tests on **Gross conversion** and **Net conversion**, using the day-by-day data. For each metric, caculate its [p-value](https://en.wikipedia.org/wiki/P-value), and indicate whether each result is statistically significant.


**Get signs**

First of all, we need to get the day-by-day sign.


```python
# Get the first 23 days of data since it has no missing values
sign_test_data = results.iloc[:23, :].copy()
sign_test_data.set_index("date", inplace=True)

# The sign is "+1"/"-1" when experiment group value is larger/smaller than control group
sign_test_data["Control GC"] = sign_test_data["enrollments_con"]/sign_test_data["clicks_con"]
sign_test_data["Experiment GC"] = sign_test_data["enrollments_exp"]/sign_test_data["clicks_exp"]
sign_test_data["Control NC"] = sign_test_data["payments_con"]/sign_test_data["clicks_con"]
sign_test_data["Experiment NC"] = sign_test_data["payments_exp"]/sign_test_data["clicks_exp"]
sign_test_data["GC Sign"] = np.sign(sign_test_data["Experiment GC"] - sign_test_data["Control GC"]).astype(int)
sign_test_data["NC Sign"] = np.sign(sign_test_data["Experiment NC"] - sign_test_data["Control NC"]).astype(int)

sign_test_data = sign_test_data.loc[:, "Control GC":"NC Sign"]

# highlight signs
sign_test_data.style.applymap(lambda x: f"background-color: {'lightgreen' if x==1 else 'pink' if x==-1 else 'none'}")
```




<style type="text/css">
#T_e668d_row0_col0, #T_e668d_row0_col1, #T_e668d_row0_col2, #T_e668d_row0_col3, #T_e668d_row1_col0, #T_e668d_row1_col1, #T_e668d_row1_col2, #T_e668d_row1_col3, #T_e668d_row2_col0, #T_e668d_row2_col1, #T_e668d_row2_col2, #T_e668d_row2_col3, #T_e668d_row3_col0, #T_e668d_row3_col1, #T_e668d_row3_col2, #T_e668d_row3_col3, #T_e668d_row4_col0, #T_e668d_row4_col1, #T_e668d_row4_col2, #T_e668d_row4_col3, #T_e668d_row5_col0, #T_e668d_row5_col1, #T_e668d_row5_col2, #T_e668d_row5_col3, #T_e668d_row6_col0, #T_e668d_row6_col1, #T_e668d_row6_col2, #T_e668d_row6_col3, #T_e668d_row7_col0, #T_e668d_row7_col1, #T_e668d_row7_col2, #T_e668d_row7_col3, #T_e668d_row8_col0, #T_e668d_row8_col1, #T_e668d_row8_col2, #T_e668d_row8_col3, #T_e668d_row9_col0, #T_e668d_row9_col1, #T_e668d_row9_col2, #T_e668d_row9_col3, #T_e668d_row10_col0, #T_e668d_row10_col1, #T_e668d_row10_col2, #T_e668d_row10_col3, #T_e668d_row11_col0, #T_e668d_row11_col1, #T_e668d_row11_col2, #T_e668d_row11_col3, #T_e668d_row12_col0, #T_e668d_row12_col1, #T_e668d_row12_col2, #T_e668d_row12_col3, #T_e668d_row13_col0, #T_e668d_row13_col1, #T_e668d_row13_col2, #T_e668d_row13_col3, #T_e668d_row14_col0, #T_e668d_row14_col1, #T_e668d_row14_col2, #T_e668d_row14_col3, #T_e668d_row15_col0, #T_e668d_row15_col1, #T_e668d_row15_col2, #T_e668d_row15_col3, #T_e668d_row16_col0, #T_e668d_row16_col1, #T_e668d_row16_col2, #T_e668d_row16_col3, #T_e668d_row17_col0, #T_e668d_row17_col1, #T_e668d_row17_col2, #T_e668d_row17_col3, #T_e668d_row18_col0, #T_e668d_row18_col1, #T_e668d_row18_col2, #T_e668d_row18_col3, #T_e668d_row19_col0, #T_e668d_row19_col1, #T_e668d_row19_col2, #T_e668d_row19_col3, #T_e668d_row20_col0, #T_e668d_row20_col1, #T_e668d_row20_col2, #T_e668d_row20_col3, #T_e668d_row21_col0, #T_e668d_row21_col1, #T_e668d_row21_col2, #T_e668d_row21_col3, #T_e668d_row22_col0, #T_e668d_row22_col1, #T_e668d_row22_col2, #T_e668d_row22_col3 {
  background-color: none;
}
#T_e668d_row0_col4, #T_e668d_row0_col5, #T_e668d_row1_col4, #T_e668d_row2_col4, #T_e668d_row2_col5, #T_e668d_row3_col4, #T_e668d_row3_col5, #T_e668d_row4_col4, #T_e668d_row5_col4, #T_e668d_row5_col5, #T_e668d_row6_col4, #T_e668d_row6_col5, #T_e668d_row7_col4, #T_e668d_row7_col5, #T_e668d_row8_col4, #T_e668d_row9_col4, #T_e668d_row10_col4, #T_e668d_row10_col5, #T_e668d_row11_col4, #T_e668d_row11_col5, #T_e668d_row12_col4, #T_e668d_row13_col4, #T_e668d_row13_col5, #T_e668d_row14_col4, #T_e668d_row14_col5, #T_e668d_row15_col4, #T_e668d_row15_col5, #T_e668d_row16_col4, #T_e668d_row16_col5, #T_e668d_row19_col5, #T_e668d_row21_col4, #T_e668d_row22_col4 {
  background-color: pink;
}
#T_e668d_row1_col5, #T_e668d_row4_col5, #T_e668d_row8_col5, #T_e668d_row9_col5, #T_e668d_row12_col5, #T_e668d_row17_col4, #T_e668d_row17_col5, #T_e668d_row18_col4, #T_e668d_row18_col5, #T_e668d_row19_col4, #T_e668d_row20_col4, #T_e668d_row20_col5, #T_e668d_row21_col5, #T_e668d_row22_col5 {
  background-color: lightgreen;
}
</style>
<table id="T_e668d">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_e668d_level0_col0" class="col_heading level0 col0" >Control GC</th>
      <th id="T_e668d_level0_col1" class="col_heading level0 col1" >Experiment GC</th>
      <th id="T_e668d_level0_col2" class="col_heading level0 col2" >Control NC</th>
      <th id="T_e668d_level0_col3" class="col_heading level0 col3" >Experiment NC</th>
      <th id="T_e668d_level0_col4" class="col_heading level0 col4" >GC Sign</th>
      <th id="T_e668d_level0_col5" class="col_heading level0 col5" >NC Sign</th>
    </tr>
    <tr>
      <th class="index_name level0" >date</th>
      <th class="blank col0" >&nbsp;</th>
      <th class="blank col1" >&nbsp;</th>
      <th class="blank col2" >&nbsp;</th>
      <th class="blank col3" >&nbsp;</th>
      <th class="blank col4" >&nbsp;</th>
      <th class="blank col5" >&nbsp;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_e668d_level0_row0" class="row_heading level0 row0" >Sat, Oct 11</th>
      <td id="T_e668d_row0_col0" class="data row0 col0" >0.195051</td>
      <td id="T_e668d_row0_col1" class="data row0 col1" >0.153061</td>
      <td id="T_e668d_row0_col2" class="data row0 col2" >0.101892</td>
      <td id="T_e668d_row0_col3" class="data row0 col3" >0.049563</td>
      <td id="T_e668d_row0_col4" class="data row0 col4" >-1</td>
      <td id="T_e668d_row0_col5" class="data row0 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row1" class="row_heading level0 row1" >Sun, Oct 12</th>
      <td id="T_e668d_row1_col0" class="data row1 col0" >0.188703</td>
      <td id="T_e668d_row1_col1" class="data row1 col1" >0.147771</td>
      <td id="T_e668d_row1_col2" class="data row1 col2" >0.089859</td>
      <td id="T_e668d_row1_col3" class="data row1 col3" >0.115924</td>
      <td id="T_e668d_row1_col4" class="data row1 col4" >-1</td>
      <td id="T_e668d_row1_col5" class="data row1 col5" >1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row2" class="row_heading level0 row2" >Mon, Oct 13</th>
      <td id="T_e668d_row2_col0" class="data row2 col0" >0.183718</td>
      <td id="T_e668d_row2_col1" class="data row2 col1" >0.164027</td>
      <td id="T_e668d_row2_col2" class="data row2 col2" >0.104510</td>
      <td id="T_e668d_row2_col3" class="data row2 col3" >0.089367</td>
      <td id="T_e668d_row2_col4" class="data row2 col4" >-1</td>
      <td id="T_e668d_row2_col5" class="data row2 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row3" class="row_heading level0 row3" >Tue, Oct 14</th>
      <td id="T_e668d_row3_col0" class="data row3 col0" >0.186603</td>
      <td id="T_e668d_row3_col1" class="data row3 col1" >0.166868</td>
      <td id="T_e668d_row3_col2" class="data row3 col2" >0.125598</td>
      <td id="T_e668d_row3_col3" class="data row3 col3" >0.111245</td>
      <td id="T_e668d_row3_col4" class="data row3 col4" >-1</td>
      <td id="T_e668d_row3_col5" class="data row3 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row4" class="row_heading level0 row4" >Wed, Oct 15</th>
      <td id="T_e668d_row4_col0" class="data row4 col0" >0.194743</td>
      <td id="T_e668d_row4_col1" class="data row4 col1" >0.168269</td>
      <td id="T_e668d_row4_col2" class="data row4 col2" >0.076464</td>
      <td id="T_e668d_row4_col3" class="data row4 col3" >0.112981</td>
      <td id="T_e668d_row4_col4" class="data row4 col4" >-1</td>
      <td id="T_e668d_row4_col5" class="data row4 col5" >1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row5" class="row_heading level0 row5" >Thu, Oct 16</th>
      <td id="T_e668d_row5_col0" class="data row5 col0" >0.167679</td>
      <td id="T_e668d_row5_col1" class="data row5 col1" >0.163706</td>
      <td id="T_e668d_row5_col2" class="data row5 col2" >0.099635</td>
      <td id="T_e668d_row5_col3" class="data row5 col3" >0.077411</td>
      <td id="T_e668d_row5_col4" class="data row5 col4" >-1</td>
      <td id="T_e668d_row5_col5" class="data row5 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row6" class="row_heading level0 row6" >Fri, Oct 17</th>
      <td id="T_e668d_row6_col0" class="data row6 col0" >0.195187</td>
      <td id="T_e668d_row6_col1" class="data row6 col1" >0.162821</td>
      <td id="T_e668d_row6_col2" class="data row6 col2" >0.101604</td>
      <td id="T_e668d_row6_col3" class="data row6 col3" >0.056410</td>
      <td id="T_e668d_row6_col4" class="data row6 col4" >-1</td>
      <td id="T_e668d_row6_col5" class="data row6 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row7" class="row_heading level0 row7" >Sat, Oct 18</th>
      <td id="T_e668d_row7_col0" class="data row7 col0" >0.174051</td>
      <td id="T_e668d_row7_col1" class="data row7 col1" >0.144172</td>
      <td id="T_e668d_row7_col2" class="data row7 col2" >0.110759</td>
      <td id="T_e668d_row7_col3" class="data row7 col3" >0.095092</td>
      <td id="T_e668d_row7_col4" class="data row7 col4" >-1</td>
      <td id="T_e668d_row7_col5" class="data row7 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row8" class="row_heading level0 row8" >Sun, Oct 19</th>
      <td id="T_e668d_row8_col0" class="data row8 col0" >0.189580</td>
      <td id="T_e668d_row8_col1" class="data row8 col1" >0.172166</td>
      <td id="T_e668d_row8_col2" class="data row8 col2" >0.086831</td>
      <td id="T_e668d_row8_col3" class="data row8 col3" >0.110473</td>
      <td id="T_e668d_row8_col4" class="data row8 col4" >-1</td>
      <td id="T_e668d_row8_col5" class="data row8 col5" >1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row9" class="row_heading level0 row9" >Mon, Oct 20</th>
      <td id="T_e668d_row9_col0" class="data row9 col0" >0.191638</td>
      <td id="T_e668d_row9_col1" class="data row9 col1" >0.177907</td>
      <td id="T_e668d_row9_col2" class="data row9 col2" >0.112660</td>
      <td id="T_e668d_row9_col3" class="data row9 col3" >0.113953</td>
      <td id="T_e668d_row9_col4" class="data row9 col4" >-1</td>
      <td id="T_e668d_row9_col5" class="data row9 col5" >1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row10" class="row_heading level0 row10" >Tue, Oct 21</th>
      <td id="T_e668d_row10_col0" class="data row10 col0" >0.226067</td>
      <td id="T_e668d_row10_col1" class="data row10 col1" >0.165509</td>
      <td id="T_e668d_row10_col2" class="data row10 col2" >0.121107</td>
      <td id="T_e668d_row10_col3" class="data row10 col3" >0.082176</td>
      <td id="T_e668d_row10_col4" class="data row10 col4" >-1</td>
      <td id="T_e668d_row10_col5" class="data row10 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row11" class="row_heading level0 row11" >Wed, Oct 22</th>
      <td id="T_e668d_row11_col0" class="data row11 col0" >0.193317</td>
      <td id="T_e668d_row11_col1" class="data row11 col1" >0.159800</td>
      <td id="T_e668d_row11_col2" class="data row11 col2" >0.109785</td>
      <td id="T_e668d_row11_col3" class="data row11 col3" >0.087391</td>
      <td id="T_e668d_row11_col4" class="data row11 col4" >-1</td>
      <td id="T_e668d_row11_col5" class="data row11 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row12" class="row_heading level0 row12" >Thu, Oct 23</th>
      <td id="T_e668d_row12_col0" class="data row12 col0" >0.190977</td>
      <td id="T_e668d_row12_col1" class="data row12 col1" >0.190031</td>
      <td id="T_e668d_row12_col2" class="data row12 col2" >0.084211</td>
      <td id="T_e668d_row12_col3" class="data row12 col3" >0.105919</td>
      <td id="T_e668d_row12_col4" class="data row12 col4" >-1</td>
      <td id="T_e668d_row12_col5" class="data row12 col5" >1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row13" class="row_heading level0 row13" >Fri, Oct 24</th>
      <td id="T_e668d_row13_col0" class="data row13 col0" >0.326895</td>
      <td id="T_e668d_row13_col1" class="data row13 col1" >0.278336</td>
      <td id="T_e668d_row13_col2" class="data row13 col2" >0.181278</td>
      <td id="T_e668d_row13_col3" class="data row13 col3" >0.134864</td>
      <td id="T_e668d_row13_col4" class="data row13 col4" >-1</td>
      <td id="T_e668d_row13_col5" class="data row13 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row14" class="row_heading level0 row14" >Sat, Oct 25</th>
      <td id="T_e668d_row14_col0" class="data row14 col0" >0.254703</td>
      <td id="T_e668d_row14_col1" class="data row14 col1" >0.189836</td>
      <td id="T_e668d_row14_col2" class="data row14 col2" >0.185239</td>
      <td id="T_e668d_row14_col3" class="data row14 col3" >0.121076</td>
      <td id="T_e668d_row14_col4" class="data row14 col4" >-1</td>
      <td id="T_e668d_row14_col5" class="data row14 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row15" class="row_heading level0 row15" >Sun, Oct 26</th>
      <td id="T_e668d_row15_col0" class="data row15 col0" >0.227401</td>
      <td id="T_e668d_row15_col1" class="data row15 col1" >0.220779</td>
      <td id="T_e668d_row15_col2" class="data row15 col2" >0.146893</td>
      <td id="T_e668d_row15_col3" class="data row15 col3" >0.145743</td>
      <td id="T_e668d_row15_col4" class="data row15 col4" >-1</td>
      <td id="T_e668d_row15_col5" class="data row15 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row16" class="row_heading level0 row16" >Mon, Oct 27</th>
      <td id="T_e668d_row16_col0" class="data row16 col0" >0.306983</td>
      <td id="T_e668d_row16_col1" class="data row16 col1" >0.276265</td>
      <td id="T_e668d_row16_col2" class="data row16 col2" >0.163373</td>
      <td id="T_e668d_row16_col3" class="data row16 col3" >0.154345</td>
      <td id="T_e668d_row16_col4" class="data row16 col4" >-1</td>
      <td id="T_e668d_row16_col5" class="data row16 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row17" class="row_heading level0 row17" >Tue, Oct 28</th>
      <td id="T_e668d_row17_col0" class="data row17 col0" >0.209239</td>
      <td id="T_e668d_row17_col1" class="data row17 col1" >0.220109</td>
      <td id="T_e668d_row17_col2" class="data row17 col2" >0.123641</td>
      <td id="T_e668d_row17_col3" class="data row17 col3" >0.163043</td>
      <td id="T_e668d_row17_col4" class="data row17 col4" >1</td>
      <td id="T_e668d_row17_col5" class="data row17 col5" >1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row18" class="row_heading level0 row18" >Wed, Oct 29</th>
      <td id="T_e668d_row18_col0" class="data row18 col0" >0.265223</td>
      <td id="T_e668d_row18_col1" class="data row18 col1" >0.276479</td>
      <td id="T_e668d_row18_col2" class="data row18 col2" >0.116373</td>
      <td id="T_e668d_row18_col3" class="data row18 col3" >0.132050</td>
      <td id="T_e668d_row18_col4" class="data row18 col4" >1</td>
      <td id="T_e668d_row18_col5" class="data row18 col5" >1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row19" class="row_heading level0 row19" >Thu, Oct 30</th>
      <td id="T_e668d_row19_col0" class="data row19 col0" >0.227520</td>
      <td id="T_e668d_row19_col1" class="data row19 col1" >0.284341</td>
      <td id="T_e668d_row19_col2" class="data row19 col2" >0.102180</td>
      <td id="T_e668d_row19_col3" class="data row19 col3" >0.092033</td>
      <td id="T_e668d_row19_col4" class="data row19 col4" >1</td>
      <td id="T_e668d_row19_col5" class="data row19 col5" >-1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row20" class="row_heading level0 row20" >Fri, Oct 31</th>
      <td id="T_e668d_row20_col0" class="data row20 col0" >0.246459</td>
      <td id="T_e668d_row20_col1" class="data row20 col1" >0.252078</td>
      <td id="T_e668d_row20_col2" class="data row20 col2" >0.143059</td>
      <td id="T_e668d_row20_col3" class="data row20 col3" >0.170360</td>
      <td id="T_e668d_row20_col4" class="data row20 col4" >1</td>
      <td id="T_e668d_row20_col5" class="data row20 col5" >1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row21" class="row_heading level0 row21" >Sat, Nov 1</th>
      <td id="T_e668d_row21_col0" class="data row21 col0" >0.229075</td>
      <td id="T_e668d_row21_col1" class="data row21 col1" >0.204317</td>
      <td id="T_e668d_row21_col2" class="data row21 col2" >0.136564</td>
      <td id="T_e668d_row21_col3" class="data row21 col3" >0.143885</td>
      <td id="T_e668d_row21_col4" class="data row21 col4" >-1</td>
      <td id="T_e668d_row21_col5" class="data row21 col5" >1</td>
    </tr>
    <tr>
      <th id="T_e668d_level0_row22" class="row_heading level0 row22" >Sun, Nov 2</th>
      <td id="T_e668d_row22_col0" class="data row22 col0" >0.297258</td>
      <td id="T_e668d_row22_col1" class="data row22 col1" >0.251381</td>
      <td id="T_e668d_row22_col2" class="data row22 col2" >0.096681</td>
      <td id="T_e668d_row22_col3" class="data row22 col3" >0.142265</td>
      <td id="T_e668d_row22_col4" class="data row22 col4" >-1</td>
      <td id="T_e668d_row22_col5" class="data row22 col5" >1</td>
    </tr>
  </tbody>
</table>




For Gross conversion, there are 4 positive signs in 23 day-by-day tests.
As for Net conversion, the number of positive signs is 10.

Then we can use [Sign Test Calculator](https://www.graphpad.com/quickcalcs/binomial1/) to get the p-value. Here is the screenshot of Gross conversion:

<img src="/figs/udacity-ab-testing/Udacity-AB-Testing-Sign-Binomial-Test-Calculator.png" style="width:65%; margin:auto; display:block"/>

**Sign Test Result**

- **Gross conversion**: p-value 0.0026 is less than 0.05, so it's statistical significant.

- **Net conversion**: p-value 0.6776 is larger than 0.05, so it's not statistical significant.

In summary, the results of sign tests and effect size tests match.

### <a id="analysis_summary">4.3 Summary</a>

The result of Gross conversion is significant, but Net conversion is not. In other words, after applying the change, enrollments has decreased but payments has no significant change.

## <a id="recommendation">5. Recommendations</a>

Seems both the effect size tests and sign tests indicate the initial hypothesis is correct, that is, the Free Trial Screener could set clearer expectations for students up front, people who spent less than 5 hours per week probably didn't enroll for free trial, that's why Gross conversion decreased. Meanwhile, Net conversion has no significant change means the Free Trial Screener doesn't significantly reduce the number of students that pay for it after the free trial.

However, there are some potential issues:

- What if the decrease in Gross conversion is because some students don't like the Free Trial Screener?
After all, it takes extra steps to finish the enrollment with it.


- Even though Net conversion has no significant change, but it just indicates students pay after 14 days' free trial. We don't know the number of students who complete the course will be increased or decreased.


- Can this change really improve the student experience? Right now we don't see any direct benefits from the test results.

Consequently, I would not recommend launching this Free Trial Screener feature for now. We need to understand the reason behind the decrease in Gross conversion, and we need concrete evidence shows the change is beneficial to Udacity, like improving the student experience or increasing revenue. Let's talk with other team members and the manager about our concerns.

## <a id="follow_up">6. Follow-Up Experiment: Adding Detailed Introductions of Instructors</a>

### Requirement
Give a high-level description of the follow up experiment you would run, what your hypothesis would be, what metrics you would want to measure, what your unit of diversion would be, and your reasoning for these choices.


### Overview of the Design
In this follow-up experiment, I would like to add a detailed introduction page for each instructor, and check if it can increase the click-through-probability on "Enroll now" button, that is, number of unique cookies to click the "Enroll now" button divided by number of unique cookies to view the course overview page. Here is the screeshot of course overview page with "Enroll now" button:

<img src="/figs/udacity-ab-testing/Udacity-AB-Testing-Follow-Up-Experiment-Enroll-Button.png" style="width:50%; margin:auto; display:block"/>

Note: After clicking "Enroll now" button, students need to enter the payment information. There is a 30 day free trial, after that, students will be charged automatically.

**Current**

Currently, each instructor's introduction on the Udacity course overview page is short and very general. Therefore, users do not know how experienced those instructors are in this field, which makes the course less attractive.


**Changes that require A/B testing**

Create a detailed introduction page for each instructor, with more details like:

- What is the instructor's field of expertise?
- How many years has the instructor been working in this field?
- The list of courses he/she instructs
- User ratings of those courses

And add a clickable link on the course overview page that can redirect users to that page. Here is the mock-up of course overview page:
<img src="/figs/udacity-ab-testing/Udacity-AB-Testing-Follow-Up-Experiment-Mockup-1.png" style="width:70%; margin:auto; display:block"/>

The mockup of instructor's introduction page:

<img src="/figs/udacity-ab-testing/Udacity-AB-Testing-Follow-Up-Experiment-Mockup-2.png" style="width:70%; margin:auto; display:block"/>

### Hypothesis

The hypothesis is by adding detailed introduction pages of instructors, user will find those instructors more professional and find the course more credible. Therefore, it can increase the Click-through-probability of "Enroll now" button.

### Unit of Diversion
Cookies

### Metrics

**Invariant Metrics**

Invariant metrics are those happen before the experiment change is trigger.

- **Number of cookies**: That is, number of unique cookies to view the course overview page. ($$d_{min}=3000$$)


**Evaluation Metrics**

Evaluation metrics of this follow-up experiment are very similar to those of Free Trial Screener experiment. Some additional evaluation metrics have been added because the number of clicks may change.

- **Number of clicks**: That is, number of unique cookies to click the "Enroll now" button. ($$d_{min}=240$$)
- **Click-through-probability**: That is, number of unique cookies to click the "Enroll now" button divided by number of unique cookies to view the course overview page. ($$d_{min}=0.01$$)
- **Number of user-ids**: That is, number of users who complete the checkout and enrollment process. ($$d_{min}=50$$)
- **Gross conversion**: That is, number of user-ids to complete the checkout and enrollment, divided by number of unique cookies to click the "Enroll now" button. ($$d_min=0.01$$)
- **Retention**: That is, number of user-ids to remain enrolled past the 30-day boundary (and thus make at least one payment) divided by the number of user-ids to complete checkout. ($$d_{min}=0.01$$)
- **Net conversion**: That is, number of user-ids to remain enrolled past the 30-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Enroll now" button. ($$d_{min}=0.0075$$)
