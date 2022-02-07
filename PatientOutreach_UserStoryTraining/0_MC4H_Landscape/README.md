# Microsoft Cloud for Healthcare Landscape
## Patient Outreach: A Microsoft Cloud for Healthcare Capability
The **Patient Outreach** capability is delivered as an enabler for **Personalized Care** within the **Enhanced Patient Engagement** prioritized healthcare scenario.

![Microsoft Cloud for Healthcare Conceptual Architecture](./MC4H_Ecosystem_PersonalizedCare.png)

![Personalized Care Capabilities including Patient Outreach](./PersonalizedCare_Capabilities.png)

## Patient Outreach Feature Summary
The Patient Outreach capability of the Microsoft Cloud for Healthcare is built on a set of FHIR Data Capabilities in Azure and Dynamics 365 base features, with added configurations, examples, and new Apps. 

| DATA PLATFORM FEATURES | DYNAMICS 365 BASE FEATURES | MC4H ADDED FEATURES |
| :----: | :----: | :---: |
| **Azure Managed APIs** | **Dynamics 365 Marketing** | **Patient Outreach App** |
| Azure API for FHIR | Segmentation | Healthcare-branded Outbound Marketing model app |
| Healthcare APIs (preview) | Multi-channel campaigns (Customer Journeys) | Healthcare Data Model (CDM) |
| | Campaign Automation & Tracking | Healthcare Forms & Views |
| **Azure OSS Components** | Event Management | Patient Timeline Control |
| FHIR Bulk Loader | Lead Scoring & Management | Care Team Visualization Control |
| FHIR Proxy | Social Engagement| Sample Patient Segment Definitions |
| FHIR Sync Agent | | Sample Patient Event Definitions |
| | **Dynamics 365 Customer Insights** | |
| **Connectors** | Measures / KPIs | **Sync Admin for FHIR App**|
| Azure FHIR PQ Connector | Advanced Segmentation | FHIR Sync Agent Integration Mgt|
| | Customer Card for D365 | FHIR to CDM for Health Mapping |
| | | FHIR Synced Clinical Data Mgt Center |

## Reference Architectures
Below is a high level reference architecture that shows one potential way these pieces can work together for a full stack Microsoft Cloud for Heatlhcare solution to Patient Outreach. In this training, we will tailor this to the specific user story. 

![Patient Outreach Reference Architecture](./PatientOutreach_RefArchitecture.png)

> TO DO... ADD Microsoft Reference Architecture for Healthcare Data (Azure FHIR ref architecture)