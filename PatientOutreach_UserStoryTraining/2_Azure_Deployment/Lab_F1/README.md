# Deploy FHIR Components with ARM Templates
>**Objective:**<br>
Understand the Azure API for FHIR to FHIR-Proxy realtionship, and use and ARM template to deploy the Azure API for FHIR, FHIR-Proxy and FHIR Bulk Loader. 

## Pre-requisites
* Azure Subscription: User must have rights to deploy resources at the Resource Group scope in their Azure Subscription (i.e. Contributor built-in role).
* Azure Active Directory (AAD): User must have Application Administrator rights for the AAD tenant attached to the Azure Subscription.

## Outcomes
* Azure API for FHIR (PaaS) deployed and available
* FHIR-Proxy (OSS) deployed and able to communicate with Azure API for FHIR
* FHIR-Bulk Loader (OSS) deployed and available
* Postman set up and able to make REST API calls to Azure API for FHIR

## Step 1:  
For this lab, you will complete a challenge published as a part of the Microsoft Cloud for Healthcare OpenHack. This deploys the FHIR components into a training environment. 
> NOTE: For an actual implementation, the ARM template will likely be adjusted for the customer's specific needs and processes, and a deployment process (ADO pipelines, etc.) should be designed around it. 

Open the following github repo in a new window, complete the instructions (only Challenge 01), and then return here. 
[Microsoft MC4H Open Hack - Challenge 01](https://github.com/openhack-mc4h/tree/main/Challenge-01)
<br>

> You completed the lab!<br>
Continue to the next module: [Dynamics 365 FHIR Integration](https://github.com/microsoft/MC4H-Acceleration/tree/main/PatientOutreach_UserStoryTraining/3_D365_FHIR_Integration)