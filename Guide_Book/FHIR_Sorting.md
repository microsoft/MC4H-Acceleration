# Sorting FHIR resources
Sorting query results can be done on the client or on the server. If you want the server to sort for you, here are some examples:

HTTP Request: `GET {{fhirURL}}/Patient?_sort=status`

HTTP Request: `GET {{fhirURL}}/Observation?_sort=status,-date,category`

## Additional Reading

- [FHIR REST API capabilities for Azure Health Data Services FHIR service | Microsoft Docs](https://docs.microsoft.com/en-us/azure/healthcare-apis/fhir/fhir-rest-api-capabilities)
- [Search examples for FHIR service | Microsoft Docs](https://docs.microsoft.com/en-us/azure/healthcare-apis/fhir/search-samples)  
- [Http - FHIR v4.0.1 (hl7.org)](https://www.hl7.org/fhir/http.html#delete)
- [Search - FHIR v4.0.1 (hl7.org)](https://www.hl7.org/fhir/search.html)
