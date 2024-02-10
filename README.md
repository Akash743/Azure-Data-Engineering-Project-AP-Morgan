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
