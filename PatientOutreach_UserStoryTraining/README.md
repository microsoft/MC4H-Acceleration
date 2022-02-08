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
* Microsoft Cloud for Healthcare In a Day
* Azure FHIR Open Hack

## Training Agenda

0. [Microsoft Cloud for Healthcare Landscape](./0_MC4H_Landscape/)
    * [Patient Outreach Capability & Feature Overview](./0_MC4H_Landscape#patient-outreach-a-microsoft-cloud-for-healthcare-capability)
    * [Reference Architectures](./PatientOutreach_UserStoryTraining/0_MC4H_Landscape#reference-architectures)
    * [Lab 1: Deploy Patient Outreach Capability](./0_MC4H_Landscape/Lab1)
1.  [Agile Solution Design: A Patient Outreach User Story](./1_Solution_Design)
    * [Defining the User Story](./PatientOutreach_UserStoryTraining/1_Solution_Design#defining-the-user-story)
    * [The MVP Solution Design](./PatientOutreach_UserStoryTraining/1_Solution_Design#the-mvp-solution-design)
2. Azure Deployment
    * Microsoft Reference Architecture for Healthcare Data
    * Azure API for FHIR Overview
    * FHIR Proxy OSS Overview
    * FHIR Loader OSS Overview
    * Lab 2: Deploy Azure FHIR Reference Architecture with ARM Templates
3. Dynamics 365 FHIR Integration
    * Azure FHIR Sync Agent Overview
    * Lab 3: Dataverse Pre-Setup & Config
    * Lab 4: FHIR Sync Agent Deployment
    * Lab 5: Integration Settings & Administration
4. [Customer Insights Segmentation](./4_Customer_Insights_Config)
    * [What is Customer Insights?](./PatientOutreach_UserStoryTraining/4_Customer_Insights_Config#what-is-customer-insights)
    * [Integration Architectures](./PatientOutreach_UserStoryTraining/4_Customer_Insights_Config#what-is-customer-insights)
    * Lab 6: Ingest FHIR Data
    * Lab 7: Ingest Dataverse Data
    * Lab 8: Map, Match & Merge
    * Lab 9: Relationships & Activities
    * Lab 10: Create Measures & Segments
4. Dynamics 365 Configuration
    * Customer Insights Customer Card Overview
    * Patient Journey Overview
    * Lab 11: Integrate Unified Profile Customer Card Add-in
    * Lab 12: Simple Email Patient Journey