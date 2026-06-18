Thoroughly read and understand the PBIP knowledge base in the [kb-pbip](../.resources/kb-pbip.md) file.

# Business Requirements 💼

Create a new semantic model named 'Sales' and ensure the following requirements are met:

| Requirement ID | Description | User Story | Expected Behavior |
|---------------|-------------|------------|-------------------|
| **SALES-001** | Total Sales Performance Overview | As a **Sales Manager**, I want to see the total sales amount across all channels (Internet + Reseller) so that I can understand our overall revenue performance. | Create a combined [Total Sales Amount] measure that sums the sales from Internet and Reseller with proper relationship handling through shared dimensions. |
| **SALES-002** | Sales Growth Analysis | As a **Executive Leadership**, I want to see year-over-year and month-over-month sales growth percentages so that I can track business progress against targets. | Create time intelligence measures using SAMEPERIODLASTYEAR and PREVIOUSMONTH functions: [Sales YoY Growth %] and [Sales MoM Growth %] with proper Calendar table relationships. |
| **SALES-003** | Top Performing Products | As a **Product Manager**, I want to identify the top 10 products by sales amount and category so that I can focus marketing efforts on high-performing items. | Create [Product Sales Rank] measure using RANKX function and establish proper relationships between fact tables and DimProduct for accurate product-level aggregations. |

# Data source information 🛢️

- **Server**: `dummyserver.database.windows.net`
- **Database**: `AdventureWorksDW`

If you cannot connect to the SQL Server above to get the schema, all  information of the Datawarehouse can be found in the file `AzureSQLSchema.csv`. 

# Development rules 🧑‍💻

- Analyze the datasource tables in `AzureSQLSchema.csv` and pick the tables that best answer the requirements. But dont bring any tables or columns that are not strictly necessary, when in doubt ask me.
- Use import storage mode for all tables.
- Don't create a new Calendar table, make sure you reuse Calendar table in the TMDL file `.resources/Calendar.tmdl` you may change names but keep exactly as-is. 
- Always prefer simple star-schema modeling and not snowflake. For example if you find tables like Product, ProductSubCategory and ProductCategory. Prefer to create a single Product table that joins these tables.
- The created TMDL tables should have the necessary columns and measures to answer the requirements.
- The relationship between Calendar and fact table should be made using a datetime column. If the fact table  dont have a datetime or date column please create one using PowerQuery language
- All measures should be created in the source fact tables TMDL files, but do not duplicate measures they must be unique within the model. 
- The server and database name should be semantic model parameters
- Make sure you set descriptions on all created measures using business language
- Don't try to test the semantic model data

# Semantic Model Naming conventions 🏷️

- Use singular names for dimension tables
- Use plural names for fact tables
- All model object names should be lower case
- Don't use 'fact' or 'dim' in the table or column names. Prefer business friendly representation
- Don't use column names like 'product name' prefer 'product' instead
- Measure names should follow a consistent naming convention: 
  - [measure name] for base measure
  - [measure name (ly)] for last year value of the base measure
  - [measure name (ytd)] for ytd value for the base measure
