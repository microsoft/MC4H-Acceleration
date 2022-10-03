Most health organizations are going to start with FHIR by wanting to add a new Patient.

The first thing to think about is POST vs PUT.
- POST is the typical way you would execute a create in REST and it can be used the same way here. If you use POST to add a patient (or any other resource) to FHIR, the FHIR API will generate a unique ID for that patient. This will happen even if you provide an id in the body.
PUT uses the unique id you provide.
- Both POST and PUT requires a BODY that represents the patient we want to add.

Let’s say you want to create a basic Patient in FHIR, you might use:

HTTP Request: POST {{fhirURL}}\Patient
Request Body:
{
    "resourceType": "Patient", 
    "identifier": [
        {
            "system": "myEMR", 
            "value": "1234567890"
        }
    ], 
    "active": true, 
    "name": [
        {
            "use": "usual", 
            "family": "Smith", 
            "given": [
                "Jane"
            ]
        }
    ], 
    "telecom": [
        {
            "use": "mobile", 
            "value": "+15551234567"
        }
    ], 
    "gender": "female", 
    "birthDate": "1974-08-12", 
    "deceasedBoolean": false, 
    "address": [
        {
            "use": "home", 
            "line": [
                "1234 River Way"
            ], 
            "city": "Nowhere", 
            "state": "OH", 
            "postalCode": "12345"
        }
    ]
}
