# Pharmacy claims
Using SQL to answer business questions about pharmacy claims
## 📌 Overview
### Business needs

The client is an insurance company interested in setting up a test database before the data wareshouse goes live in production , along with pre-program along with pre-program SQL queries for future analysis and reportings. The developer is given a small sample dataset containing a list of pharmacy claims records.(see photo below - click to expand image)

### Normalization 

<img width="573" alt="raw dataset" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/raw_data.png>

The raw sample dataset was in its zero normal form because there were multiple entitles/tables nested under one sheet. Each row represents a pharmacy claim transaction containing the name of the patient, the name of the medication filled and billing information. To normalize the dataset from zero normal form to 1NF, I looked for immediate entities, table and keys.

<img width="573" alt="dataset normalization" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/data_org.png>

#### 1NF and primary key set up 
An entity is the logical aspect of the database, whereas a table is the physical aspect of the database. A table represents a part of the business function that links to a different part of the organization. The four immediate entities that jumped out were members, drugs, copays, and insurance. I then assigned each attribute from the raw dataset to one of the four entities (i.e., memeber_id would be under the member table). 

The last component for the database in its 1NF is the primary and foreign keys. Each transactional row must contain a unique identifier. Below are the immediate attributes that qualify to be the unique identifier or primary key for its table: 

1. Member_id is the primary key for each patient under the member table. In this example, the primary key is a natural key because the unique identifier was already there in the raw dataset.
2. Drug_NDC is the primary key for each medication under the drug table. In this example, the primary key is a natural key because the unique identifier was already there in the raw dataset.
3. Drug_form_code is the primary key for each drug form under the drug table. In this example, the primary key is a natural key because the unique identifier was already there in the raw dataset.
4. Drug_brand_generic_code is the primary key for drug brand form under the drug table. In this example, the primary key is a natural key because the unique identifier was already there in the raw dataset.

A total of two primary keys emerged under the drug table. For each table to be in its 2NF, there shouldn't be any hidden entities. I broke the drug table into two new tables - drug_form and drug_brand_generic_code. 

Lastly, I created a new fill table, which contains billing information such as copays and insurance paid with fill_id as the primary key. 
In this example, the primary key is a surrogate key because I had to make up a unique identifier for the table. The FILL_ID doesn't have any business, so FILL_ID is a surrogate key.

#### 2NF and 3NF
After the transformation, each table was in its 3NF. There were no hidden entities, no functional dependencies, and attributes depending on its primary key.

#### Fact table (i.e. fill table)

The grain of the fill table is that each row in the transaction fact table represents a record of prescription fill.

COPAY_AMOUNT and INSURANCE_PAID are facts, while FILL_DATE is a dimension. COPAY_AMOUNT is an additive fact because I can add them up with the FILL_DATE dimension (what is the total copay amount for all the dates?). INSURANCE_PAID is also an additive fact because I can add up insurance paid for all the dates or selected dates.

<img width="573" alt="star schema" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/star_scheme.png>

### Foreign keys, CASCADE, SET NULL or RESTRICT 

The image above shows an ERD in the form of a star schema. Much like a star structure, the fill table is the centerpiece where it connects to the rest of the tables and vice versa with foreign keys. Each foreign key acts as a connector between the tables, where each foreign key is the primary key in the dimension table. 
All four foreign keys reside in the fill table: MEMBER_ID, DRUG_NDC, DRUG_FROM_COD, and EDRUG_BRAND_GENERIC_CODE.

* MEMBER_ID is a foreign key infill table because I’d like to know whose drug it belongs to for the prescription filled. 
* DRUG_NDC is a foreign key in the fill table because I can refer the NDC number to know which medication is filled. 
* DRUG_FORM_CODE is a foreign key in the fill table because I can refer to the code and know the medication's form. 
* DRUG_BRAND_GENERIC_CODE is a foreign key in the FILL table because I can refer to the DRUG_BRAND_GENERIC_CODE table to know if the medication filled is a brand name or generic name.

CASCADE, SET NULL, and RESTRICT are options for dealing with changes when there is an update or deletion in the parent table. CASCADE is used when you want to roll over the changes in the child table when there is an update or deletion in the parent table. SET NULL means that where there's an update or deletion in the parent table, the child table will change the values to NULL. The most restricted kind would be the RESTRICT, where no change would happen in the child table if there’s an update or deletion in the parent table.

Before deciding which option to go with for each foreign key, it is essential to determine which lowly changing dimensions(Type 0,1,2 and 3) to go with for the database. Type 2 is appropriate for the business use case because I don't want to overwrite any changes in the dimension table. Instead, a new row is created to capture the latest information while preserving the historical record. For example, if there's a change in drug form, I would like to keep the history of the fill transaction. 

Now that I know how to deal with information changes in the dimension tables, CASCADE is the option that best matches Type 2 for all foreign keys. 

### Sample queries
#### 1
The client is interested in having a few sample queries ready before the database goes live. Below are some commonly asked questions from across different business function. 

<img width="573" alt="raw dataset" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/num_rx.png>

Above is a SQL query that answers * how many prescriptions were filled for the drug Ambien? * - Answer: 5. 

<img width="573" alt="raw dataset" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/num_rx_ans.png> 

I started by looking at the tables that contained the information. I would need the name of the medication and prescription transaction The fact table and drug table contain the information I need to answer the business question. 

The drugs table left join the fact table on drug_ndc would result in a larger table with all the information needed to answer the business question. The COUNT and GROUP BY function would show the total number of a particular medication filled.

I can also further refine the filter by adding the WHERE clause. For example, *how many prescriptions were filled for the drug Ambien after 2017-01-01?* In which case, I would add the following WHERE clause to the query: 

```
SELECT COUNT(`fact_fill`.`DRUG_NDC`), DRUG_NAME
FROM dim_drugs
LEFT JOIN fact_fill ON `dim_drugs`.`DRUG_NDC` = `fact_fill`.`DRUG_NDC`
WHERE fill_date > 2017-01-01
GROUP BY DRUG_NAME;

```
#### 2 
The following SQL query is set up to questions like * how many unique members are over 65 years of age?* and * how many prescription did they fill?*

<img width="573" alt="raw dataset" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/case_logic.png>

From the SELECT statement, I listed out all the attributes that I wanted to see from the query output: 
* member_id
* age
* the total number of prescription filled
* the total amount of copay
* the total amount of insurance paid 
* day of birth 

I then look for the tables that contain the information I listed above - fact table and members table. The fact table left join the members table on member_id, resulting in a larger table with all the information I need in the output.  

<img width="573" alt="raw dataset" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/case_logic_ans.png>

Next, I used the CASE statement to group the members by their age into two buckets - 65+ or 65-. 

Case statement is equivalent to if...then..else statement. In SQL it is in the form of case...then...else, which translates to case year is less than 1980 then 65+ and when case year is greater than 1980 then 65-.I nested the CASE statement under the SELECT in the form of subquery. The output of the subquery would already assign the memebers into one of the two buckets. 

Once all the members is assigned to their age group category, I add the rest of the information I want in the SELECT statement:

* total number of prescription filled
* total amount of copay
* total amount of insurance paid 

The query ends with a GROUP BY, which would bucket all the output by age group category. 

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
