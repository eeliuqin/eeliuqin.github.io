---
title: "Healthcare Data Literacy: Healthcare Terms"
category: posts
excerpt: "Notes for Coursera's Health Information Literacy for Data Analytics Specialization Offered by UCDavis"
---

### ICD
Originally published by the WHO in 1959, ICD was short for International Classification of Diseases adapted for indexing hospital records and operational classification. It was developed to help support the development of a standardized classification of hospital records.

ICD-9 was released in 1978 and with 3/4/5 digit code. ICD-10 was adopted in US until Oct 1, 2015. It increased to 7 digit code, which offers more specific disease reporting.
The US utilizes its own national variant of ICD-10: ICD-10-CM (CM means Clinical Modification, over 69,000 codes) for Diagnosis codes in all healthcare settings, ICD-10-PCS (Proceure Coding System, over 70,000 codes) only for hospital inpatient procedures.

### CPT
It means Current Procedural Terminology, it has quite a bit of overlap - 87,000 terms with ICD-10 for procedure codes. CPT codes are the codes used for reporting surgeries and minor procedures and getting paid. When a claim is filed with the CPT procedure code along with the appropriate ICD-10 diagnosis code, payment is made to the providing practitioner.  

### CPT vs. ICD-10-PCS
In some public datasets, we may see both ICD-9-CM (before Oct 01, 2015) and ICD-10-CM (after Oct 01, 2015) for Diagnosis codes and CPT-4 for Procedure codes. It's less common to see ICD-10-PCS, as it's only used in facilities like hospitals to code inpatient procedures, while CPT is used by physicians and healthcare providers to report professional services.

### SNOMED-CT
It means Systematized Nomenclature of Medical Clinical Terms.
ICD-10 is more aligned with SNOMED than ICD-9 (not granular enough), ICD-11 is claimed to be exactly aligned with SNOMED.

### LOINC
Logical Observation Identifiers, Names, and Codes (LOINC) is a standard that facilitates the exchange and pooling of results, such as laboratory tests or vital signs, for clinical care, outcomes management, and research. It's maintained by the [Regenstrief institute](https://www.regenstrief.org/) which is an Indianapolis based research organization, affiliated with Indiana University. LOINC has been endorsed by the American Clinical Laboratory Association and the College of American Pathologists, and identified by [HL7](https://en.wikipedia.org/wiki/Health_Level_7) as a preferred standard.

It's an open source coding system, but it's usually used as an interoperability coding system when data exchange between systems. The [LOINC Website](https://loinc.org/guides/) provides guides to translate the local institution codes to the LOINC standard. However, it's almost never natively within systems and this is a problem.

### SNOMED vs. ICD
Whereas ICD-10 is primarily (though not exclusively) a classification of diseases, SNOMED CT has codes covering not just diseases, but also clinical findings, symptoms, diagnoses, procedures, body structures, organisms and other aetiologies, substances, pharmaceuticals, devices and specimens.

### SNOMED vs. LOINC
According to [this article](https://danielvreeman.com/blog/2015/11/07/guidelines-for-using-loinc-and-snomed-ct-together-without-overlap/), LOINC codes are widely used to identify vital sign observations.
SNOMED CT is a good source of codes for categorical answers to LOINC observables.
In other words, LOINC focuses on questions while SNOMED focuses on anwers.

### AHRQ
The Agency for Healthcare Research and Quality's

AHRQ Quality Indicators:
<table border="1" width="100%">
  <thead>
    <tr style="text-align: right;">
      <th width="30%">Indicator</th>
      <th>Usage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Prevention Quality Indicators (PQIs)</th>
      <td>
      - Use hospital inpatient discharge data, to measure ambulatory care sensitive conditions.<br/>
      - These are conditions for which good outpatient care can potentially prevent the need for
hospitalization, or for which early intervention can prevent complications or more severe disease.
      </td>
    </tr>
    <tr>
      <th> Inpatient Quality Indicators (IQIs) </th>
      <td>- Provide a perspective on quality of care inside hospitals, such as inpatient mortality, and overuse/underuse/misuse of specific procedures </td>
    </tr>
    <tr>
      <th>Patient Safety Indicators (PSIs)</th>
      <td>- Flag potential in-hospital complications and adverse events.</td>
    </tr>
    <tr>
      <th>Pediatric Quality indicators (PDIs)</th>
      <td>- Focus on potentially preventable complications, iatrogenic events, and hospitalizations among patients under the age of 18</td>
    </tr>
  </tbody>
</table>


### HEDIS
Healthcare Effectiveness Data and Information Set, are used by more than 90% of health plans in the US to measure various aspects of health care.
The current version has 92 measures across 6 domains. They are generally process measures and thus most of them are not risk-adjusted.

### Patient Registries
A patient registry is a systematic collection of data about individuals who share a certain disease or condition, like cancer, diabetes, and arthritis, etc. Actually, the patient registry is a database that contains information about patients and their medical history. It can be used to help doctors, nurses, and other healthcare providers make decisions about the best course of treatment for a specific individual or group of people.

### HIM
Health Information Management (HIM): the collection, analysis, storing, protecting and ensuring the quality of patient health information.

HIM professionals may interact with physicians, patients, and insurance companies, but their focus will be on ensuring that health information collected during patient care is properly maintained in accordance with the healthcare organization's established standards, legal requirements, and policies and procedures.
