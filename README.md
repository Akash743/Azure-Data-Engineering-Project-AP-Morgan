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


Steps:
1. Create ADLS Storage Account with Landing Folder in it
2. Processing using Azure Databricks: Create Azure Databricks Workspace
3. Create SQL server and attach database to it. A server can have multiple databases. Check the Add Current Client IP address
4. Install SSMS(Sequel Server Management Studio) in personal system and connect Azur DB with it using Azure Server name. Add CLient IP to Firewall settings.
5. Create Table using query. Table contains the correct file schema. Will use this format stored in table to validate the files

   ![image](https://github.com/Akash743/Azure-Data-Engineering-Project-AP-Morgan/assets/57750483/57dec0bb-b40f-4ca8-8b64-c50fbce0550e)

We have a three different type of file available.

One could be product, another one could be product description and then the third one could be a customer.

So for the product, you can see it says that it should have these four digit columns started and it

created modified it.

And for this column, the data, whatever is coming should be in the format present in ColumnDateFormat.

So our goal is if we are getting the customer file, we will pull the date format for this customr and so on.

So that's how we will use the schema validation for our incoming file on this date format.

