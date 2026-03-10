#Place Attribute Conflation – Golden Dataset Construction & Evaluation
#Project Overview

This project focuses on creating a reliable ground-truth (Golden Dataset) to improve the accuracy of place attribute conflation across multiple datasets.

The goal is to evaluate how well a rule-based or ML-based attribute selection system can determine the most accurate value for business place attributes when multiple sources provide conflicting or incomplete information.

##The workflow includes:

- Exploring the provided parquet dataset

- Understanding attribute structure and potential conflicts

- Creating a manually verified Golden Dataset

- Defining attribute quality rules

- Building a rule-based selection algorithm

- Evaluating algorithm performance against the ground truth

Exploring DSPy for ML-based attribute selection

Project Workflow
Step 1 — Explore Given Dataset

The provided dataset contains the following columns:

['id', 'base_id', 'sources', 'names', 'categories', 'confidence',
 'websites', 'socials', 'emails', 'phones', 'brand', 'addresses',
 'base_sources', 'base_names', 'base_categories', 'base_confidence',
 'base_websites', 'base_socials', 'base_emails', 'base_phones',
 'base_brand', 'base_addresses']
Dataset Structure

Two primary sources are provided for each record:

Source 1

id

Associated attributes without prefix

Source 2

base_id

Attributes with base_ prefix

Example:

names vs base_names
phones vs base_phones
addresses vs base_addresses
Key Attributes

id
Identification number for each record.

sources
Shows which dataset the information was pulled from, including:

update time

confidence score

internal source id

The array of source information for the properties of a given feature lists the property in JSON Pointer notation and the dataset that specific value came from.

All features must have a root level source which is the default source if a specific property's source is not specified.

Attribute Descriptions

name
Name of the place.

Potential issues:

Names in different languages
Example: japanese_restaurant: 京都御所

Special characters or diacritics.

website
Link to the place's website.

phones
Phone numbers associated with the place.

Potential issues:

Formatting differences

Extensions

+1 vs no +1 prefix for US numbers.

brand
Brand name of the place.

categories
Category describing what type of place it is.

confidence
Score between 0 and 1 indicating how confident we are that the place actually exists.

Open question:

Need to ask Overture leads how this confidence is calculated and what role it plays in attribute source selection.

address
Address of the business.

Potential issues:

Missing components

Incomplete addresses

Inconsistent postal code formatting

Observations From Dataset Exploration

Some fields are frequently empty.

Examples:

emails

websites

Based on discussion with other Group A project members:

base_emails appears to only contain data for roughly 50% of records.

This leads to a potential rule for the selection algorithm:

If one source has an empty attribute and the other contains information, default to selecting the non-empty value.

Golden Dataset Construction
Sample Size

The original goal was:

500 records

However, the approach is currently:

Start with 100–200 records

Refine labeling guidelines

Expand to a final dataset

Random sampling (RNG) will be used to ensure variety across records, including:

Missing attributes

Conflicting sources

Different formatting cases

However, it is not feasible to manually account for every possible case, so the goal is to cover as many realistic scenarios as possible.

Manual Data Cleaning Process

The Golden Dataset is constructed through manual verification.

Process:

Select record

Review all source attributes

Verify externally if necessary
(Google Maps, Yelp, official websites)

Record the correct value

Document reasoning

Sources used for verification:

Yelp

Google Maps

Official business websites

Indicators of reliable information include:

Information appearing consistently across multiple sources

Official business website confirmation

Most recently updated data

Highest completeness of fields

Attribute-Level Labeling (Golden Dataset Format)

This format allows models to learn how to choose between multiple sources.

Example:

Record_ID	Attribute	Selected_Value	Selected_Source	Correct?	Reason
1001	Name	Starbucks	Yelp	TRUE	Cleanest canonical name
1001	Phone	925-555-1000	Yelp	TRUE	Majority + matches official site
1001	Website	https://www.starbucks.com
	Google	TRUE	Valid formatted URL
1001	Category	Coffee Shop	Yelp	TRUE	More specific than "Cafe"

This format helps the ML model learn how to select attributes from competing sources.

Attribute Selection Guidelines
General Selection Principles

Prefer official business website data

Prefer majority agreement across sources

Normalize formatting before comparison

Verify conflicting values externally

Record reasoning for each selection

Golden Record Pick Logic
Name
Scenario	Rule	Notes
Single language	Pick it	Example: Valley Transmission
Native script + English transliteration combined	Pick combined field	Example: 京都御所 Kyoto Imperial Palace
Native script only	Pick combined if exists, else native	Improves accessibility
Multiple identical names	Pick preferred source	Remove location suffixes
Address
Scenario	Rule
One source has postal code	Pick source with postal code
Both have postal codes	Pick preferred dataset
Street completeness differs	Pick more complete address
Missing optional info	Pick first-in-list or preferred source

Example:

7643 Gate Pkwy Ste 104  >  7643 Gate Pkwy
Phone
Scenario	Rule
Multiple formats	Pick E.164 formatted number
Different numeric lengths	Pick longest numeric
One formatted	Prefer formatted

Example:

+19049989600 > 9049989600
Category
Scenario	Rule
Different categories	Pick controlled taxonomy
Both valid	Pick preferred dataset

Example:

shipping_center preferred over vehicle_shipping
Website
Scenario	Rule
Same domain	Pick canonical full URL
Multiple domains	Pick official domain
Social or redirect links	Avoid

Example:

https://www.goinpostaljacksonville.com
Email
Scenario	Rule
Multiple emails	Pick most official
Personal vs corporate	Prefer corporate
Social Accounts
Scenario	Rule
Multiple accounts	Pick official account
Fan pages	Avoid
Deterministic Selection Rules (Quick Reference)

Postal code / structured fields > transliteration > key order

Native + English names preferred when available

E.164 phone format preferred

Controlled taxonomy for categories

Preferred dataset or first-in-list as tie-breaker

Remove location suffixes unless part of official name

Preserve native spelling and diacritics when confirmed

Current Project Status

Progress so far:

Defined top 5 attributes for the golden dataset

Performed a deep dive into the parquet dataset

Identified potential attribute conflicts

Created attribute selection rules

Began manual golden dataset labeling

Added a notes column explaining reasoning for attribute choices

The expectation is that this reasoning column will assist in model training and decision processes, although this has not yet been tested.

Challenges

The main challenge is that the manual dataset construction approach is time-intensive.

Creating one golden record requires:

Reviewing multiple sources

Cross-checking external websites

Verifying formatting and consistency

Another issue encountered was spending too much time understanding the dataset, which delayed labeling and initial testing.

Now that guidelines are established, labeling should proceed faster.

Machine Learning Approach (DSPy)

The professor and a previous presenter (Drew) mentioned DSPy as a potential approach.

An article was found discussing the use of DSPy in a similar attribute selection setting, so the next step is to explore:

DSPy documentation

Syntax and signatures

Model training using the golden dataset

A Colab notebook is currently in progress for implementing this approach.

Refining Project Scope

Due to time constraints, the golden dataset size will likely be adjusted.

Original plan:

1000 records

Revised estimate:

250 – 300 records

The focus will be on higher-quality labeled records rather than quantity.

Evaluation Metrics

Original accuracy goal:

90%

Based on previous repositories and results in similar projects, performance has generally been in the 80% range.

Updated goal:

> 80% attribute selection accuracy
Original OKRs
Objective 1

Create a reliable ground-truth dataset to improve accuracy of place attributes conflation.

KR1
Produce a labelled dataset of 1000 places with attributes and confidence score ≥95%.

KR2
Identify and define 6 primary place attributes for the ground-truth dataset.

Objective 2

Deliver accurate and up-to-date information for businesses, stores, and places of interest.

KR1
Define and document 3 selection criteria for determining the highest attribute accuracy among sources.

KR2
Reach 90% accuracy between algorithm-selected attributes and the ground-truth place.

Updated Key Results

Adjustments were made due to project timeline and manual labeling complexity.

Updated targets:

Golden dataset size: 250–300 records

Target model accuracy: >80%

These refinements help balance:

Dataset quality

Model evaluation

Time constraints

Strategic Next Steps

Continue expanding the golden dataset

Ensure representation of edge cases

Implement DSPy model training

Evaluate attribute selection accuracy

Add more labeled records if necessary to improve model performance

These steps ensure progress toward both project objectives:

creating a high-quality ground truth dataset

building a reliable attribute selection model.

Resources
Raw Data + Golden Dataset

https://docs.google.com/spreadsheets/d/1cHw_81_H6qFIbxIOWbuwtnVEPTYy7FJj3x0jPNgw6Qo/edit?gid=990629450#gid=990629450

DSPy Colab Notebook

https://colab.research.google.com/drive/1GCtyLh8WI6Hp6zlTg5jJdmPh5D8Ow-IB?usp=sharing
