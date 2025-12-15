# Spark 3.5 CDM Connector Installation Guide

> **⚠️ DISCLAIMER**: This guide is for a **private fork** of the Azure Spark CDM Connector, optimized for Apache Spark 3.5. This version is **NOT officially supported by Microsoft**.

> **⚠️ LIMITED TESTING DISCLAIMER**: The testing of features of the Spark CDM Connector is limited, so not all features might work as expected. Use with caution and test thoroughly in your specific environment.

This guide provides step-by-step instructions for installing and using the Spark CDM Connector with Apache Spark 3.5 on:
- **Microsoft Fabric Runtime 1.3** (Spark 3.5, Delta 3.2)
- **Azure Synapse Analytics** (Spark 3.5 pools)

## Prerequisites

### For Microsoft Fabric Runtime 1.3
- **Microsoft Fabric Workspace**: Access to a Fabric workspace with Spark capabilities
- **Fabric Runtime 1.3**: Ensure your workspace is configured to use Fabric Runtime 1.3 (Spark 3.5, Delta 3.2)
- **Data Storage**: Access to storage where your CDM data resides:
  - **OneLake**: Microsoft Fabric's native data lake (recommended for Fabric environments)
  - **ADLS Gen2**: Azure Data Lake Storage Gen2 with HNS enabled
- **Authentication**: Appropriate permissions for the storage account (Service Principal for OneLake or ADLS Gen 2, or SAS token for ADLS Gen2)

### For Azure Synapse Analytics
- **Synapse Workspace**: Access to an Azure Synapse Analytics workspace
- **Spark 3.5 Pool**: Apache Spark 3.5 pool configured in your workspace
- **Data Storage**: Azure Data Lake Storage Gen2 with HNS enabled
- **Authentication**: Synapse Managed Identity, Service Principal, or SAS token with appropriate RBAC permissions

## Building the Connector (If Not Using Pre-built JAR)

If you're building from source, follow these steps:

### Prerequisites for Building
- **Java 11+**: OpenJDK 11 or higher
- **SBT**: Scala Build Tool (version 1.5.5 or higher)
- **Git**: For cloning the repository

### Build Steps
1. **Clone the repository**:
   ```bash
   # Note: This is a private fork, not the official Microsoft repository
   git clone https://github.com/mpoeckl/spark-cdm-connector.git
   cd spark-cdm-connector
   ```

2. **Run the assembly build**:
   ```bash
   sbt assembly
   ```

3. **Find the compiled JAR**:
   The build will create: `target/spark-cdm-connector-assembly-spark3.5-1.20.0.jar`
   
   **File Details**:
   - **Location**: `target/spark-cdm-connector-assembly-spark3.5-1.20.0.jar`
   - **Size**: ~22.5 MB (uber JAR with all dependencies)
   - **Contents**: CDM connector + Jackson 2.15.2 + MSAL4J + CDM Standards

## Step 1: Configure Spark 3.5 Environment

### For Microsoft Fabric Runtime 1.3

1. Navigate to your **Fabric Workspace settings**
2. Go to **Data Engineering/Science** → **Spark Settings**
3. Select the **Environment** tab
4. Under **Runtime Versions**, expand the dropdown
5. Select **1.3 (Spark 3.5, Delta 3.2)** and save your changes

![Runtime Selection](https://learn.microsoft.com/en-us/fabric/data-engineering/media/mrs/runtime13.png)

### For Azure Synapse Analytics

1. Navigate to your **Synapse workspace**
2. Go to **Manage** → **Apache Spark pools**
3. Create a new pool or select an existing pool
4. Under **Apache Spark version**, select **3.5** (or the latest Spark 3.5.x version available)
5. Configure pool size and settings as needed
6. Review and create the Spark pool

## Step 2: Install the CDM Connector

> **📋 Note**: This private fork is not published via Maven repositories. Manual JAR installation is required.

### Upload JAR File

1. **Obtain the JAR file**: 
   - Build from source using: `sbt assembly`
   - Find the compiled JAR at: `target/spark-cdm-connector-assembly-spark3.5-1.20.0.jar`
   - Or download from the releases section of this repository

2. **Install in Microsoft Fabric**:
   - In your Fabric workspace, go to **Data Engineering** → **Environment**
   - Create or edit an environment
   - Upload the JAR file under **Custom libraries**
   - Save and publish the environment

3. **Install in Azure Synapse Analytics**:
   - In your Synapse workspace, go to **Manage** → **Workspace packages**
   - Click **Upload** and select the JAR file
   - In your Synapse workspace, go to **Manage** → **Apache Spark pools**
   - Browse to your Spark 3.5 pool and select **Packages** in the context menu
   - Click **+ Select from workspace packages** and select the uploaded package
   - Apply changes and restart the pool if needed

4. **Alternative: Session-level Installation**:
```python
# Reference the JAR in your notebook session (works in both Fabric and Synapse)
spark.conf.set("spark.jars", "/path/to/spark-cdm-connector-assembly-spark3.5-1.20.0.jar")
```

## Step 3: Configure Authentication

### 🔐 Authentication Methods and Storage Support

> **⚠️ Testing Status**: This fork has been tested with Microsoft Fabric Runtime 1.3 and Azure Synapse Analytics Spark 3.5 pools.

#### Authentication Support Matrix

| Authentication Method | ADLS Gen2 | OneLake | Platform Notes |
|----------------------|-----------|---------|----------------|
| **Service Principal** | ✅ Supported | ✅ Supported | Supported on both Fabric and Synapse.<br>**Required** for OneLake in Fabric. |
| **Managed Identity** | ✅ Synapse <br> ❌ Fabric | ✅ Synapse <br> ❌ Fabric | Supported in Synapse Analytics.<br>**Not supported in Fabric.** |
| **SAS Token** | ✅ Supported | ❌ Not supported | Supported on Fabric and Synapse.<br>**ADLS Gen2 only.** |
| **Interactive/User** | ❌ Not supported | ❌ Not supported | Not supported on any platform. |

### Option A: Azure Synapse Analytics ONLY: Managed Identity

Managed Identity is supported on Azure Synapse Analytics only.

#### ADLS Gen2 or OneLake (Python Example)
```python
# Synapse Managed Identity authentication
# Supported for ADLS Gen2 and OneLake access from Synapse Analytics only
# Not supported in Microsoft Fabric Spark!

df = spark.read \
    .format("com.microsoft.cdm") \
    .option("storage", "...") \
    .option("manifestPath", "...") \
    .option("entity", "...") \
    .load()
```

### Option B: Service Principal (Required for Microsoft Fabric Spark and OneLake)

Service Principal authentication provides the most reliable access to both ADLS Gen2 and OneLake storage from Microsoft Fabric.
Supported for Fabric and Synapse Spark.

#### ADLS Gen2 or OneLake (Python Example)
```python
# Service Principal authentication
# Required for OneLake access from Fabric Spark, also works with ADLS Gen2

df = spark.read \
    .format("com.microsoft.cdm") \
    .option("appId", "your-app-id") \
    .option("appKey", "your-app-secret") \
    .option("tenantId", "your-tenant-id") \
    .option("storage", "...") \
    .option("manifestPath", "...") \
    .option("entity", "...") \
    .load()
```

### Option C: SAS Token (ADLS Gen2 Only)

> **📋 Note**: SAS Token authentication only works with ADLS Gen2 storage, not with OneLake.

#### ADLS Gen2 (Scala Example)

```scala
// SAS Token based authentication, supported for Synapse and Fabric Spark
val df = spark.read
  .format("com.microsoft.cdm")
  .option("sasToken", "your-sas-token")
  .option("storage", "...")
  .option("manifestPath", "...")
  .option("entity", "...")
  .load()
```

## Step 4: Reading CDM Data

### 🗂️ Data Source Options

#### Option 1: Azure Data Lake Storage Gen2

**Scala Example:**
```scala
// Read CDM entity from ADLS Gen2
val df = spark.read
    .format("com.microsoft.cdm")
    /* add SAS Token or Service Principal authentication options if required */
    .option("storage", "your-storageaccount.dfs.core.windows.net")
    .option("manifestPath", "your-container/cdm-folder/YourManifest.manifest.cdm.json")
    .option("entity", "your-cdm-entity")
    .load()

// Display data and schema
df.show()
df.printSchema()
```

#### Option 2: OneLake Lakehouse Files

**Python Example - Reading from OneLake:**
```python
# Read CDM entity from OneLake
# Service Principal authentication required for Fabric Spark, Synapse may use Managed Identity (remove appId, appKey and tenantId)
df = spark.read \
    .format("com.microsoft.cdm") \
    .option("appId", "your-app-id") \
    .option("appKey", "your-app-secret") \
    .option("tenantId", "your-tenant-id") \
    .option("storage", "onelake.dfs.fabric.microsoft.com") \
    .option("manifestPath", "your-workspace/your-lakehouse.Lakehouse/Files/cdm-folder/YourManifest.manifest.cdm.json") \
    .option("entity", "your-cdm-entity") \
    .load()
```

**Scala Example - Reading from OneLake:**
```scala
// Read CDM entity from OneLake
// Service Principal authentication required for Fabric Spark, Synapse may use Managed Identity (remove appId, appKey and tenantId)
val df = spark.read
  .format("com.microsoft.cdm")
  .option("appId", "your-app-id")
  .option("appKey", "your-app-secret")
  .option("tenantId", "your-tenant-id")
  .option("storage", "onelake.dfs.fabric.microsoft.com")
  .option("manifestPath", "your-workspace/your-lakehouse.Lakehouse/Files/cdm-folder/YourManifest.manifest.cdm.json")
  .option("entity", "your-cdm-entity")
  .load()
```

#### 📋 OneLake Path Structure Guide


OneLake and ADLS Gen2 format for CDM Connector:

1. ADLS Gen2:
```
    storage: "[StorageAccountName].dfs.core.windows.net"
    manifestPath: "[Container]/[Folder]/[manifest-file].manifest.cdm.json"

    Example:
    storage: "mystorage.dfs.core.windows.net"
    manifestPath: "ContosoAnalytics/customer-data/Customers.manifest.cdm.json"
```
2. OneLake:
```
    storage: "onelake.dfs.fabric.microsoft.com"
    manifestPath: "[WorkspaceName]/[LakehouseName].Lakehouse/Files/[your-path]/[manifest-file].manifest.cdm.json"

    Example:
    storage: "onelake.dfs.fabric.microsoft.com"
    manifestPath: "ContosoAnalytics/SalesData.Lakehouse/Files/customer-data/Customers.manifest.cdm.json"
```

## Step 5: Writing CDM Data

### Write with Entity Definition

```scala
import org.apache.spark.sql.SaveMode

// Write DataFrame to CDM format
processedData
  .write
  .format("com.microsoft.cdm")
  .option("storage", "yourstorageaccount.dfs.core.windows.net")
  .option("manifestPath", "your-container/output-cdm/OutputManifest.manifest.cdm.json")
  .option("entity", "YourEntity")
  .option("entityDefinition", "path/to/YourEntity.cdm.json")
  .mode(SaveMode.Overwrite)
  .save()
```

### Write with Auto-Schema Generation

```scala
// Let the connector generate the schema automatically
processedData
  .write
  .format("com.microsoft.cdm")
  .option("storage", "yourstorageaccount.dfs.core.windows.net")
  .option("manifestPath", "your-container/auto-schema-output/AutoGenerated.manifest.cdm.json")
  .option("entity", "AutoGeneratedEntity")
  .mode(SaveMode.Overwrite)
  .save()
```

## Step 6: Advanced Configuration **[⚠️ Not tested yet]**

### Performance Tuning for Fabric

```scala
// Configure for optimal Fabric performance
spark.conf.set("spark.sql.adaptive.enabled", "true")
spark.conf.set("spark.sql.adaptive.coalescePartitions.enabled", "true")
spark.conf.set("spark.serializer", "org.apache.spark.serializer.KryoSerializer")

// CDM-specific optimizations
val df = spark.read
  .format("com.microsoft.cdm")
  .option("storage", "yourstorageaccount.dfs.core.windows.net")
  .option("manifestPath", "your-container/large-dataset/BigData.manifest.cdm.json")
  .option("entity", "LargeEntity")
  .option("maxCDMThreads", "8") // Increase for large datasets
  .load()
```

### Working with Partitioned Data

```scala
// Read from partitioned CDM data
val partitionedData = spark.read
  .format("com.microsoft.cdm")
  .option("storage", "yourstorageaccount.dfs.core.windows.net")
  .option("manifestPath", "your-container/partitioned-data/PartitionedManifest.manifest.cdm.json")
  .option("entity", "PartitionedEntity")
  .load()

// Leverage Spark's partition pruning for better performance
val filteredData = partitionedData.filter($"year" === 2024 && $"month" === 10)
```
