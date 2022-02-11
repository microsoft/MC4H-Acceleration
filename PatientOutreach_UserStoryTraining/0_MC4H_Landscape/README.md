# Microsoft Cloud for Healthcare Landscape
> After reviewing this material, get hands on experience with:<br>
[Lab M1: Deploy Patient Outreach Capability](./Lab_M1)

## Patient Outreach: A Microsoft Cloud for Healthcare Capability

The **Patient Outreach** capability is delivered as an enabler for **Personalized Care** within the **Enhanced Patient Engagement** prioritized healthcare scenario.

![Microsoft Cloud for Healthcare Conceptual Architecture](./MC4H_Ecosystem_PersonalizedCare.png)

![Personalized Care Capabilities including Patient Outreach](./PersonalizedCare_Capabilities.png)

## Patient Outreach Feature Summary

The Patient Outreach capability of the Microsoft Cloud for Healthcare is built on a set of FHIR Data Capabilities in Azure and Dynamics 365 base features, with added configurations, examples, and new Apps. 

### KEY DATA PLATFORM FEATURES
| **Azure Managed APIs** | **Azure OSS Components** | **Connectors**| 
| :---: | :---: | :---: |
| Azure API for FHIR | FHIR Bulk Loader | Azure FHIR PQ Connector |
| Healthcare APIs (preview) | FHIR Proxy | |
| | FHIR Sync Agent | |
### KEY DYNAMICS 365 BASE FEATURES
| **Dynamics 365 Marketing** | **Dynamics 365 Customer Insights** |
| :----: | :---: |
| Segmentation | Measures / KPIs |
| Multi-channel campaigns (Customer Journeys) | Advanced Segmentation |
| Campaign Automation & Tracking | Customer Card for D365 |
| Event Management |  |
| Lead Scoring & Management | |
| Social Engagement| |
| Sample Patient Event Definitions | |

### CLOUD FOR HEALTHCARE ADDED FEATURES
| **Patient Outreach App** | **Dynamics 365 Customer Insights** |
| :----: | :---: |
| Healthcare-branded Outbound Marketing model app | FHIR Sync Agent Integration Mgt |
| Healthcare Data Model (CDM) | FHIR to CDM for Health Mapping |
| Healthcare Forms & Views | FHIR Synced Clinical Data Mgt Center |
| Patient Timeline Control | |
| Care Team Visualization Control | |
| Sample Patient Segment Definitions | |
| Sample Patient Event Definitions | |


## Reference Architectures

Below is a high level reference architecture that shows one potential way these pieces can work together for a full stack Microsoft Cloud for Heatlhcare solution to Patient Outreach. In this training, we will tailor this to the specific user story. 

![Patient Outreach Reference Architecture](./PatientOutreach_RefArchitecture.png)

> **TO DO... ADD Microsoft Reference Architecture for Healthcare Data (Azure FHIR ref architecture)**