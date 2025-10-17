# Spark CDM Connector - Fabric Runtime 1.3 Edition

> **🚨 IMPORTANT DISCLAIMER**  
> This is a **private fork** of the original [Azure Spark CDM Connector](https://github.com/Azure/spark-cdm-connector) maintained independently for Microsoft Fabric Runtime 1.3 compatibility. This fork is **NOT officially supported by Microsoft** and is provided as-is for community use.
> 
> **📋 Please read the full [DISCLAIMER](DISCLAIMER.md) before using this software.**

## About This Fork

This repository contains an upgraded version of the Spark CDM Connector specifically optimized for:
- **Apache Spark 3.5** (Microsoft Fabric Runtime 1.3)
- **Enhanced Fabric Integration**
- **Modern Dependency Management**

### 🎯 Key Improvements

- ✅ **Spark 3.5 Compatibility**: Full support for Apache Spark 3.5.0
- ✅ **Fabric Runtime 1.3**: Native integration with Microsoft Fabric
- ✅ **Updated Dependencies**: Jackson 2.15.2, CDM Standards 2.8.0
- ✅ **Enhanced Authentication**: MSAL4J integration for Azure AD
- ✅ **Performance Optimizations**: Leverages latest Spark engine improvements

### 📋 Version Information

| Component | Version | Notes |
|-----------|---------|-------|
| **CDM Connector** | spark3.5-1.20.0 | This fork |
| **Apache Spark** | 3.5.0 | Target runtime |
| **Fabric Runtime** | 1.3 | Compatible |
| **Scala** | 2.12.15 | Language version |
| **Java** | 11+ | Required runtime |

### 🏗️ Original Project

This fork is based on the official Microsoft Azure Spark CDM Connector:
- **Original Repository**: https://github.com/Azure/spark-cdm-connector
- **Last Synced Version**: spark3.3-1.19.7
- **Original License**: MIT License

For the **official Microsoft-supported version**, please use the [original Azure repository](https://github.com/Azure/spark-cdm-connector).

### ⚠️ Support and Maintenance

- **Official Support**: Use the [original Azure repository](https://github.com/Azure/spark-cdm-connector) for Microsoft-supported versions
- **Fork Maintenance**: This fork is maintained independently and may not receive regular updates
- **Community Contributions**: Issues and PRs are welcome but response time may vary
- **Production Use**: Consider the support implications before using in production environments

### 📚 Documentation

- **Installation Guide**: [Fabric Installation Guide](documentation/FABRIC_INSTALLATION_GUIDE.md)
- **Usage Overview**: [Using the Spark CDM Connector](documentation/overview.md)
- **Upgrade Details**: [Spark 3.5 Upgrade Documentation](documentation/spark_35_upgrade/)

### 🔗 Resources

- **CDM Documentation**: https://docs.microsoft.com/en-us/common-data-model/
- **Microsoft Fabric**: https://docs.microsoft.com/en-us/fabric/
- **Original Connector Issues**: https://github.com/Azure/spark-cdm-connector/issues

### 📝 License

This fork maintains the same MIT License as the original project. See [LICENSE](LICENSE) for details.
