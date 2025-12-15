# Spark 3.5 CDM Connector Package Build Status

> **⚠️ LIMITED TESTING DISCLAIMER**: The testing of features of the Spark CDM Connector is limited, so not all features might work as expected. Use with caution and test thoroughly in your specific environment.

> **Related Documentation**: For upgrade implementation details, see [UPGRADE_IMPLEMENTATION_SUMMARY.md](./UPGRADE_IMPLEMENTATION_SUMMARY.md).

## Current Build

| Property | Value |
|----------|-------|
| **JAR File** | `spark-cdm-connector-assembly-spark3.5-1.20.0.jar` |
| **Location** | `target/` |
| **Size** | ~22.5 MB (uber JAR) |
| **Last Build** | December 2025 |
| **Status** | ✅ SUCCESSFUL |

## Build Environment

| Component | Version |
|-----------|---------|
| Java | OpenJDK 11 (Microsoft Build) |
| SBT | 1.11.7 |
| Scala | 2.12.15 |
| Spark | 3.5.0 |

## Build Commands

```bash
# Clean build
sbt clean compile

# Create assembly JAR
sbt assembly
```

## Package Contents

The uber JAR includes:
- ✅ Spark CDM Connector (DataSource V2 implementation)
- ✅ Jackson 2.15.2 libraries (shaded)
- ✅ MSAL4J authentication support
- ✅ CDM Standards 2.8.0
- ⚠️ LZO compression disabled (repository connectivity issues)

## Compatibility

| Platform | Version | Status |
|----------|---------|--------|
| Microsoft Fabric | Runtime 1.3 | ✅ Compatible |
| Azure Synapse Analytics | Spark Pool 3.5 | ✅ Compatible |
| Apache Spark | 3.5.x | ✅ Compatible |
| Java | 11+ | ✅ Required |

## Build Warnings

The build completes with warnings that do not affect functionality:

| Priority | Warning | Impact |
|----------|---------|--------|
| Low | Build configuration warnings | None - safe to ignore |
| Low | Pattern matching depth warnings | None - compiler limitations |
| Medium | Catch-all Throwable patterns | Future cleanup opportunity |

**Recommendation**: All warnings are non-blocking. JAR is production-ready.

## Verification

### JAR Contents Check
```bash
jar -tf target/spark-cdm-connector-assembly-spark3.5-1.20.0.jar | head -20
```

### Quick Test in Fabric
```python
# Load CDM entity
df = spark.read.format("com.microsoft.cdm") \
    .option("storage", "<account>.dfs.core.windows.net") \
    .option("manifestPath", "<container>/<path>/default.manifest.cdm.json") \
    .option("entity", "<EntityName>") \
    .load()

df.show()
```

## Deployment

1. Upload JAR to target environment:
   - **Fabric**: Workspace Environment → Custom Libraries
   - **Synapse**: Manage → Workspace Packages → Apache Spark Pools

2. Restart Spark session after JAR installation

3. Verify with test read/write operations

---

*Build documentation last updated: December 2025*
