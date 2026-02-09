# Mruthula-places-attributes-conflation-model
Project A: Places Attributes Conflation


# Project Workflow
1) Explore given dataset parquet 
    -observe the attributes + understand how it is structured
2) Create golden dataset
    - pick a sample size (i think 500 was the goal, but start off small [maybe 100-200] and firm up those)
    - RNG (need a variety of data w/ diff missing infos but can’t do for every record, try to cover as many cases as possible)
    - objectiveness of manual data cleaning: 
          - manually go thru + decide which of the sources has best attribute for the given place (could be based on most recently updated, which appears more frequently?, go thru Yelp/Google Maps, check for --
    - consistency [does the information appear across multiple web-sources?-->could be indicator of reliable info])
    - can this process be automated? Out of the 2000 records, for golden dataset, how many records is too many/too less for “Golden Dataset”?
3) Create attribute quality rules (for name, address, phone #, email, website, categories)
4) Create rule-based selection algorithm (looking into using DSPy)
5) Evaluation + Metrics




# STEP 1 - EXPLORE GIVEN DATASET

columns present in the dataset: ['id', 'base_id', 'sources', 'names', 'categories', 'confidence','websites', 'socials', 'emails', 'phones', 'brand', 'addresses','base_sources', 'base_names', 'base_categories', 'base_confidence','base_websites', 'base_socials', 'base_emails', 'base_phones','base_brand', 'base_addresses']

source 1 = id (no prefix), source 2 = base_id (has prefix base)
id = identification # for each of the records
sources = shows which dataset info is pulled from, update time, confidence, and internal id number to identify the record, The array of source information for the properties of a given feature, with each entry being a source object which lists the property in JSON Pointer notation and the dataset that specific value came from. All features must have a root level source which is the default source if a specific property's source is not specified.

name = name of the place 
  * potential issues: some names are in diff languages (ex: japanese_restaurant: 143) + special characters 
website = link to place’s website
phones = phone number
  * potential issues: difference arising due to including ph # extensions (+1 vs no +1 for US numbers)
brand = brand name 
category = what type of place it is 
confidence = how confident we are that the place actually exists (0 to 1) 
  [need to ask Overture leads how this is calculated + what role it plays in attribute source selection]
address = address of the business (potentially need to check if all components are present and if the address is complete?)
 * postcodes don’t all have consistent pattern (mix of #s and letters, so may have to group by location/area?)

