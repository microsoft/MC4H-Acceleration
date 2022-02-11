# Microsoft Cloud for Healthcare Landscape
> After reviewing this material, get hands on experience with:<br>
[Lab M1: Deploy Patient Outreach Capability](./Lab_M1)

## Patient Outreach: A Microsoft Cloud for Healthcare Capability

The **Patient Outreach** capability is delivered as an enabler for **Personalized Care** within the **Enhanced Patient Engagement** prioritized healthcare scenario.

![Microsoft Cloud for Healthcare Conceptual Architecture](./MC4H_Ecosystem_PersonalizedCare.png)

![Personalized Care Capabilities including Patient Outreach](./PersonalizedCare_Capabilities.png)

## Patient Outreach Feature Summary

The Patient Outreach capability of the Microsoft Cloud for Healthcare is built on a set of FHIR Data Capabilities in Azure and Dynamics 365 base features, with added configurations, examples, and new Apps. 

### Key Horizontal Microsoft Cloud Features
| **Azure Services** | **Open Source (OSS) on Azure** |  **Dynamics 365 Marketing** | **Dynamics 365 Customer Insights** | 
| :---: | :---: | :----: | :---: |
| Azure API for FHIR | FHIR Server | Segmentation | Measures / KPIs |
| Healthcare APIs (preview) | FHIR Bulk Loader | Multi-channel campaigns (Customer Journeys) | Advanced Segmentation |
| | FHIR Proxy  | Campaign Automation & Tracking | Customer Card for D365 |
| | FHIR Sync Agent | Event Management |  |
| | | Lead Scoring & Management | |
| | | Social Engagement| |

### Key Cloud for Healthcare Added Features
| **Base Dataverse Extensions** | **Patient Outreach App** | **Sync Admin for FHIR App** | **Connectors** 
| :---: | :---: | :---: | :---: |
| Healthcare Data Model (CDM) | Healthcare-branded Outbound Marketing model app | FHIR Sync Agent Integration Mgt | Azure FHIR for Power Query |
| Healthcare Forms & Views | Sample Patient Segment Definitions | FHIR to CDM for Health Mapping | FHIRBase for Power Platform  (preview) |
| Patient Timeline Control | Sample Patient Event Definitions | FHIR Synced Clinical Data Mgt Center | FHIRClinical for Power Platform (preview) |
| Care Team Visualization Control  | | | |



## Reference Architectures

Below is a high level reference architecture that shows one potential way these pieces can work together for a full stack Microsoft Cloud for Heatlhcare solution to Patient Outreach. In this training, we will tailor this to the specific user story. 

![Patient Outreach Reference Architecture](./PatientOutreach_RefArchitecture.png)

> **TO DO... ADD Microsoft Reference Architecture for Healthcare Data (Azure FHIR ref architecture)**