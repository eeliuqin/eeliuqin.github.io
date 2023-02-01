---
title: "Healthcare Data Models: Verification and Validation of Data"
category: posts
excerpt: "Notes for Coursera's Health Information Literacy for Data Analytics Specialization Offered by UCDavis"
---

This is my study notes for [Healthcare Data Models](https://www.coursera.org/learn/healthcare-data-models).

### Data validation vs. data verification

Both verification and validation contribute to usable data. Data validation ensures the data falls within the expected range of values, while data verification ensures that data is accurate and consistent.

Validation focuses on the alignment of data with outside values, standards and benchmarks.
Verification focuses on how data matches internal practices and processes.

**Examples**

Entering 500 lbs for baby weight is a data validation error.<br/>
Entering weight in kilograms when the system requires it to be pounds is a data validation error.

### Why quality levels need to be adjusted if data is repurposed?

Repurposing data happens often, e.g.: looking at billing data to discover what treatment has been provided.
However, data gathered for one purpose may be not accurate for other analyses that we wish to draw it from.
As data analysts, we will need to make sure it's of sufficient quality to support the new purpose.

### Method to eliminate data errors
Whenever we see issues like wrong data format, data that doesn't conform to expectations, and incomplete data, we can trace them back to the source and try to fix it.