# Patient Outreach User Story Training
> Deployment training of a single Patient Outreach User Story in the Microsoft Cloud for Healthcare

## Objective & Audience
**Training Objective:**<br>
Successfully plan for and deploy a fully integrated* Patient Outreach use case across multiple technologies using Microsoft Cloud for Healthcare components

>Note:<br>
This exercise uses SYNTHEA (fake) patient data to hydrate the Azure FHIR Services. Integration from an EHR such as EPIC is not included in this exercise, and is a significant effort that must be addressed in an actual customer implementation

**Intended Audience:**<br>
Technical Architects, Developers, Consultants with hands-on knowledge working with Microsoft Cloud Technologies

**Approximate Time:** 8 hours
## Pre-Requisite Trainings
* [Get started with Microsoft Cloud for Healthcare](https://docs.microsoft.com/en-us/learn/paths/healthcare-in-a-day/)
* [Microsoft Azure Healthcare APIs Workshop](https://github.com/microsoft/azure-healthcare-apis-workshop) (optional)
* [Unlock customer intent with Dynamics 365 Customer Insights](https://docs.microsoft.com/en-us/learn/paths/build-customer-insights/)
* [Microsoft Dynamics 365 Fundamentals: Marketing](https://docs.microsoft.com/en-us/learn/paths/explore-microsoft-dynamics-365-marketing/)

## Contents
| # | Module | Topics Covered | Hands-on Material |
| --- | --- | --- | --- |
| 0| [Microsoft Cloud for Healthcare Landscape](./0_MC4H_Landscape/) | <ls><li>Patient Outreach Capability & Feature Overview</li><li>Reference Architectures</li></ls> | <ls><li>[Lab M1: Deploy Patient Outreach Capability](./0_MC4H_Landscape/Lab_M1)</li></ls> | 
| 1 | [Agile Solution Design: A Patient Outreach User Story](./1_Solution_Design) | <ls><li>Defining the User Story</li><li>The MVP Solution Design</li></ls> | <ls><li>Sample User Story work breakdown (coming soon)</li></ls>|
| 2 | [Azure FHIR Deployments](./2_Azure_Deployment) | <ls><li>Azure FHIR Data Architecture</li></ls> | OpenHack-FHIR for MC4H<br><ls><li>[Challenge0: Prerequistes](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/OpenHack-FHIR0-Prerequistes)</li><li> [Challenge1: Deploy FHIR Components](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/OpenHack-FHIR1-DeployFHIR)</li><li>[Challenge2: Configure Service Client Authorization](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/OpenHack-FHIR2-AuthSetup)</li><li>[Challenge3: Bulk Load Synthea Data](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/OpenHack-FHIR3-BulkLoad)</li><li>[Challenge4: Deploy FHIR Sync Agent](https://github.com/microsoft/OpenHack-FHIR/tree/main/MC4H/OpenHack-FHIR4-FHIRSyncAgent)</li></ls> |
| 3 | [Azure FHIR - Dynamics 365 Integration](./3_D365_FHIR_Integration) | <ls><li>Azure FHIR Sync Agent Overview</li></ls> | OpenHack-FHIR for MC4H<br><ls><li>[Challenge5: FHIR Sync to Dataverse Configuration](https://github.com/microsoft/OpenHack-FHIR/blob/main/MC4H/Challenge5-FHIRSyncDV) |
| 4 | [Customer Insights Segmentation](./4_Customer_Insights_Config)</li></ls> | <ls><li>What is Customer Insights?</li><li>Integration Architectures</li></ls> | <ls><li>[Lab CI1: Ingest FHIR Data](./4_Customer_Insights_Config/Lab_CI1)</li><li>[Lab CI2: Ingest Dataverse Data](./4_Customer_Insights_Config/Lab_CI2)</li><li>[Lab CI3: Map, Match & Merge](./4_Customer_Insights_Config/Lab_CI3)</li><li>[Lab CI4: Define Relationships & Activities](./4_Customer_Insights_Config/Lab_CI4)</li><li>[Lab CI5: Define Patient Measures & Segments](./4_Customer_Insights_Config/Lab_CI5)</li></ls> |
| 5 | Dynamics 365 Configuration<br>(coming soon) | <ls><li>Customer Insights Customer Card Overview</li><li>Patient Journey Overview</li></ls> | <ls><li>Lab D1: Integrate Unified Profile Customer Card Add-in</li><li>Lab D2: Simple Email Patient Journey</li></ls> |