# Spark CDM Connector Upgrade Analysis: From Spark 3.3 to Spark 3.5

## Executive Summary

This document analyzes the feasibility of upgrading the Spark CDM Connector from Apache Spark 3.3 to Apache Spark 3.5 for compatibility with Microsoft Fabric Runtime 1.3 and Azure Synapse Analytics Spark Pool 3.5.

Based on comprehensive code analysis and compatibility review, **the upgrade is feasible with moderate effort** requiring targeted changes in specific areas.

> **Note**: For implementation details, see [UPGRADE_IMPLEMENTATION_SUMMARY.md](./UPGRADE_IMPLEMENTATION_SUMMARY.md).

## Pre-Upgrade State

### Project Overview
| Property | Value |
|----------|-------|
| **Current Version** | spark3.3-1.19.7 |
| **Target Version** | spark3.5-1.20.0 |
| **Architecture** | DataSource V2 API based connector |
| **Language** | Scala 2.12.15 |
| **Build System** | SBT 1.5.5 |

### Dependencies Requiring Updates
| Dependency | Current Version | Target Version |
|------------|-----------------|----------------|
| Spark Core | 3.3.0 | 3.5.0 |
| Spark SQL | 3.3.0 | 3.5.0 |
| Jackson | 2.13.4 | 2.15.2 |
| Scala | 2.12.15 | 2.12.15 (no change) |
| CDM Standards | 2.8.0 | 2.8.0 (no change) |
| Hadoop | 3.3.1 | 3.3.1 (no change) |

## Compatibility Analysis

### ✅ Positive Compatibility Indicators

1. **DataSource V2 API Stability**
   - The connector uses Spark's DataSource V2 API which is stable across 3.x versions
   - Core interfaces (`SupportsCatalogOptions`, `Table`, `ScanBuilder`, etc.) remain unchanged
   - No breaking changes identified in migration guides for V2 API between 3.3 and 3.5

2. **Scala Version Compatibility**
   - Current Scala 2.12.15 is compatible with Spark 3.5
   - No Scala version upgrade required

3. **Core Architecture Compatibility**
   - Catalog-based implementation using `CatalogPlugin` and `TableCatalog` interfaces
   - Read/Write operations through standard V2 API patterns
   - No deprecated APIs in current codebase

4. **Target Runtime Environments**
   - Java 11 support (project already targets Java 11)
   - ADLS Gen2 integration patterns remain stable
   - Authentication mechanisms (MSI, SAS, App Registration) unchanged

### ⚠️ Areas Requiring Attention

1. **JavaConverters Import Compatibility**
   - `scala.collection.JavaConverters` shows deprecation warnings in newer Scala versions
   - Files affected: 7 files including `SparkTable.scala`, `CDMModelCommon.scala`
   - Note: `scala.jdk.CollectionConverters` is NOT available in Scala 2.12.15
   - Recommendation: Keep existing imports for Scala 2.12.15 compatibility

2. **Hadoop LZO Dependency**
   - External repository (`https://maven.twttr.com/`) may have connectivity issues
   - Dependency: `com.hadoop.gplcompression:hadoop-lzo:0.4.20`
   - Impact: LZO compression support (minimal use cases affected)
   - Recommendation: Consider disabling if build issues occur

3. **Dependency Version Alignment**
   - Jackson libraries need update to 2.15.2 for Spark 3.5 compatibility
   - Hadoop libraries 3.3.1 verified compatible
   - MSAL4J 1.10.1 confirmed compatible

### 🔍 Low-Risk Areas

1. **SQL Migration Guide Review**
   - Most breaking changes in Spark 3.4→3.5 relate to SQL/DataFrame operations
   - CDM connector primarily uses DataSource V2 APIs which are more stable
   - No identified impact on connector's core functionality

2. **Performance Optimizations**
   - Spark 3.5 includes performance improvements that benefit the connector automatically
   - No code changes required to leverage these improvements

## Implementation Plan

### Phase 1: Dependency Updates
1. Update `build.sbt` with new Spark and Jackson versions
2. Update version identifier to "spark3.5-1.20.0"
3. Address any repository connectivity issues (hadoop-lzo)
4. Maintain existing shading rules for conflict prevention

### Phase 2: Code Modernization
1. Review and fix any JavaConverters compatibility issues
2. Update files with deprecated imports if needed
3. Verify all source files compile successfully

### Phase 3: Testing & Validation
1. Create comprehensive CDM test data
2. Execute unit tests
3. Perform integration testing with target runtimes

### Phase 4: Documentation & Release
1. Update README.md and overview.md
2. Create installation guide for Fabric and Synapse
3. Document any breaking changes or limitations

## Risk Assessment

### Low Risk
- DataSource V2 API compatibility (stable across versions)
- Core connector functionality (architecture validated)
- Authentication patterns (unchanged)

### Medium Risk
- Dependency version conflicts (requires testing)
- JavaConverters compatibility (may need import adjustments)
- Build configuration changes (SBT updates)

### Mitigations
- Comprehensive testing before release
- Maintain backward compatibility where possible
- Document any limitations clearly

## Conclusion

The upgrade from Spark 3.3 to Spark 3.5 is **feasible and recommended** for Microsoft Fabric Runtime 1.3 and Azure Synapse Analytics Spark Pool 3.5 compatibility. The DataSource V2 API stability and Scala 2.12.15 compatibility provide a solid foundation for the upgrade with minimal risk to existing functionality.

---

*Analysis Date: October 2025*
