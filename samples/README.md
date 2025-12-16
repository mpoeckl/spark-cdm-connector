# Test CDM Data for Spark 3.5 Connector

The directory [sample-cdm-data](sample-cdm-data) contains complete test CDM data for validating the Spark 3.5 CDM Connector with Microsoft Fabric Runtime 1.3 and Azure Synapse Analytics Apache Spark Pools 3.5.

The directory [crm-cdm](crm-cdm) contains sample Synapse Dataverse Link export data in model.json format (CDM <1.0).

## Structure

### sample-cdm-data (CDM 1.0+ / manifest.cdm.json format)
- **CdmSampleData.manifest.cdm.json**: Main manifest file defining the CDM folder structure
- **Employee.cdm.json**: Employee entity definition with various data types
- **Customer.cdm.json**: Customer entity definition with business data
- **SalesOrder.cdm.json**: Sales order entity definition with relationships
- **Employee/**: Sample employee data in both CSV and Parquet formats
- **Customer/**: Sample customer data in CSV format  
- **SalesOrder/**: Sample sales order data in Parquet format

## Entity Schemas

### Employee Entity
- **EmployeeId** (integer, PK): Unique employee identifier
- **FirstName** (string, required): Employee first name
- **LastName** (string, required): Employee last name
- **Email** (string): Email address
- **DepartmentId** (integer): Department reference
- **Salary** (decimal 10,2): Employee salary
- **HireDate** (date): Date of hire
- **IsActive** (boolean): Employment status
- **LastModified** (datetime): Last update timestamp

### Customer Entity
- **CustomerId** (integer, PK): Unique customer identifier
- **CustomerName** (string, required): Company name
- **ContactEmail** (string): Primary contact email
- **Phone** (string): Phone number
- **Address, City, State, PostalCode, Country** (strings): Address information
- **CreditLimit** (decimal 12,2): Credit limit
- **CustomerSince** (date): Customer acquisition date

### SalesOrder Entity
- **OrderId** (integer, PK): Unique order identifier
- **CustomerId** (integer, FK): Reference to Customer
- **EmployeeId** (integer, FK): Reference to Employee
- **OrderDate, RequiredDate, ShippedDate** (dates): Order timeline
- **ShipVia** (integer): Shipping company reference
- **Freight** (decimal 8,2): Shipping cost
- **Ship*** (strings): Shipping address details
- **OrderTotal** (decimal 12,2): Total order amount

## Usage in Testing

These files provide complete sample data to test:

1. **Read Operations**: Reading CDM entities into Spark DataFrames
2. **Schema Validation**: Ensuring proper data type mapping
3. **Mixed Formats**: Testing both CSV and Parquet data sources within the same entity
4. **Complex Types**: Validating decimal precision, dates, and boolean handling
5. **Fabric Integration**: Testing in Microsoft Fabric Runtime 1.3 environment
6. **Multi-Partition Entities**: The Employee entity demonstrates reading from multiple data partitions (CSV + Parquet)

## Sample Test Code

### Reading manifest.cdm.json format (CDM 1.0+)
```scala
// Read Employee entity using ADLS Gen2 and SAS token
val employees = spark.read
  .format("com.microsoft.cdm")
  .option("sasToken", "your-sas-token")
  .option("storage", "your-storage-account.dfs.core.windows.net")
  .option("manifestPath", "your-container/your-cdm-folder/CdmSampleData.manifest.cdm.json")
  .option("entity", "Employee")
  .load()

employees.show()
employees.printSchema()
```

### Reading model.json format (CDM <1.0, Dataverse Link)
```scala
// Read account entity from Synapse Dataverse Link export
val accounts = spark.read
  .format("com.microsoft.cdm")
  .option("appId", "your-app-id")
  .option("appKey", "your-app-key")
  .option("tenantId", "your-tenant-id")
  .option("storage", "your-storage-account.dfs.core.windows.net")
  .option("manifestPath", "your-container/crm-cdm/model.json")
  .option("entity", "account")
  .load()

accounts.show()
accounts.printSchema()
```
**Note**: model.json format requires a config.json file in the same directory to resolve relative paths.

## Notes

- All test data is synthetic and for testing purposes only at your own risk
- sample-cdm-data follows CDM 1.0+ format standards (manifest.cdm.json)
- crm-cdm follows CDM <1.0 format (model.json) as exported by Synapse Dataverse Link
- Compatible and testet with Fabric Runtime 1.3 (Spark 3.5) and Azure Synapse Analytics Apache Spark Pools 3.5
- Includes proper CDM traits and data type specifications
- Employee entity includes both CSV and Parquet partitions for comprehensive format testing