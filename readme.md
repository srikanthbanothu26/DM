# Data Migration Through SQL Commands

**Note**: Ensure all required modules are installed in the new database before initiating the migration process.

## Key Considerations Before Data Migration

1. Focus on **basic models** and related models before migrating specific models.
2. Basic models include:
   - `res.currency`
   - `res.country`
   - `res.country.group`
   - `res.country.state`
   - `res.company`
   - `res.partners`
   - `res.users`

3. Realted models are the models with many-to-one and one-to-many relationships in target model
---

## Process to Connect to the Database

1. **Switch to the `postgres` user**:  
   ```bash
   sudo su postgres
   ```
2. **Access the PostgreSQL command-line interface**:  
   ```bash
   \psql
   ```
3. **Connect to the target database**:  
   ```sql
   \c database_name
   ```

**Extras**:
- List all models in the database:  
  ```sql
  \d
  ```
- View the structure or data of a specific model:  
  ```sql
  \d model_name
  ```

---

## Model Data Migration

To migrate data into a specific model, use the following command:  
```sql
COPY target_model_name (col1, col2, col3, col4, ...) 
FROM 'path/to/file_name.csv' 
WITH CSV HEADER;
```
**Note**:

1. In this case, 'HEADER' denotes the column name in the target model and the table column name in the CSV file.

2. Above command **inserts new data** only and does not update existing data in the target model.
 
### Important Note
The possibility of 'id' mismatches owing to pre-existing records may arise. In order to resolve this issue, we must remove current records prior to data migration in the target model.

**Delete existing data** in the target model before migration:  
   ```sql
   DELETE FROM target_model_name;
   ```
---

## Sequence of Basic Models for Data Migration

To ensure proper dependencies, migrate the following models in this order:

1. **Currency** (`res.currency`)  
2. **Country** (`res.country`)   
3. **Groups** (`res.country.group`)
4. **State** (`res.country.state`) 
5. **Company** (`res.company`)  
6. **Contacts** (`res.partner`) 
7. **Users** (`res.users`) 
---

By following this structured approach, you can ensure a smooth and accurate data migration process.


## CURRENCY

target_model: 
            `
                res.currency
            `
### to view the list of all columns of target model
```sql
    \d res_currency
```

**note**: 
   1. you need to copy all the records from old db to a folder in csv format  
   2. delete all the records of target model in new db

### insert all the records from old db to new db

```sql
copy res_currency from '/home/Downloads/res_currency.csv' with csv header;
```

***if you want to insert only specific columns***

**note**:
    please replace these columns with actual columns 

```sql
copy res_currency(column1,column2,column3,   column4,,......) from '/home/Downloads/res_currency.csv' with csv header;
```
---

---



**CONTACTS**
---
model: ` res.partner`

related models: ` res.country,res.country.state,res.company`

note: 
  1. before migration of res.partner need to migrate related models
  2. verify all the related models were migrated or not
  3. verify all the required fields are in migration or not
  4. delete all the records of target model in new database

```sql
    \d res_partner
```

```sql
    copy res_partner (column1,column2,column3,column4,.....) from '/home/srikanth/Downloads/res_partner.csv' with csv header;
```

----
*** 
When migrating data using SQL commands, we may encounter some problems. For example, a field in the old database may have a char datatype, but the data type in the new database is json, so we must format the datatype in a csv file. 
***

 formatting command for char fields to json command
 ``` 
 
 "{""en_US"": """ & P2 & """}" 
 
 ```
**Note**:

 that this command is only used in CSV files, where p is a column and 2 is a row.

Any errors that occur when upgrading a module that attempts to create records, such as a name with an id that already exists in this module, must be fixed by using the following command: 
```sql 
ALTER SEQUENCE Model_id_seq RESTART WITH max(id); 

```
example:
```sql
ALTER SEQUENCE account_analytic_line_id_seq RESTART WITH 16150; 
```

Search for a Related Table: Run the following query to search for any table with a similar name:

```sql
SELECT table_name 
FROM information_schema.tables 
WHERE table_name LIKE '%hr_department%';
```



## Credits

This document was meticulously prepared by **Srikanth Banothu**.  
It serves as a comprehensive guide for data migration between different Odoo versions, tailored to ensure a seamless transition to the latest version.

If you find this documentation helpful or if it gets shared, I kindly request you to acknowledge my contribution. Your recognition motivates me to create more useful content in the future.

---

## Copyright and Rights Reserved

© 2024 **Srikanth Banothu**. All rights reserved.  

This document, including all its content and examples, is copyrighted material.  
Unauthorized reproduction, distribution, or modification of this documentation, in part or in full, is strictly prohibited without prior written consent from the author.

By using this document, you agree to respect the intellectual property rights of the author.

---

