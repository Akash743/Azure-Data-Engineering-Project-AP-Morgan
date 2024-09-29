# Azure-Data-Engineering-Project-AP-Morgan
Azure Data Engineering Project: AP Morgan Data Pipeline

**Pipeline**

<img width="394" alt="Project2_Archiecture" src="https://github.com/Akash743/Azure-Data-Engineering-Project-AP-Morgan/assets/57750483/2c2c0092-36ae-4421-98cd-4ad2432a2ebf">


**High Level Details**

1. Internal Application sends CSV file in Azure data lake storage. Validation needed to apply on this as follows:

2. Check for duplicate rows. If it contains duplicate rows, file need to be rejected.

3. Need to validate the date format for all the date fields. Date column names and desired date format is stored in a Azure SQL server. If validation fails file will be rejected.

4. Move all the rejected files to Reject folder.

5. Move all the passed files to Staging folder.

6. Write the passed files as the Delta table in the Azure Databricks


**Steps:**
1. Create ADLS Storage Account with Landing Folder in it
2. Processing using Azure Databricks: Create Azure Databricks Workspace
3. Create SQL server and attach database to it. A server can have multiple databases. Check the Add Current Client IP address
4. Install SSMS(Sequel Server Management Studio) in personal system and connect Azure DB with it using Azure Server name. Add CLient IP to Firewall settings.
5. Create Table named 'FileDetailsFormat' using query. Table contains the correct file schema. Will use this format stored in table to validate the files

   ![image](https://github.com/Akash743/Azure-Data-Engineering-Project-AP-Morgan/assets/57750483/57dec0bb-b40f-4ca8-8b64-c50fbce0550e)

- We have three different type of file available. One could be Product, another one could be Product description and then the third one could be a Customer.

- So for the Product, you can see it says that it should have these four date columns And for this column, the data, whatever is coming should be in the format present in ColumnDateFormat.

- So our goal is if we are getting the customer file, we will pull the date format for this customer file and will validate

So that's how we will use the schema validation for our incoming file on this date format.


7. Create Key Vault and store the secrets
What we want is we want this Databricks to pull the schema of incoming file from this database,
And if it is valid/invalid, based on that it will push the incoming file into these directories, which is again in the ADLS.
We need to make a connection of Databricks to these storage accounts as well as the database. For both, will store the credentials in the Key Vault. SAS token for connecting ADLS folders and database credentials for DB
Create Secret Scope for Databricks notebook. This Scope will connect to key Vault to access the ADLS and DB. Use Key Vault Resouce ID and DNS name for Secret scope

8. **Create Databricks cluster**

Create Notebook which will have the following:
- Input from ADF
- Create DB onnection
- Mount the storage

  
**Rule 1** - Check Duplication of rows in file: Find if the row counts in file are equal to no. of distinct rows. If yes, means there is no duplication is there

**Rule 2** - Check for date formats

- Based on the file name received, filter 'ColumnName' and 'ColumnDateFormat' from the DB for that specific file name type. Ex. For file 'Product.csv', it will fetch those 2 columns corresponding to FileName 'Product'
         
- Now will filter that ColumnName from the file and try to convert that into ColumnFormat taken from DB. Will count non null occurences while conversion. 
         
- If non null occurences = no. of rows in file, means all rows date values wer fine as all could be converted to desired Column date format. And this will happen for all 4 ows in DB for Product - StartDate, EndDate, CreateDate, ModifiedDate.

In case any of the 2 rules is violated, will send the file to Landing\Rejected folder, otherwise will send to Landing\Staging Folder

9. **Create Pipeline**
- Pipeline which will move the file or which will help to validate the file from the storage using Databricks and will transfer that to respective folders
- Create Azure Data Factory account
- Create Linked Service for Databricks - generate Access token from Databricks and store that in Key Vault. Add access policy - Key, Secret & Certificate Manager and select Project2 as Principal. This will allow Databricks to access secret stored in Key Vault
- Now, use the secret stored in Key Vault to create the linked service
- Create Linked Service for ADLS which will help us connect to these services

- Create Pipeline
Go to Author tab and select Databricks Notebook to be run. This notebook should run as soon as we receive a file in our Storage Input folder
- Create Storage Event Trigger for the same which will triiger the notebook as any file is dropped in Storage account.
- Ensure that Storage Event Trigger is registered already (Subscriptions>>Resource Providers>>EventGrid)

10. **Testing**
Put Valid and Invalid files in Input folder and chck if the pipeline is executed. If executed successfully, will be able to see those files moved to respective Staging and Rejected folder. 
