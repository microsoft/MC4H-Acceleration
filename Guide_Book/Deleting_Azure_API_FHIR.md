## Deleting from Azure API for FHIR

Make sure to verifying data exists before attempting to delete.

| **Command** | **Description** |
| --- | --- |  
| `GET {fhirURL}/Patient/{patientId}` | This request retrieves the resource (such as Patient) based on the resource’s unique ID. |  
| `GET {fhirURL}/Patient?identifier={identifier}` | This request executes a search for a resource (such as Patient) to find any matches in a resource’s array of identifiers. This is a search so it could return no matches, one match, or multiple matches. |  
| `GET {fhirURL}/Patient/{patientId}/_history` |FHIR maintains a history of changes to a resource. This request returns the history – or changes over time – for a resource (such as Patient). |
| `GET {fhirURL}/Coverage?beneficiary={patientId}` | This request searches for resources (such as Coverage) based on the id of an associated resource (such as Benficiary/Patient). This request will return zero, one, or many results. |
<br>

There are various options for deleting resources in Azure API for FHIR.  

Following are some specific examples for deleting _patient_ records

| **Command** | **Description** |
| --- | --- |  
| `DELETE {fhirURL}/Patient/{patientId}` | If you know the specific resource (such as Patient) id (which is different from identifier), then this operation will delete that resource. |  
| `DELETE {fhirURL}/Patient/{patientId}?hardDelete=true` | Azure API for FHIR maintains a rich history of changes to a resource so simply deleting a resource may not be the complete purge you are wanting. If you want to clean up this resource history, then you should pass the hardDelete query string parameter in your HTTP request.  |
| `DELETE {fhirURL}/Patient/?identifier={identifier}` | This delete option searches for a resource (such as Patient) based on some search criteria (such as identifier). If a single resource matches this criteria, the resource will be deleted. |  
| `DELETE {fhirURL}/Patient?identifier={identifier}&_count={maximum#ToDelete}` | Note: This can be used with hardDelete parameter too. |
<br>

>If you want to delete multiple resources, it is recommended to write code to loop through a list of IDs or any other criteria that applies to your scenario.  
You can search for multiple records and delete any that match your search criteria, but to do so, you need to provide the *_count* parameter and the maximum number of rows you want to delete.  
If you don’t provide the *_count* parameter and multiple resources are returned, then you will get an HTTP error response.  

<br>

Additional examples of deleting _beneficiary_ records:

| **Command** | **Description** |
| --- | --- |  
| `DELETE {fhirURL}/Coverage?beneficiary={patientId}` | It’s quite common for resources to relate to other resources (such as Patient to Coverage). If you want to delete related resources, one option is to use this delete request to remove a resource based on it’s reference to another. Be mindful of deleting resources shared by other resources. For example, if you are deleting a patient, you may want to delete it’s related coverage resource, but the coverage is also related to a payor resource and you likely do not want to delete that payor organization referenced by other coverage resources.  Deleting a resource is a significant change and if multiple resources are returned by this search request, the delete will fail. |
| `DELETE {fhirURL}/Coverage?beneficiary={patientId}&_count={maxToDelete}` | If you want to intentionally delete multiple resources found in search criteria, you need to specify a count parameter. This count needs to exceed the number of search results or it will still fail on the assumption that it would delete more than the user expected. |  
<br>