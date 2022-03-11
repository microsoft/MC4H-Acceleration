# Azure FHIR Data Architectures
> After reviewing this material, get hands on experience with the OpenHack-FHIR for MC4H challenges below:
> * [Challenge0 - Prerequistes](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/Challenge0-Prerequistes)
> * [Challenge1 - Deploy FHIR Components](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/Challenge1-DeployFHIR)
> * [Challenge2 - Configure Service Client Authorization](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/Challenge2-AuthSetup)
> * [Challenge3 - Bulk Load Synthea Data](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/Challenge3-BulkLoad)
> * [Challenge4 - Deploy FHIR Sync Agent](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/Challenge4-FHIRSyncAgent)
> <br>
> Continue to next module: [Dynamics 365 FHIR Integration](https://github.com/microsoft/MC4H-Acceleration/tree/main/PatientOutreach_UserStoryTraining/3_D365_FHIR_Integration)

FHIR® (Fast Healthcare Interoperability Resources) is an industry standard for health data storage and exchange. Microsoft is strategically adopting FHIR as the standard format for health data storage and exchange in the Microsoft health data platform. 

To support various FHIR infrastructure for Healthcare Data patterns in Azure, there are both managed Azure Services and Connectors fully supported by Microsoft Engineering, and Open Source Software (OSS) projects available for use. 

| Name (link to more info) | Description |
| --- | --- |
| [Azure API for FHIR](https://docs.microsoft.com/en-us/azure/healthcare-apis/azure-api-for-fhir/overview) | Fully managed, standards-based, FHIR compliant API and stateful data repository. This managed Azure Service is based on the FHIR Server OSS project (below) |
| [Azure Healthcare APIs (preview)](https://azure.microsoft.com/en-us/services/healthcare-apis/) | An evolved verion of Azure API for FHIR that will offer additional technology and services |
| [FHIR Server](https://github.com/microsoft/fhir-server) | An open-source implementation of a standards-based, FHIR compliant API and stateful data |
| [FHIR Proxy](https://github.com/microsoft/fhir-proxy) | An open-source implementation of a secure FHIR Gateway and Proxy that wraps the Azure API for FHIR with an intelligent checkpoint capable of filtering based on admin-defined rules|
| [FHIR Bulk Loader & Export](https://github.com/microsoft/fhir-loader) | An open-source implementation of bulk import and export features for Azure API for FHIR | 
| [FHIR Sync Agent](https://github.com/microsoft/fhir-cds-agent)<br> (private repo)| An Azure implementation of an integration engine for synchronizing health data between Azure API for FHIR and Dataverse |

In this training, Azure API for FHIR, FHIR Proxy, FHIR Bulk Loader & Export, and FHIR Sync Agent will be utilized together to build a complete FHIR architecture allowing ingestion and storage of FHIR formatted healthcare data for access to and use in Dynamics 365 Marketing and Customer Insights.

# Reference Architecture
![Healthcare Data Reference Architecture in Azure](./images/AzureFHIRReferenceDataFlow.png)

Click the links above of the Azure API for FHIR, FHIR Proxy, and FHIR Bulk Loader, to review the capabilities and how they work together. 
***

Complete the labs:

* The below links will take you OpenHack-FHIR for MC4H labs pertinent to this section:
    * [Challenge0 - Prerequistes](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/Challenge0-Prerequistes)
    * [Challenge1 - Deploy FHIR Components](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/Challenge1-DeployFHIR)
    * [Challenge2 - Configure Service Client Authorization](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/Challenge2-AuthSetup)
    * [Challenge3 - Bulk Load Synthea Data](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/Challenge3-BulkLoad)
    * [Challenge4 - Deploy FHIR Sync Agent](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/Challenge4-FHIRSyncAgent)

* Continue to next module: [Dynamics 365 FHIR Integration](https://github.com/microsoft/MC4H-Acceleration/tree/main/PatientOutreach_UserStoryTraining/3_D365_FHIR_Integration)

