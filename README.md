# **Netflix Data Engineering Project**

This project showcases the development of a modern data engineering pipeline using **Azure Data Factory**, **Databricks**, **Azure Data Lake**, and **Azure Synapse Analytics**. We extract data from **GitHub**, process it using **Databricks Auto Loader**, store it in a **three-tier Data Lake architecture**, and expose it for analysis in **Power BI**.

---

## 🏗️ **Architecture Overview**

The pipeline adheres to a **three-tier architecture** within **Azure Data Lake**:

- **Bronze Layer**: Stores raw, incremental data.
- **Silver Layer**: Contains cleaned and transformed datasets.
- **Gold Layer**: Stores aggregated data modeled using a **star schema**, optimized for analytics.

The **Silver** and **Gold** layers are built using **Delta Live Tables (DLT)**.

---

## 🧰 **Tech Stack**

- **Data Source**: GitHub (CSV files)
- **Data Ingestion**: Azure Data Factory
- **Data Processing**: Databricks with Auto Loader
- **Storage**: Azure Data Lake (Bronze, Silver, Gold)
- **Data Warehousing**: Azure Synapse Analytics
- **Visualization**: Power BI

---

## 🚀 **Project Setup**

### **Step 1: Provision Azure Resources**

1. Log in to the **Azure Portal**.
2. Create a **Resource Group**.
3. Create an **Azure Data Lake Storage Gen2** account with the following containers:
   - `raw`
   - `bronze`
   - `silver`
   - `gold`

---

### **Step 2: Set Up Azure Data Factory**

1. Create a **Data Factory** under the same resource group.
2. Launch **Data Factory Studio**.
3. Go to **Manage > Linked Services** and create:
   - **GitHub (HTTP)**:
     - Base URL: `https://raw.githubusercontent.com/`
     - Authentication: **Anonymous**
   - **Azure Data Lake**:
     - Connect to the Data Lake created earlier.
     - Test connection to verify.

---

### **Step 3: Build the Data Pipeline**

1. In **Data Factory**, open the **Author** tab and create a new pipeline.
2. Add the following components:
   - **Web Activity**: Fetch metadata for `netflix_titles.csv`.
   - **Set Variable**: Store file metadata as a string variable.
   - **Validation Activity**: Ensure the `netflix_titles.csv` file exists in the **Raw** container.
   - **ForEach Activity**: Dynamically process files using this JSON array:

     ```json
     [
       {"filename": "netflix_titles.csv", "foldername": "netflix_titles"},
       {"filename": "netflix_category.csv", "foldername": "netflix_category"},
       {"filename": "netflix_countries.csv", "foldername": "netflix_countries"},
       {"filename": "netflix_directors.csv", "foldername": "netflix_directors"}
     ]
     ```

   - **Copy Data Activity**:
     - Source: GitHub (HTTP)
     - Destination: Azure Data Lake (Bronze layer)
     - Use dynamic file path parameters.

---

### **Step 4: Run the Pipeline**

1. Validate connections between all activities.
2. Click **Publish All**.
3. Add a **Trigger**, then select **Trigger Now** to execute.

📝 **Note**: Ensure `netflix_titles.csv` is uploaded to the **Raw** container. The pipeline will not run if it is missing.

---

## ⚙️ **Step 5: Set Up Azure Databricks**

Azure Databricks is a separate service and requires specific configuration to access data from Azure Data Lake.

### **Step 5.1: Create a Databricks Workspace**

1. In Azure Portal, create a **Databricks** workspace.
2. Launch the workspace.
3. Go to **Settings > Account > Catalogs**.

### **Step 5.2: Install Access Connector for Azure Databricks**

1. Go to the **Azure Marketplace**.
2. Search and install **Access Connector for Azure Databricks**.
3. Select the resource group and provide a name.
4. Click **Review + Create** and deploy.

### **Step 5.3: Configure IAM Access**

1. Navigate to your **Azure Data Lake**.
2. Open **IAM (Access Control)** > **Add Role Assignment**.
3. Role: `Storage Blob Data Contributor`
4. Member Type: **Managed Identity**
5. Select the **Access Connector** created earlier.
6. Click **Review + Assign**.

---

### **Step 5.4: Create a Unity Metastore**

1. Go back to **Databricks > Catalogs**.
2. Click **Create Metastore**.
3. Fill out required details:
   - Name
   - Storage root (one of your containers)
4. Uncheck **Automatically assign workspace**.
5. Skip and proceed.

### **Step 5.5: Assign Metastore to Workspace**

1. In **Catalog > Metastores**, click your newly created metastore.
2. Click **Assign Workspace** and select your Databricks workspace.

---

### **Step 5.6: Final Metastore Setup**

1. Go to **Configuration > Metastore Admin**.
2. Change **Hashed account** to your actual admin account.

---

### **Step 5.7: Create a Catalog**

1. In **Catalog**, click **+ New Catalog**.
2. Choose **Standard** type.
3. No need to specify external location (already configured at metastore level).
4. Grant access to all accounts if only one user is present.

---

### **Step 5.8: Configure External Locations**

1. Create a **Storage Credential**:
   - Name it appropriately.
   - Provide the **Access Connector ID**.

2. Create **External Locations** for each tier:
   - **Bronze External Location**:
     - Name: `bronze_ext`
     - Path: URL of Bronze container
     - Select the storage credential you just created
   - Repeat for **Raw** , **Silver** and **Gold** 

---

## ⚙️ **Step 6: Configure Compute and Notebook**

1. Go to **Compute** and create a new cluster:
   - **Policy**: Personal
   - **Runtime Version**: LTS version (most stable)
   - **Auto Terminate**: After 20 minutes of inactivity

2. Go to **Workspace**, create a folder (e.g., `Netflix Project`).
3. Inside it, create a new **Notebook**:
   - Rename it (e.g., `1_AutoLoader`)
   - Use it to write logic for **Auto Loader** and **DLT pipeline** development.

---


step 6.1 -
just to repluicate the code of 1_autoloader

step7-
thwen create a new nea notebookmin the workspace called silver and replicate the silver notebook povided in the repo

and the silver notebook is going to be parameterized note book for which we care going to create a workflow to get data into our silver container
