# Spark 3.5 Upgrade Documentation

This folder contains documentation for the Spark CDM Connector upgrade from Spark 3.3 to Spark 3.5 for Microsoft Fabric Runtime 1.3 and Azure Synapse Analytics Spark Pool 3.5 compatibility.

> **⚠️ LIMITED TESTING DISCLAIMER**: The testing of features of the Spark CDM Connector is limited, so not all features might work as expected. Use with caution and test thoroughly in your specific environment.

## 📁 Documentation Structure

| Document | Purpose |
|----------|---------|
| [SPARK_3.5_UPGRADE_ANALYSIS.md](./SPARK_3.5_UPGRADE_ANALYSIS.md) | Pre-upgrade feasibility analysis, compatibility assessment, risk evaluation, and implementation planning |
| [UPGRADE_IMPLEMENTATION_SUMMARY.md](./UPGRADE_IMPLEMENTATION_SUMMARY.md) | Implementation details, code changes, issues resolved, and upgrade benefits |
| [PACKAGE_BUILD_STATUS.md](./PACKAGE_BUILD_STATUS.md) | Build environment, JAR specifications, build commands, and deployment instructions |

## 🎯 Upgrade Summary

| Property | Value |
|----------|-------|
| **Source Version** | spark3.3-1.19.7 |
| **Target Version** | spark3.5-1.20.0 |
| **Status** | ✅ COMPLETED |
| **Output JAR** | `target/spark-cdm-connector-assembly-spark3.5-1.20.0.jar` |

**Key Achievements**:
- ✅ Upgraded from Spark 3.3.0 to 3.5.0
- ✅ Full read and write support for CDM folders
- ✅ Compatible with Fabric Runtime 1.3 and Synapse Spark Pool 3.5
- ✅ No breaking changes for existing applications

## 📚 Related Documentation

- [Main README](../../README.md) - Project overview
- [Installation Guide](../INSTALLATION_GUIDE.md) - Fabric and Synapse deployment
- [Usage Overview](../overview.md) - Technical documentation

---

*Last updated: December 2025*