# Adding a Patient
Most health organizations are going to start with FHIR by wanting to add a new Patient.

The first thing to think about is POST vs PUT.
- POST is the typical way you would execute a create in REST and it can be used the same way here. If you use POST to add a patient (or any other resource) to FHIR, the FHIR API will generate a unique ID for that patient. This will happen even if you provide an id in the body.
PUT uses the unique id you provide.
- Both POST and PUT requires a BODY that represents the patient we want to add.

Let’s say you want to create a basic Patient in FHIR, you might use:

HTTP Request: `POST {{fhirURL}}\Patient`
Request Body:
`{
    "resourceType": "Patient", 
    "identifier": [
        { "system": "myEMR", "value": "1234567890" }
    ], 
    "active": true, 
    "name": [
        { "use": "usual", "family": "Smith", "given": [ "Jane" ] }
    ], 
    "telecom": [
        { "use": "mobile", "value": "+15551234567" }
    ], 
    "gender": "female", 
    "birthDate": "1974-08-12", 
    "deceasedBoolean": false, 
    "address": [
        { "use": "home", "line": [ "1234 River Way" ], "city": "Nowhere", "state": "OH", "postalCode": "12345" }
    ]
}`

Since the above request is a POST, FHIR will generate a unique ID for the patient. The entire patient resource will be returned in the response - including the generated ID so you can take further action on that as needed.

Alternatively, you might use PUT to prevent FHIR from assigning a unique ID and provide your own unique ID instead. In case it's not obvious, this option means you are taking on the responsibility of ensuring uniqueness of the patient's ID.
HTTP Request: `PUT {{fhirURL}}\Patient\{{uniquePatientId}}`
Request Body:
`{
    "resourceType": "Patient", 
    "id": "{{uniquePatientId}}"
    "identifier": [
        { "system": "myEMR", "value": "1234567890" }
    ], 
    "active": true, 
    "name": [
        { "use": "usual", "family": "Smith", "given": [ "Jane" ] }
    ], 
    "telecom": [
        { "use": "mobile", "value": "+15551234567" }
    ], 
    "gender": "female", 
    "birthDate": "1974-08-12", 
    "deceasedBoolean": false, 
    "address": [
        { "use": "home", "line": [ "1234 River Way" ], "city": "Nowhere", "state": "OH", "postalCode": "12345" }
    ]
}`

## Additional Reading

- [FHIR REST API capabilities for Azure Health Data Services FHIR service | Microsoft Docs](https://docs.microsoft.com/en-us/azure/healthcare-apis/fhir/fhir-rest-api-capabilities)
- [Search examples for FHIR service | Microsoft Docs](https://docs.microsoft.com/en-us/azure/healthcare-apis/fhir/search-samples)  
- [Http - FHIR v4.0.1 (hl7.org)](https://www.hl7.org/fhir/http.html#delete)
- [Search - FHIR v4.0.1 (hl7.org)](https://www.hl7.org/fhir/search.html)
