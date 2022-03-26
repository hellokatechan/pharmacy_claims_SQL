# Pharmacy claims
Using SQL to answer business questions about pharmacy claims
## 📌 Overview
### Business needs

The client is an insurance company interested in setting up a test database before rolling out the data wareshouse goes live in production , along with pre-program along with pre-program SQL queries for future analysis and reporting. The developer is given a small sample dataset containing a list of records of pharmacy claims.(see photo below)

### Normalization 

<img width="573" alt="raw dataset" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/raw_data.png>

The raw sample dataset was in its zero normal form because there were multiple entitles/tables nested under one sheet. Each row represents a pharmacy claim transaction containing a patient's name, medication filled and billing information. To normalize the dataset from zero normal form to 1NF, I looked for immediate entities, tables and keys in the raw dataset.

<img width="573" alt="dataset normalization" src=https://raw.githubusercontent.com/hellokatechan/pharmacy_claims_SQL/main/MARKDOWNS/data_org.png>

Entities often lead to tables, which I then assigned each attribute under a table. Each table should have a unique identifier column, which serves as a primary key for each table. All tables have a primary key except for the FILL fact table - I created FILL_ID as the primary key.  



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
