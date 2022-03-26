# Pharmacy claims
Using SQL to answer business questions about pharmacy claims
## 📌 Overview
### Business needs

The client is an insurance company interested in setting up a test database before rolling out the data wareshouse goes live in production , along with pre-program along with pre-program SQL queries for future analysis and reporting. The developer is given a small sample dataset containing a list of records of pharmacy claims.(see photo below - click to expand image)

### Normalization 

<img width="573" alt="raw dataset" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/raw_data.png>

The raw sample dataset was in its zero normal form because there were multiple entitles/tables nested under one sheet. Each row represents a pharmacy claim transaction containing the name of the patient, the name of the medication filled and billing information. To normalize the dataset from zero normal form to 1NF, I looked for immediate entities, table and keys.

<img width="573" alt="dataset normalization" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/data_org.png>

#### 1NF
An entity is can be thought of the logical aspect of the database where as a table is the physical ascept of the database. A table represents a part of the business function that also links to different part of the organization. The four immediate entities that jump out were members, drugs, copays and insurance paid. I then assigned each attribute from the raw dataset to an entity (i.e. memeber_id would be under the member table). 

The last component for the database to be in its 1NF are primary and foriegn key. To be a primary key, it must contain an unique identifer for each transactional row. Below are the immediate attributes that qualify to be the unique identify for its table: 

1. Member_id is an unique identifer for each patient under the member table
2. Drug_NDC is an unique identifer for each medication under the drug table
3. Drug_form_code is an unique identifer for each drug form under the drug table 
4. Drug_brand_generic_code is an unique identifer for drug brand under the drug table 

A total of two primary keys emerged under the drug table, which I then broke it up into two new tables - drug_form and drug_brand_generic_code. Lastly, 
I created a new table called fill table that contains billing information such as copays and insurance paid with fill_id as the primary key.

#### 2NF 



## :label: Project outcomes
<details>
<summary>
Click to view project outcomes
  
</summary>

* Flawlessly converts raw data into a set of complete and error-free relational tables that meet all 3NF standards. Tables should be either a complete fact or a complete dimensional table.

* Uploads data and creates a complete and error-free star schema in MySQL. Clearly designates the primary and foreign keys. Fully explains in detail the choice to create a primary key as a natural key or a surrogate key using the SQL code. Explains in detail the specific MySQL action with the FKs in case of DELETION or UPDATE. Further select either CASCADE, SET NULL, or RESTRICT for each of the FKs.

* Draws an Entity-Relationship Diagram of your star schema fact and dimension tables. Accurately identifies all the joins types, primary keys, and foreign keys in all of the needed tables.

* Asks appropriate, in-depth and insightful questions to solve a business case. Creates relevant, clear and concise sample queries using SQL. Filters data in the correct format using lead and/or lag functions.
</details>
