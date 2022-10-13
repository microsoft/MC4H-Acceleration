
# Data Cleanup in Microsoft FHIR Services

After a data migration testing cycle, when data entry errors have been identified or any other process generates inconsistent or plainly "bad" data, a clean up of the FHIR repository will be required. This page describes some of the possibilities available to a data steward when performing that cleanup.  

Applicable to both Azure Health Data Services (AHDS) and Azure API for FHIR.

## Reprovision the FHIR Service

If all data needs to be cleared from the FHIR Service, consider deleting and re-provisioning the Azure Resource (either the FHIR Service resource within the AHDS workspace, or the Azure API for FHIR resource). This will be quicker, cleaner, and more cost effective than executing a series of delete API calls. 

## Deleting in Bulk by Resource Type

If all records in a specific resource need to be deleted, then queries must first be made to capture the number of that resource type currently exists in FHIR. These counts are used to establish the correct number to delete next. If the number of entities is greater than 100, we’ll have to use paging as FHIR will only allow us to delete 100 entities at a time. 

### Get Resource Count
Send the following HTTP request to establish the number of Encounters currently in the system. 

`GET: {fhirURL}/Encounter?_summary=count`

### hardDELETE with Count

If the number of encounters returned exeeds 100, the DELETE operations require use of paging if the number of entities to be deleted exceeds 100. This means you can only do 100 at a time, but you should be able to run the same HTTP request repeatedly until you get through all of the entities. 

The following example uses an arbitrary filter for the date entities were last updated, but this could be any date you feel would encapsulate every entity, and assumes 1000 encounters were returned from the GET request. 

`/URL: {fhirURL}/Encounter?hardDelete=true&_lastUpdated=gt2001-01-01&_count={1000}`

## Deleting Specific Resources

When a more granular approach is necessary, because only some data needs to be deleted, the following examples can be helpful in developing your approach to delete data via API calls into the FHIR Service. 

> In best practice, verify that the data you are trying to remove exists before attempting to delete it, so that you can confirm your calls did exactly what you expect. 

### Search or Retrieve Calls

Below are some example queries for various to retrieve specific records, used both to confirm their existence prior to a delete, and to confirm they were deleted after.

| **Command** | **Description** | 
| --- | --- |  
`GET {fhirURL}/Patient/{patientId}` | Retrieve a resource (such as Patient) based on the resource’s unique FHIR ID (primary key _id_ element) |  
`GET {fhirURL}/Patient?identifier={identifierValue}` | Search for a resource (such as Patient) to find any matches in a resource’s array of identifiers. This is a search so it could return no matches, one match, or multiple matches. |  
`GET {fhirURL}/Patient/{patientId}/_history` | FHIR maintains a history of changes to a resource. This request returns the history – or changes over time – for a resource (such as Patient). |
`GET {fhirURL}/Coverage?beneficiary={patientId}` | This request searches for resources (such as Coverage) based on the id of an associated resource (such as Benficiary/Patient). This request will return zero, one, or many results. |

### DELETE Calls

Following are some specific examples for deleting _patient_ records:

| **Command** | **Description** |
| --- | --- |  
| `DELETE {fhirURL}/Patient/{patientId}` | If you know the specific resource (such as Patient) id (which is different from identifier), then this operation will delete that resource. |  
| `DELETE {fhirURL}/Patient/{patientId}?hardDelete=true` | Azure API for FHIR maintains a rich history of changes to a resource so simply deleting a resource may not be the complete purge you are wanting. If you want to clean up this resource history, then you should pass the hardDelete query string parameter in your HTTP request.  |
| `DELETE {fhirURL}/Patient/?identifier={identifier}` | This delete option searches for a resource (such as Patient) based on some search criteria (such as identifier). If a single resource matches this criteria, the resource will be deleted. |  
| `DELETE {fhirURL}/Patient?identifier={identifier}&_count={maximum#ToDelete}` | Note: This can be used with hardDelete parameter too. |  
| `DELETE {fhirURL}/Coverage?beneficiary={patientId}` | It’s quite common for resources to relate to other resources (such as Patient to Coverage). If you want to delete related resources, one option is to use this delete request to remove a resource based on it’s reference to another. Be mindful of deleting resources shared by other resources. For example, if you are deleting a patient, you may want to delete it’s related coverage resource, but the coverage is also related to a payor resource and you likely do not want to delete that payor organization referenced by other coverage resources.  Deleting a resource is a significant change and if multiple resources are returned by this search request, the delete will fail. |
| `DELETE {fhirURL}/Coverage?beneficiary={patientId}&_count={maxToDelete}` | If you want to intentionally delete multiple resources found in search criteria, you need to specify a count parameter. This count needs to exceed the number of search results or it will still fail on the assumption that it would delete more than the user expected. |  

> If you need to delete multiple resources, it is recommended to write custom code (i.e. plugins) or a Power Automate Flow to loop through a list of IDs or any other criteria that applies to your scenario.  
>
>You can search for multiple records and delete any that match your search criteria, but to do so, you need to provide the __count_ parameter and the maximum number of rows you want to delete.  
>
>If you don’t provide the __count_ parameter and multiple resources are returned, then you will get an _HTTP error_ response.  

### Complete Example
The below example shows step by step calls that might be used to delete all Coverages related to a Patient with _id = D000000001_.

1. Confirm existence of records:

    `GET {fhirURL}/Patient/D000000001`<br>
    `GET {fhirURL}/Coverage?beneficiary=D000000001`

2. Assuming the patient exists, and 2 unique coverages were returned as related to the patient, delete the coverages:

    `DELETE {fhirURL}/Coverage?beneficiary={D000000001}&_count={2}`

3. Confirm the patient is untouched, but the coverage records were successfully deleted:

    `GET {fhirURL}/Patient/D000000001`<br>
    `GET {fhirURL}/Coverage?beneficiary=D000000001`

## Additional Reading

- [FHIR REST API capabilities for Azure Health Data Services FHIR service | Microsoft Docs](https://docs.microsoft.com/en-us/azure/healthcare-apis/fhir/fhir-rest-api-capabilities)  
- [Azure FHIR Sync Agent Dataverse configuration | Microsoft Docs](https://docs.microsoft.com/en-us/dynamics365/industry/healthcare/sync-agent-dataverse-configuration#create-an-app-user-for-the-azure-fhir-sync-agent)
- [Search examples for FHIR service | Microsoft Docs](https://docs.microsoft.com/en-us/azure/healthcare-apis/fhir/search-samples)  
- [Http - FHIR v4.0.1 (hl7.org)](https://www.hl7.org/fhir/http.html#delete)
- [Search - FHIR v4.0.1 (hl7.org)](https://www.hl7.org/fhir/search.html)
