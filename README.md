# Netflix Data Engineering Project  

In this project, we are using GitHub as the data source and Azure Data Factory to load data. We also use Databricks with Auto Loader to implement an incremental data loading model.  

All data is stored in a Data Lake, following a three-tier architecture:  
- **Bronze**: Stores raw incremental data.  
- **Silver**: Contains transformed data.  
- **Gold**: Serves data using a star schema.  

Both the Silver and Gold layers are implemented as Delta Live Tables.  

For data warehousing, we use **Azure Synapse**, and for reporting, we use **Power BI**.  

## Steps to Set Up  

### Step 1: Create Azure Resources  
1. Go to the **Azure Portal** and create a **Resource Group**.  
2. Create a **Data Lake** and add four containers:  
   - `raw`  
   - `bronze`  
   - `silver`  
   - `gold`  
step-2
now in the resource group created previusly ,we create a data facotry resource and then we go to data factory studio
the we go to linked servies and create following linked services
the first sercvice is a souce from we are getting data and its github soo we craete a http  linked service
and at base url palce we base url of files location in github
