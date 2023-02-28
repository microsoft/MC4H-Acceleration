# Patching FHIR Resources

[Azure API for FHIR](https://learn.microsoft.com/en-us/azure/healthcare-apis/azure-api-for-fhir/fhir-rest-api-capabilities#patch-and-conditional-patch) supports [REST Patch](https://www.rfc-editor.org/rfc/rfc5789) and Conditional Patching for updating elements of resources without replacing/updating the entirety of the resource e.g. changing a patient given name without replacing the whole patient resource. There are two methods of patching FHIR resources: FHIR Patch or JSON Patch. Both support conditional patching viz. perfomring an update on an element of a resource based on some criteria. However what kind of criteria you can use in your conditional is different from one method of patching versus another. Generally speaking, *unless* the client performing the patch has a copy of the server's version of the data and therefore knows initimately the size and shape of that resource, it is recommended that you use FHIR Patch over JSON Patch due to more advanced querying capabilities through FHIR Path.

# FHIR Patch

FHIR Patch allows you to update elements on a FHIR resource without updating the full resource. Below is simple example FHIR Patch request to add an identifier:
```
PATCH {{baseUrl}}/Patient/{{patientId}}
Content-Type: application/fhir+json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "operation",
            "part": [
                {
                    "name": "type",
                    "valueCode": "add"
                },
                {
                    "name": "path",
                    "valueString": "Patient"
                },
                {
                    "name": "name",
                    "valueString": "identifier"
                },
                {
                    "name": "value",
                    "valueIdentifier": {
                        "system": "example-system",
                        "value": "123"
                    }
                }
            ]
        }
    ]
}
```
FHIR Patch requests are easily identifiable by their Content-Type of application/fhir+json and that, like most FHIR requests, the body starts by declaring a resourceType. In our body we are providing a single [parameter](https://www.hl7.org/fhir/parameters.html) in our array of parameters. Each parameter is an operation. FHIR Patch supports add, insert, delete, replace, and move [operations](https://www.hl7.org/fhir/fhirpatch.html#concept). In this case, ours is a simple add operation. Add appends a resource to the element defined in the path using the name. Here our path is simply Patient. In more complex conditional examples, our path can use [FHIR Path](http://hl7.org/fhirpath/N1/) to identify nodes based on various criteria. Lastly, we provide the value we want to insert into the identifier array on the patient. Notice we use "valueIdentifier." If we were providing a different element (which would have to happen at a different path since identifiers only support a certain element type) we would change it to be value{{element type here}}.

Here is another more complex example where we are our patient has gotten married, moved their primary address, and changed their name and as a result we want to 
- replace their family/surname name
- delete their previous address
- add a new address
- replace their marital status
While you can perform a replace on their existing address, deleting and adding to demonstrate more variety of techniques available to you and it seems relatively appropriate in that they are getting a house and removing the old one as opposed to replacing their name but there is nothing *technically* wrong with replacing the whole element, procedurally you might argue one way or another.
```
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "operation",
            "part": [
                {
                    "name": "type",
                    "valueCode": "replace"
                },
                {
                    "name": "path",
                    "valueString": "Patient.name.where(use = 'official').family"
                },
                {
                    "name": "value",
                    "valueString": "Smithers"
                }
            ]
        },
        {
            "name": "operation",
            "part": [
                {
                    "name": "type",
                    "valueCode": "add"
                },
                {
                    "name": "path",
                    "valueString": "Patient"
                },
                {
                    "name": "name",
                    "valueString": "address"
                },
                {
                    "name": "value",
                    "valueAddress": {
                        "use": "home",
                        "line": [
                            "12399 Main Street"
                        ],
                        "city": "The Big Apple",
                        "state": "NY",
                        "postalCode": "12345",
                        "country": "US",
                        "text": "12399 Main Street"
                    }
                }
            ]
        },
        {
            "name": "operation",
            "part": [
                {
                    "name": "type",
                    "valueCode": "delete"
                },
                {
                    "name": "path",
                    "valueString": "Patient.address.where(line.first() = '357 Beier Glen Apt 6')"
                }
            ]
        },
        {
            "name": "operation",
            "part": [
                {
                    "name": "type",
                    "valueCode": "replace"
                },
                {
                    "name": "path",
                    "valueString": "Patient.maritalStatus"
                },
                {
                    "name": "value",
                    "valueCodeableConcept": {
                        "coding": [
                            {
                                "system": "http://terminology.hl7.org/CodeSystem/v3-MaritalStatus",
                                "code": "S",
                                "display": "S"
                            }
                        ],
                        "text": "S"
                    }
                }
            ]
        }
    ]
}
```
Now we have 4 parameters containing our 2 replace, 1 add, and 1 delete operations. Breaking each down:
```
{
    "name": "operation",
    "part": [
        {
            "name": "type",
            "valueCode": "replace"
        },
        {
            "name": "path",
            "valueString": "Patient.name.where(use = 'official').family"
        },
        {
            "name": "value",
            "valueString": "Smithers"
        }
    ]
}
```
Our first operation is a replacement of the surname on the [HumanName](https://www.hl7.org/fhir/datatypes.html#HumanName) entry of the [name](https://www.hl7.org/fhir/patient-definitions.html#Patient.name) array that has the use 'official'. You can see we use the where method in FHIR Path to choose the official name and then we follow it by specifying we're just replacing the family name property on that entry. That let's us just replace it with a valueString.
```
{
    "name": "operation",
    "part": [
        {
            "name": "type",
            "valueCode": "add"
        },
        {
            "name": "path",
            "valueString": "Patient"
        },
        {
            "name": "name",
            "valueString": "address"
        },
        {
            "name": "value",
            "valueAddress": {
                "use": "home",
                "line": [
                    "12399 Main Street"
                ],
                "city": "The Big Apple",
                "state": "NY",
                "postalCode": "12345",
                "country": "US",
                "text": "12399 Main Street"
            }
        }
    ]
}
```
Our next operation is adding a new address. Like in our original simple example of adding an identifier, we specify that we're adding an address on Patient. Then provide the whole new Address element which we call out as valueAddress. If you're ever confused on what your value{{elementType}} should be, a good way to know is by checking the FHIR documentation ![Picture of FHIR documentation page highlighting element type next to properties for on the Patient resource](/Guide_Book/Images/FHIR_DocumentationTypeHighlight.png), the documentation calls out the name on patient as HumanName so the JSON node becomes valueHumanName if you were to add/replace the entirety of the name element on the Patient.
```
{
    "name": "operation",
    "part": [
        {
            "name": "type",
            "valueCode": "delete"
        },
        {
            "name": "path",
            "valueString": "Patient.address.where(line.first() = '357 Beier Glen Apt 6')"
        }
    ]
}
```
Our third operation is the deletion of the previous address. Since we are deleting we only need to specify the operation type and the path at which it occurs. There are a couple of key things to remember here about FHIR Patch:
- FHIR Path is case sensitive
- The delete will not throw an error if there is no value at that path
- The delete operation will fail if it finds more than one element using that path. This means that while we could have chosen to choose which address to delete by "use='home'" if you're allowing for more than one address with "use='home'" than the query would fail there. The flip side is that by specifying use instead of the first line in the address, you don't need to know the specifics of the address you're trying to replace.
You'll note that we can use methods like .first() on line collection on the address element. FHIR Path offers a number of [functions](http://hl7.org/fhirpath/N1/#collections) to assist with collections.
```
{
    "name": "operation",
    "part": [
        {
            "name": "type",
            "valueCode": "replace"
        },
        {
            "name": "path",
            "valueString": "Patient.maritalStatus"
        },
        {
            "name": "value",
            "valueCodeableConcept": {
                "coding": [
                    {
                        "system": "http://terminology.hl7.org/CodeSystem/v3-MaritalStatus",
                        "code": "M",
                        "display": "M"
                    }
                ],
                "text": "M"
            }
        }
    ]
}
```
Our last operation is to replace their marital status. Unlike the with family name, here we are electing to replace the entire [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept) element. So our path is pretty simple but our value goes from being just a valueString to valueCodeableConcept and we have the entire CodeableConcept right there. The choice to replace the entire CodeableConcept versus just elements of it really came down to how much would we need to replace and can we do it in a single operation. Here we would have had to replace 3 nodes in the JSON that makes up the CodeableConcept as opposed to the single node on the JSON that made up the HumanName element in our first operation for this example. So it just made sense to replace the whole CodeableConcept in a sigle operation.

todo: add examples for insert and move (try to include other complex path exampes in them; include upsert example)

## Additional Resources
- [FHIR Patch on Azure](https://learn.microsoft.com/en-us/azure/healthcare-apis/azure-api-for-fhir/fhir-rest-api-capabilities#patch-with-fhirpath-patch)
- [FHIR Path documentation](http://hl7.org/fhirpath/N1/)
- [FHIR Patch documentation](https://www.hl7.org/fhir/fhirpatch.html)
- [Example FHIR Patch Requests](https://github.com/microsoft/fhir-server/blob/main/docs/rest/FhirPatchRequests.http)

# JSON Patch

JSON Patch is an alternative way to patch FHIR resources although JSON Patch is not specific to FHIR as a specification.

todo: call out capabilities of json patch; add examples for add, remove, replace, copy, move, test

## Additional reading
- [JSON Patch on Azure FHIR](https://learn.microsoft.com/en-us/azure/healthcare-apis/azure-api-for-fhir/fhir-rest-api-capabilities#patch-with-json-patch)
- [JSON Patch](https://jsonpatch.com/)
- [Example JSON Patch Requests](https://github.com/microsoft/fhir-server/blob/main/docs/rest/JsonPatchRequests.http)
