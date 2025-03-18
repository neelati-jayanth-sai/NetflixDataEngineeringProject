# Netflix Data Engineering Project  

This project demonstrates how to build a data engineering pipeline using **Azure Data Factory**, **Databricks**, **Azure Data Lake**, and **Azure Synapse Analytics**. We extract data from **GitHub**, process it using **Databricks Auto Loader**, and store it in a three-tier architecture within a Data Lake. Finally, the processed data is made available for reporting using **Power BI**.  

## Architecture Overview  

The data is stored in Azure Data Lake following a **three-tier architecture**:  

- **Bronze Layer**: Stores raw incremental data.  
- **Silver Layer**: Contains cleaned and transformed data.  
- **Gold Layer**: Optimized for analytics and reporting using a **star schema**.  

Both **Silver** and **Gold** layers are implemented using **Delta Live Tables**.  

## Tech Stack  
- **Data Source**: GitHub (Raw CSV files)  
- **Data Ingestion**: Azure Data Factory  
- **Data Processing**: Databricks with Auto Loader  
- **Storage**: Azure Data Lake (Bronze, Silver, Gold layers)  
- **Data Warehousing**: Azure Synapse Analytics  
- **Reporting**: Power BI  

## Project Setup  

### Step 1: Create Azure Resources  

1. Log in to the **Azure Portal**.  
2. Create a **Resource Group**.  
3. Set up an **Azure Data Lake** and create the following containers:  
   - `raw`  
   - `bronze`  
   - `silver`  
   - `gold`  

### Step 2: Configure Azure Data Factory  

1. In the **Azure Portal**, create a **Data Factory** within the previously created Resource Group.  
2. Open **Azure Data Factory Studio**.  
3. Navigate to **Manage > Linked Services**, and create the following linked services:  
   - **Source (GitHub)**:  
     - Create an **HTTP Linked Service**.  
     - Set the **Base URL** to the raw file location in GitHub (e.g., `https://raw.githubusercontent.com/`).  
     - Use **Anonymous Authentication**.  
   - **Destination (Azure Data Lake)**:  
     - Create a new **Linked Service** for Data Lake.  
     - Select the Data Lake created in **Step 1**.  
     - Test the connection to ensure it works.  

### Step 3: Create a Data Pipeline in Azure Data Factory  

1. In **Azure Data Factory**, go to the **Author** tab.  
2. Click **New Pipeline** and configure the following components:  
   - **Web Activity**:  
     - Configure it with the GitHub URL for `netflix_titles.csv`.  
     - Fetch metadata for validation.  
   - **Set Variable Activity**:  
     - Store the fetched data in a variable (Data Type: **String**).  
   - **Validation Activity**:  
     - Ensure `netflix_titles.csv` exists in the **Raw** container before proceeding.  
   - **ForEach Activity**:  
     - Process multiple files dynamically using the following array:  

       ```json
       [
         {"filename": "netflix_titles.csv", "foldername": "netflix_titles"},
         {"filename": "netflix_category.csv", "foldername": "netflix_category"},
         {"filename": "netflix_countries.csv", "foldername": "netflix_countries"},
         {"filename": "netflix_directors.csv", "foldername": "netflix_directors"}
       ]
       ```  
     - This enables dynamic file processing.  
   - **Copy Data Activity**:  
     - Use the **GitHub Linked Service** as the source.  
     - Set the **relative file path** dynamically using parameters.  
     - Configure the **sink (destination)** to store the file in the **Bronze** container.  

### Step 4: Publish & Run the Pipeline  

1. Connect all components properly in the pipeline.  
2. Click **Publish All** to save changes.  
3. Add a **Trigger** and select **Trigger Now** to execute the pipeline.  

## Notes  
- Ensure the `netflix_titles.csv` file is uploaded to the **Raw** container before running the pipeline.  
- If the file is missing, the pipeline will not execute.  
