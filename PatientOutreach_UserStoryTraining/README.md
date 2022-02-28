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
    * Patient Outreach Capability & Feature Overview
    * Reference Architectures
    * [Lab M1: Deploy Patient Outreach Capability](./0_MC4H_Landscape/Lab_M1)
1.  [Agile Solution Design: A Patient Outreach User Story](./1_Solution_Design)
    * Defining the User Story
    * The MVP Solution Design
2. [Azure FHIR Deployments](./2_Azure_Deployment)
    * FHIR Offerings in Azure
    * [Lab F1: Deploy FHIR Reference Architecture with ARM Templates](./2_Azure_Deployment/Lab_F1)
3. Dynamics 365 FHIR Integration
    * Azure FHIR Sync Agent Overview
    * Lab S1: Dataverse Pre-Setup & Config
    * Lab S2: FHIR Sync Agent Deployment
    * Lab S3: Integration Settings & Administration
4. [Customer Insights Segmentation](./4_Customer_Insights_Config)
    * What is Customer Insights?
    * Integration Architectures
    * [Lab CI1: Ingest FHIR Data](./4_Customer_Insights_Config/Lab_CI1)
    * [Lab CI2: Ingest Dataverse Data](./4_Customer_Insights_Config/Lab_CI2)
    * [Lab CI3: Map, Match & Merge](./4_Customer_Insights_Config/Lab_CI3)
    * [Lab CI4: Define Relationships & Activities](./4_Customer_Insights_Config/Lab_CI4)
    * [Lab CI5: Define Patient Measures & Segments](./4_Customer_Insights_Config/Lab_CI5)
5. Dynamics 365 Configuration
    * Customer Insights Customer Card Overview
    * Patient Journey Overview
    * Lab D1: Integrate Unified Profile Customer Card Add-in
    * Lab D2: Simple Email Patient Journey