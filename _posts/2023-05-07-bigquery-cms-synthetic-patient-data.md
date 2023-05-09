---
title: "Analyzing CMS Synthetic Patient Data using SQL"
category: posts
excerpt: "CMS Synthetic Patient Data, SQL, BigQuery"
---

## Introduction

The CMS Synthetic Patient Data OMOP (named `cms_synthetic_patient_data_omop` in Google BigQuery) is a synthetic patient dataset in the [OMOP Common Data Model v5.2](https://ohdsi.github.io/CommonDataModel/), originally released by the [CMS](https://www.cms.gov/) and accessed via BigQuery. The dataset includes 24 tables and records for 2 million synthetic patients from 2008 to 2010. It is intended to be used for research and educational purposes.


<!-- <img src="../assets/images/claims-data-file-structure.png" style="width:65%; margin:auto; display:block"/> -->


## Objective

Analyzing procedures, patients, providers, payers, and drugs using SQL. Figure out the most frequently used drugs related to the coronary artery bypass graft (CABG)

## Import libraries


```python
# Import the necessary libraries
from google.cloud import bigquery
from google.oauth2 import service_account
import pandas as pd
import db_dtypes

# pandas settings to increase readability
pd.options.display.max_seq_items = 1000
pd.options.display.max_rows = 1000 
pd.options.display.max_columns = 200
pd.options.display.max_colwidth = 200
```

## Connect to BigQuery


```python
# Set up your Google Cloud credentials
credentials = service_account.Credentials.from_service_account_file('../assets/creds/google-creds-cms-claims.json')

# Initialize the BigQuery client
client = bigquery.Client(credentials=credentials)
```

## Procedure
### Statistics of Procedure Occurrence


```python
query = """
    SELECT 
        COUNT(DISTINCT provider_id) cnt_provider,
        COUNT(DISTINCT person_id) cnt_patient,
        MIN(procedure_dat) from_date,
        MAX(procedure_dat) to_date,
    FROM `bigquery-public-data.cms_synthetic_patient_data_omop.procedure_occurrence`
"""

df = client.query(query).to_dataframe()
df
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
      <th>cnt_provider</th>
      <th>cnt_patient</th>
      <th>from_date</th>
      <th>to_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>902021</td>
      <td>1979484</td>
      <td>2007-11-27</td>
      <td>2010-12-31</td>
    </tr>
  </tbody>
</table>
</div>



During 2007/11/27-2010/12/31, a total of 902,021 providers and 1,979,484 people are involved in the dataset.

### Top 10 procedure by number of patients


```python
query = """
    SELECT
        domain_id,
        concept_name,
        COUNT(Distinct person_id) cnt_patients
    FROM `bigquery-public-data.cms_synthetic_patient_data_omop.procedure_occurrence` p
    JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.concept` c
        ON p.procedure_concept_id = c.concept_id
    GROUP BY 1,2
    ORDER BY 3 DESC
    LIMIT 10
"""

df = client.query(query).to_dataframe()
df
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
      <th>domain_id</th>
      <th>concept_name</th>
      <th>cnt_patients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Procedure</td>
      <td>Office or other outpatient visit for the evaluation and management of an established patient, which requires at least 2 of these 3 key components: An expanded problem focused history; An expanded ...</td>
      <td>1718733</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Procedure</td>
      <td>Other diagnostic procedures on lymphatic structures</td>
      <td>1689905</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Procedure</td>
      <td>Collection of venous blood by venipuncture</td>
      <td>1674947</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Procedure</td>
      <td>Office or other outpatient visit for the evaluation and management of an established patient, which requires at least 2 of these 3 key components: A detailed history; A detailed examination; Medic...</td>
      <td>1652934</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Procedure</td>
      <td>Biopsy of lymphatic structure</td>
      <td>1625521</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Procedure</td>
      <td>Biopsy of mouth, unspecified structure</td>
      <td>1562999</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Metadata</td>
      <td>No matching concept</td>
      <td>1487928</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Procedure</td>
      <td>Long-term drug therapy</td>
      <td>1303593</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Procedure</td>
      <td>Office or other outpatient visit for the evaluation and management of an established patient, which requires at least 2 of these 3 key components: A problem focused history; A problem focused exam...</td>
      <td>1271717</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Procedure</td>
      <td>Subsequent hospital care, per day, for the evaluation and management of a patient, which requires at least 2 of these 3 key components: An expanded problem focused interval history; An expanded pr...</td>
      <td>1199980</td>
    </tr>
  </tbody>
</table>
</div>



The top 1 procedure is Office or other outpatient visit related. 
What about more specific procedures? For example, procedures like **Coronary artery bypass**

### Coronary Artery Bypass Grafting (CABG) Procedures


```python
query = """
    SELECT
        concept_id,
        concept_name,
        vocabulary_id,
        COUNT(Distinct person_id) cnt_patients
    FROM `bigquery-public-data.cms_synthetic_patient_data_omop.procedure_occurrence` p
    JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.concept` c
        ON p.procedure_concept_id = c.concept_id
    WHERE LOWER(concept_name) LIKE '%coronary artery bypass%'
    GROUP BY 1,2,3
    ORDER BY 4 DESC
    LIMIT 10
"""

df = client.query(query).to_dataframe()
df
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
      <th>concept_id</th>
      <th>concept_name</th>
      <th>vocabulary_id</th>
      <th>cnt_patients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2107231</td>
      <td>Coronary artery bypass, using arterial graft(s); single arterial graft</td>
      <td>CPT4</td>
      <td>15077</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2107214</td>
      <td>Endoscopy, surgical, including video-assisted harvest of vein(s) for coronary artery bypass procedure (List separately in addition to code for primary procedure)</td>
      <td>CPT4</td>
      <td>8104</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2107223</td>
      <td>Coronary artery bypass, using venous graft(s) and arterial graft(s); 2 venous grafts (List separately in addition to code for primary procedure)</td>
      <td>CPT4</td>
      <td>5199</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2100873</td>
      <td>Anesthesia for direct coronary artery bypass grafting; with pump oxygenator</td>
      <td>CPT4</td>
      <td>5189</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2107224</td>
      <td>Coronary artery bypass, using venous graft(s) and arterial graft(s); 3 venous grafts (List separately in addition to code for primary procedure)</td>
      <td>CPT4</td>
      <td>3670</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2107222</td>
      <td>Coronary artery bypass, using venous graft(s) and arterial graft(s); single vein graft (List separately in addition to code for primary procedure)</td>
      <td>CPT4</td>
      <td>2787</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2100872</td>
      <td>Anesthesia for direct coronary artery bypass grafting; without pump oxygenator</td>
      <td>CPT4</td>
      <td>2270</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2107230</td>
      <td>Reoperation, coronary artery bypass procedure or valve procedure, more than 1 month after original operation (List separately in addition to code for primary procedure)</td>
      <td>CPT4</td>
      <td>1659</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2001514</td>
      <td>Single internal mammary-coronary artery bypass</td>
      <td>ICD9Proc</td>
      <td>1471</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2107226</td>
      <td>Coronary artery bypass, using venous graft(s) and arterial graft(s); 4 venous grafts (List separately in addition to code for primary procedure)</td>
      <td>CPT4</td>
      <td>1264</td>
    </tr>
  </tbody>
</table>
</div>



There are many procedures related to CABG. I'm going to select the concept_id 2107231 as it has the most patients.

## Patient

### Age Range


```python
cte_age = """
/* Get birth date of patients */

WITH person_bdate AS (
  SELECT
    person_id,
    CAST(CONCAT(year_of_birth,'-',month_of_birth,'-',day_of_birth) AS date) AS birth_date
  FROM `bigquery-public-data.cms_synthetic_patient_data_omop.person`
),

/* Get age of patients */

procedure_person_age AS (
  SELECT
    p.person_id,
    CAST(AVG(DATE_DIFF(p.procedure_dat, b.birth_date, year)) AS INT) AS age_at_procedure
  FROM `bigquery-public-data.cms_synthetic_patient_data_omop.procedure_occurrence` AS p
  JOIN person_bdate b
    ON p.person_id = b.person_id
  GROUP BY 1
)
"""

query = cte_age + \
"""
SELECT
    COUNT(DISTINCT person_id) cnt_patients, 
    MIN(age_at_procedure) min_age, 
    APPROX_QUANTILES(age_at_procedure, 100)[OFFSET(24)] as p25_age,
    APPROX_QUANTILES(age_at_procedure, 100)[OFFSET(49)] as p50_age,
    APPROX_QUANTILES(age_at_procedure, 100)[OFFSET(74)] as p75_age,
    MAX(age_at_procedure) max_age
FROM procedure_person_age

"""

df = client.query(query).to_dataframe()
df
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
      <th>cnt_patients</th>
      <th>min_age</th>
      <th>p25_age</th>
      <th>p50_age</th>
      <th>p75_age</th>
      <th>max_age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1979484</td>
      <td>25</td>
      <td>67</td>
      <td>73</td>
      <td>81</td>
      <td>101</td>
    </tr>
  </tbody>
</table>
</div>



75% of patients are older than 67 because it's a Medicare (for the eld population 65+ years old) dataset.

### Gender/Ethnicity Ratio


```python
cte_gender_eth = """
WITH person_gender AS (
    SELECT 
        p.person_id, 
        c.concept_name AS gender, 
        ethnicity_concept_id
    FROM `bigquery-public-data.cms_synthetic_patient_data_omop.procedure_occurrence` p
    JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.person` pe
        ON pe.person_id = p.person_id
    JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.concept` c
        ON c.concept_id = pe.gender_concept_id
),
person_eth AS (
    SELECT 
        g.person_id, 
        c.concept_name AS ethnicity
    FROM person_gender g
    JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.concept` c
        ON c.concept_id = g.ethnicity_concept_id
)
"""

gender_query = cte_gender_eth +\
"""
    SELECT
        gender,
        cnt,
        ROUND(cnt/(SUM(cnt) OVER ())*100, 2) percent_patients
    FROM (
    SELECT 
        gender, 
        COUNT(DISTINCT person_id) AS cnt
    FROM person_gender
    GROUP BY gender
    ) tmp
"""

# get count of patients by gender
df_gender = client.query(gender_query).to_dataframe()
df_gender
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
      <th>gender</th>
      <th>cnt</th>
      <th>percent_patients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MALE</td>
      <td>850727</td>
      <td>42.98</td>
    </tr>
    <tr>
      <th>1</th>
      <td>FEMALE</td>
      <td>1128757</td>
      <td>57.02</td>
    </tr>
  </tbody>
</table>
</div>




```python
eth_query = cte_gender_eth +\
"""
    SELECT
        ethnicity,
        cnt,
        ROUND(cnt/(SUM(cnt) OVER ())*100, 2) percent_patients
    FROM (
    SELECT 
        ethnicity, 
        COUNT(DISTINCT person_id) AS cnt
    FROM person_eth
    GROUP BY ethnicity
    ) tmp
"""

# get count of patients by gender
df_eth = client.query(eth_query).to_dataframe()
df_eth
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
      <th>ethnicity</th>
      <th>cnt</th>
      <th>percent_patients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Not Hispanic or Latino</td>
      <td>1935032</td>
      <td>97.75</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Hispanic or Latino</td>
      <td>44452</td>
      <td>2.25</td>
    </tr>
  </tbody>
</table>
</div>



### Patients that Visit Multiple Providers

Patients with complex medical conditions may visit multiple providers.


```python
query = """
SELECT 
    person_id,
    COUNT(DISTINCT provider_id) cnt_providers
FROM `bigquery-public-data.cms_synthetic_patient_data_omop.procedure_occurrence` p
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10
"""

df = client.query(query).to_dataframe()
df
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
      <th>person_id</th>
      <th>cnt_providers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>54123</td>
      <td>246</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1986824</td>
      <td>232</td>
    </tr>
    <tr>
      <th>2</th>
      <td>281743</td>
      <td>230</td>
    </tr>
    <tr>
      <th>3</th>
      <td>415512</td>
      <td>230</td>
    </tr>
    <tr>
      <th>4</th>
      <td>935946</td>
      <td>230</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1864049</td>
      <td>226</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2275872</td>
      <td>225</td>
    </tr>
    <tr>
      <th>7</th>
      <td>111683</td>
      <td>224</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1553227</td>
      <td>223</td>
    </tr>
    <tr>
      <th>9</th>
      <td>1891594</td>
      <td>220</td>
    </tr>
  </tbody>
</table>
</div>



The count of providers seems too large to be true. That's probably because it's a synthetic dataset.

## Provider

### Top 10 Providers by Average Daily Patient Count


```python
query = """
WITH patients_daily AS (
    SELECT 
        npi, 
        procedure_dat,
        COUNT(DISTINCT person_id) cnt_patients
    FROM `bigquery-public-data.cms_synthetic_patient_data_omop.procedure_occurrence` p
    JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.provider` pro
        ON pro.provider_id = p.provider_id
    GROUP BY 1,2
    ORDER BY 3 DESC
)

SELECT
    npi,
    CAST(CEILING(AVG(cnt_patients)) AS INT) avg_daily_cnt_patients
FROM patients_daily d
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10

"""

df = client.query(query).to_dataframe()
df
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
      <th>npi</th>
      <th>avg_daily_cnt_patients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0888901330</td>
      <td>502</td>
    </tr>
    <tr>
      <th>1</th>
      <td>8207899456</td>
      <td>365</td>
    </tr>
    <tr>
      <th>2</th>
      <td>9979265126</td>
      <td>333</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1973668724</td>
      <td>287</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2052541087</td>
      <td>280</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1052105939</td>
      <td>250</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7961400834</td>
      <td>230</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2866295111</td>
      <td>218</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0941734431</td>
      <td>212</td>
    </tr>
    <tr>
      <th>9</th>
      <td>3552796390</td>
      <td>210</td>
    </tr>
  </tbody>
</table>
</div>



The dataset does not provide further details such as provider names, but considering the number of patients per day, the top 10 providers are probably hospitals.

### CABG Providers

We've known the `concept_id` for CABG is 2107231.


```python
query = """
    SELECT 
        npi,
        COUNT(DISTINCT p.person_id) cabg_cnt_patients
    FROM `bigquery-public-data.cms_synthetic_patient_data_omop.procedure_occurrence` p
    JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.provider` pro
        ON p.provider_id = pro.provider_id
    WHERE p.procedure_concept_id = 2107231
    GROUP BY 1
    ORDER BY 2 DESC
"""

df = client.query(query).to_dataframe()
df
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
      <th>npi</th>
      <th>cabg_cnt_patients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0888901330</td>
      <td>86</td>
    </tr>
    <tr>
      <th>1</th>
      <td>9979265126</td>
      <td>78</td>
    </tr>
    <tr>
      <th>2</th>
      <td>8207899456</td>
      <td>67</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2052541087</td>
      <td>58</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1973668724</td>
      <td>52</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>12849</th>
      <td>5326588845</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12850</th>
      <td>6529848843</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12851</th>
      <td>0211677309</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12852</th>
      <td>8731816368</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12853</th>
      <td>2994512661</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>12854 rows × 2 columns</p>
</div>



Among 902021 providers, 12855 (1.4%) of them can provide CABG service. The provider with npi 0888901330 provided that service to 86 patients.

## Payer

Which payer plan has the most beneficiaries? What's the corresponding averge age of beneficiaries?



```python
query = """
WITH payer_cnt AS (
    SELECT
        plan_source_value,
        COUNT(DISTINCT p.person_id) cnt_beneficiaries
    FROM `bigquery-public-data.cms_synthetic_patient_data_omop.procedure_occurrence` p
    JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.payer_plan_period` pa
        ON p.person_id = pa.person_id
    GROUP BY 1
)

SELECT 
    plan_source_value, 
    cnt_beneficiaries,
    ROUND(cnt_beneficiaries/(SUM(cnt_beneficiaries) OVER ())*100, 2) percent_beneficiaries
FROM payer_cnt
ORDER BY percent_beneficiaries DESC
"""

df = client.query(query).to_dataframe()
df
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
      <th>plan_source_value</th>
      <th>cnt_beneficiaries</th>
      <th>percent_beneficiaries</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Medicare Part B</td>
      <td>1976387</td>
      <td>29.96</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Medicare Part A</td>
      <td>1972066</td>
      <td>29.89</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Medicare Part D</td>
      <td>1905775</td>
      <td>28.89</td>
    </tr>
    <tr>
      <th>3</th>
      <td>HMO</td>
      <td>742521</td>
      <td>11.26</td>
    </tr>
  </tbody>
</table>
</div>



## Drug

### Top 10 Drugs by Frequency


```python
query = """
SELECT 
    concept_name,
    COUNT(DISTINCT d.visit_occurrence_id) cnt_visits
FROM `bigquery-public-data.cms_synthetic_patient_data_omop.drug_exposure` d
JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.concept` c
ON c.concept_id = d.drug_concept_id
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10
"""

df = client.query(query).to_dataframe()
df
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
      <th>concept_name</th>
      <th>cnt_visits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Influenza virus vaccine, trivalent (IIV3), split virus, 0.5 mL dosage, for intramuscular use</td>
      <td>1423956</td>
    </tr>
    <tr>
      <th>1</th>
      <td>No matching concept</td>
      <td>1105382</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Epoetin Alfa</td>
      <td>488914</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sodium Chloride Injectable Solution</td>
      <td>415049</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vitamin B 12 1 MG</td>
      <td>358829</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Dexamethasone 1 MG</td>
      <td>327797</td>
    </tr>
    <tr>
      <th>6</th>
      <td>paricalcitol Injectable Solution</td>
      <td>314188</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Midazolam</td>
      <td>301279</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Triamcinolone</td>
      <td>283925</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Ondansetron</td>
      <td>275889</td>
    </tr>
  </tbody>
</table>
</div>



The drug with most visits is Influenza virus vaccine.

### Drugs related to CABG


```python
query = """
    SELECT 
        concept_name AS drug,
        COUNT(DISTINCT drug_exposure_id) cabg_cnt_visits
    FROM `bigquery-public-data.cms_synthetic_patient_data_omop.procedure_occurrence` p
    JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.drug_exposure` d
        ON p.visit_occurrence_id = d.visit_occurrence_id
    JOIN `bigquery-public-data.cms_synthetic_patient_data_omop.concept` c
        ON c.concept_id = d.drug_concept_id
    WHERE p.procedure_concept_id = 2107231
    GROUP BY 1
    ORDER BY 2 DESC
"""

df = client.query(query).to_dataframe()
df
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
      <th>drug</th>
      <th>cabg_cnt_visits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Influenza virus vaccine, trivalent (IIV3), split virus, 0.5 mL dosage, for intramuscular use</td>
      <td>112</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Adenosine Triphosphate</td>
      <td>95</td>
    </tr>
    <tr>
      <th>2</th>
      <td>regadenoson</td>
      <td>85</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sodium Chloride Injectable Solution</td>
      <td>71</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Dipyridamole Injectable Solution</td>
      <td>57</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Triamcinolone</td>
      <td>52</td>
    </tr>
    <tr>
      <th>6</th>
      <td>No matching concept</td>
      <td>46</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Vitamin B 12 1 MG</td>
      <td>46</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Dexamethasone 1 MG</td>
      <td>45</td>
    </tr>
    <tr>
      <th>9</th>
      <td>methylprednisolone acetate 40 MG</td>
      <td>29</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Aminophylline 250 MG</td>
      <td>28</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Methylprednisolone</td>
      <td>28</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Pneumococcal polysaccharide vaccine, 23-valent (PPSV23), adult or immunosuppressed patient dosage, when administered to individuals 2 years or older, for subcutaneous or intramuscular use</td>
      <td>15</td>
    </tr>
    <tr>
      <th>13</th>
      <td>darbepoetin alfa</td>
      <td>13</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Dobutamine Injectable Solution</td>
      <td>13</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Ketorolac</td>
      <td>12</td>
    </tr>
    <tr>
      <th>16</th>
      <td>tetanus toxoid vaccine, inactivated</td>
      <td>11</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Promethazine Hydrochloride 50 MG</td>
      <td>11</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Betamethasone</td>
      <td>11</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Ceftriaxone 250 MG Injection</td>
      <td>11</td>
    </tr>
    <tr>
      <th>20</th>
      <td>diphtheria toxoid vaccine, inactivated</td>
      <td>11</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Granisetron Injectable Solution</td>
      <td>8</td>
    </tr>
    <tr>
      <th>22</th>
      <td>heparin</td>
      <td>8</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Epoetin Alfa</td>
      <td>6</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Perflutren</td>
      <td>6</td>
    </tr>
    <tr>
      <th>25</th>
      <td>rituximab Injection</td>
      <td>6</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Influenza virus vaccine, trivalent (IIV3), split virus, preservative free, 0.5 mL dosage, for intramuscular use</td>
      <td>6</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Morphine Sulfate 10 MG</td>
      <td>5</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Leucovorin Injectable Solution</td>
      <td>5</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Midazolam</td>
      <td>5</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Lidocaine 10 MG</td>
      <td>5</td>
    </tr>
    <tr>
      <th>31</th>
      <td>docetaxel Injectable Solution</td>
      <td>4</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Diphenhydramine Citrate 50 MG</td>
      <td>4</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Testosterone</td>
      <td>4</td>
    </tr>
    <tr>
      <th>34</th>
      <td>sargramostim Injectable Solution</td>
      <td>3</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Estradiol Injectable Solution [Depo-estradiol]</td>
      <td>3</td>
    </tr>
    <tr>
      <th>36</th>
      <td>hyaluronate</td>
      <td>3</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Leuprolide</td>
      <td>3</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Atropine</td>
      <td>3</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Tetanus and diphtheria toxoids adsorbed (Td), preservative free, when administered to individuals 7 years or older, for intramuscular use</td>
      <td>3</td>
    </tr>
    <tr>
      <th>40</th>
      <td>Fentanyl 0.1 MG</td>
      <td>3</td>
    </tr>
    <tr>
      <th>41</th>
      <td>fulvestrant Prefilled Syringe</td>
      <td>3</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Ranitidine Injectable Solution</td>
      <td>2</td>
    </tr>
    <tr>
      <th>43</th>
      <td>pegfilgrastim Prefilled Syringe</td>
      <td>2</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Cyclophosphamide</td>
      <td>2</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Theophylline Injectable Solution</td>
      <td>2</td>
    </tr>
    <tr>
      <th>46</th>
      <td>bevacizumab Injection</td>
      <td>2</td>
    </tr>
    <tr>
      <th>47</th>
      <td>abatacept</td>
      <td>2</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Levalbuterol</td>
      <td>2</td>
    </tr>
    <tr>
      <th>49</th>
      <td>cetuximab Injection</td>
      <td>2</td>
    </tr>
    <tr>
      <th>50</th>
      <td>Filgrastim</td>
      <td>2</td>
    </tr>
    <tr>
      <th>51</th>
      <td>Meperidine Hydrochloride 100 MG</td>
      <td>2</td>
    </tr>
    <tr>
      <th>52</th>
      <td>Gentamicin Sulfate (USP)</td>
      <td>2</td>
    </tr>
    <tr>
      <th>53</th>
      <td>zoledronic acid Injectable Solution [Zometa]</td>
      <td>2</td>
    </tr>
    <tr>
      <th>54</th>
      <td>Atropine Sulfate 0.3 MG</td>
      <td>2</td>
    </tr>
    <tr>
      <th>55</th>
      <td>Zoster (shingles) vaccine (HZV), live, for subcutaneous injection</td>
      <td>2</td>
    </tr>
    <tr>
      <th>56</th>
      <td>Nalbuphine</td>
      <td>2</td>
    </tr>
    <tr>
      <th>57</th>
      <td>Doxorubicin Injectable Solution</td>
      <td>1</td>
    </tr>
    <tr>
      <th>58</th>
      <td>Budesonide</td>
      <td>1</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Sodium ferric gluconate complex Injectable Solution</td>
      <td>1</td>
    </tr>
    <tr>
      <th>60</th>
      <td>hyaluronate Prefilled Syringe [Orthovisc]</td>
      <td>1</td>
    </tr>
    <tr>
      <th>61</th>
      <td>Epinephrine</td>
      <td>1</td>
    </tr>
    <tr>
      <th>62</th>
      <td>Ibandronate</td>
      <td>1</td>
    </tr>
    <tr>
      <th>63</th>
      <td>Testosterone 100 MG</td>
      <td>1</td>
    </tr>
    <tr>
      <th>64</th>
      <td>Histrelin acetate 0.00208 MG/HR Drug Implant [Vantas]</td>
      <td>1</td>
    </tr>
    <tr>
      <th>65</th>
      <td>Cefotaxime 1000 MG Injection</td>
      <td>1</td>
    </tr>
    <tr>
      <th>66</th>
      <td>Tetanus, diphtheria toxoids and acellular pertussis vaccine (Tdap), when administered to individuals 7 years or older, for intramuscular use</td>
      <td>1</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Immunoglobulin G Injectable Solution</td>
      <td>1</td>
    </tr>
    <tr>
      <th>68</th>
      <td>Doxorubicin</td>
      <td>1</td>
    </tr>
    <tr>
      <th>69</th>
      <td>Lincomycin Injectable Solution</td>
      <td>1</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Lorazepam 2 MG</td>
      <td>1</td>
    </tr>
    <tr>
      <th>71</th>
      <td>Mepivacaine</td>
      <td>1</td>
    </tr>
    <tr>
      <th>72</th>
      <td>Ipratropium</td>
      <td>1</td>
    </tr>
    <tr>
      <th>73</th>
      <td>Magnesium Sulfate</td>
      <td>1</td>
    </tr>
    <tr>
      <th>74</th>
      <td>Glucose</td>
      <td>1</td>
    </tr>
    <tr>
      <th>75</th>
      <td>Ondansetron</td>
      <td>1</td>
    </tr>
    <tr>
      <th>76</th>
      <td>alteplase Injectable Solution</td>
      <td>1</td>
    </tr>
    <tr>
      <th>77</th>
      <td>Kanamycin Injectable Solution</td>
      <td>1</td>
    </tr>
    <tr>
      <th>78</th>
      <td>BCG, Live, Tice Strain</td>
      <td>1</td>
    </tr>
    <tr>
      <th>79</th>
      <td>Carboplatin Injectable Solution</td>
      <td>1</td>
    </tr>
    <tr>
      <th>80</th>
      <td>bortezomib Injectable Solution</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



Interestingly, the top 1 drug for Coronary Artery Bypass Grafting is **Influenza virus vaccine**, why? After do some research, I found [National Library of Medicine (NIH)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC387429/) reported there is a significant benefit of vaccination against influenza in patients hospitalized due to an acute coronary event.

Therefore, when the drug is a influenza virus vaccine, we should realize that the patient's illness is not necessarily flu.
