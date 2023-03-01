# Patching FHIR Resources

[Azure API for FHIR](https://learn.microsoft.com/en-us/azure/healthcare-apis/azure-api-for-fhir/fhir-rest-api-capabilities#patch-and-conditional-patch) supports [REST Patch](https://www.rfc-editor.org/rfc/rfc5789) and Conditional Patching for updating elements of resources without replacing/updating the entirety of the resource e.g. changing a patient given name without replacing the whole patient resource. There are two methods of patching FHIR resources: FHIR Patch or JSON Patch. Both support conditional patching viz. perfomring an update on an element of a resource based on some criteria. However what kind of criteria you can use in your conditional is different from one method of patching versus another. Generally speaking, *unless* the client performing the patch has a copy of the server's version of the data and therefore knows initimately the size and shape of that resource, it is recommended that you use FHIR Patch over JSON Patch due to more advanced querying capabilities through FHIR Path.

# FHIR Patch

FHIR Patch allows you to update elements on a FHIR resource without updating the full resource. Below is simple example FHIR Patch request to add an identifier. Note that here we will call out content type and route but as they are the same for all FHIR Patch requests they have been omitted for brevity going forward.

## Simple FHIR Patch
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

## Complex FHIR Patch
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
### Replace with FHIR Patch
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
Our next operation is adding a new address. Like in our original simple example of adding an identifier, we specify that we're adding an address on Patient. Then provide the whole new Address element which we call out as valueAddress. If you're ever confused on what your value{{elementType}} should be, a good way to know is by checking the FHIR documentation, ![Picture of FHIR documentation page highlighting element type next to properties for on the Patient resource](/Guide_Book/Images/FHIR_DocumentationTypeHighlight.png) the documentation calls out the name on patient as HumanName so the JSON node becomes valueHumanName if you were to add/replace the entirety of the name element on the Patient.
### Delete / Upsert with FHIR Patch
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
- If you swap the place of this delete and this add, you have your upsert pattern as a delete will not throw a failure if no value is found at the specified path. As such you can delete the item you are seeking to upsert if it exists and then add the desired value at that location.
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

### Insert with FHIR Patch
```
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "operation",
            "part": [
                {
                    "name": "type",
                    "valueCode": "insert"
                },
                {
                    "name": "path",
                    "valueString": "Patient.name.where(use = 'official').given"
                },
                {
                    "name": "index",
                    "valueInteger": 0
                },
                {
                    "name": "value",
                    "valueString": "Matthew" 
                }
            ]
        }
    ]
}
```
In this example, our patient's middle name was accidentally added as their first name, therefore to correct the record we want to insert their first name at the first position in the array. Inserts look just like adds except they all contain an index with valueInteger. Inserts are difficult in that you need to know the size of the array. If you are just appending to an array, it is recommended you just use the add operation instead of insert.

### Move with FHIR Patch
```
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "operation",
            "part": [
                {
                    "name": "type",
                    "valueCode": "move"
                },
                {
                    "name": "path",
                    "valueString": "Patient.name.where(use = 'official').given"
                },
                {
                    "name": "source",
                    "valueInteger": 0
                },
                {
                    "name": "destination",
                    "valueInteger": 1
                }
            ]
        }
    ]
}
```
In our previous example we inserted the first name ahead of the middle name. Let's presume that was done in error and we needed to move the original first name back in front the new first name which is in fact the middle name. To do this, we provide a source and destination position for our move where the source is the index of the item we wish to move and destination is the new position for that item.

### Replace Value If Undesired with FHIR Patch
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
                    "valueString": "Patient.identifier.where(system='example-system'and value!='1234')"
                },
                {
                    "name": "value",
                    "valueIdentifier": {
                        "system": "example-system",
                        "value": "1234"
                    }
                }
            ]
        }
    ]
}
```
Here is an example where the we want only one value for a given system in the identifier array and we only ever want to replace that value if it is different that the value we want it to be. We decided upsert will not work for our use case but want similar behavior in that the value is replaced if it is different than what we want it to be. Note that if no value is found at the path it will return a 400.

### Handling 400s FHIR Patch
FHIR Path is a powerful querying tool but in any non-delete scenario where the query finds no value at the specified path you will recive a 400. Understanding the resulting expected OperationOutcomes from your scenario and anticipating them in your code will be critical to effective programmatic use of FHIR Path queries in your patches. Below is an example OperationOutcome where my FHIR path query returned no value:
```
{
    "resourceType": "OperationOutcome",
    "id": "4814920e4d23a087ee719a2dcd0b08e6",
    "meta": {
        "lastUpdated": "2023-03-01T05:17:21.392824+00:00"
    },
    "issue": [
        {
            "severity": "error",
            "code": "invalid",
            "diagnostics": "No content found at Patient.identifier.where(system = 'example-system').value when processing patch replace operation."
        }
    ]
}
```

## Additional Resources
- [FHIR Patch on Azure](https://learn.microsoft.com/en-us/azure/healthcare-apis/azure-api-for-fhir/fhir-rest-api-capabilities#patch-with-fhirpath-patch)
- [FHIR Path documentation](http://hl7.org/fhirpath/N1/)
- [FHIR Patch documentation](https://www.hl7.org/fhir/fhirpatch.html)
- [Example FHIR Patch Requests](https://github.com/microsoft/fhir-server/blob/main/docs/rest/FhirPatchRequests.http)


# JSON Patch

JSON Patch is an alternative way to patch FHIR resources although JSON Patch is not specific to FHIR as a specification. In most cases, there is no strong reason to use JSON Patch due to its reliance on working copy of the resource you're modifying and the strength of FHIR Path querying capabilities. FHIR Path and JSON Patch both support add, remove, replace, and move operations. In addition though, JSON Patch does have copy and test operations. These operations are the primary reasons you may want to use JSON Patch over FHIR Patch in certain scenarios. In addition, JSON Patch is not specific to FHIR so in instances where you maybe using low-code solutions or the request is coming from a client or individual not deeply familiar with FHIR as a specification but familiar with JSON Patch, then JSON Patch may be preferrable.

## Add with JSON Patch 
Note that here we will call out content type and route but as they are the same for all JSON Patch requests they have been omitted for brevity going forward.
```
PATCH {{baseUrl}}/Patient/{{patientId}}
Content-Type: application/json-patch+json
[
     {
        "op": "add",
        "path": "/identifier/-",
        "value": {
            "system": "example-system",
            "value": "1234"
        }
    }
]
```
Here we are appending a new identifier to the end of the array of identifiers on a patient. Let's say we wanted to wanted to "insert" this same value at a specific position in that array, we can modify the path part of the query like so:
```
[
     {
        "op": "add",
        "path": "/identifier/2",
        "value": {
            "system": "example-system",
            "value": "1234"
        }
    }
]
```
If you wanted to insert at the beginning to the array you would just change the 2 to a one.

Note that you can get 200 responses back for add operations that are actually rejected by the server e.g. trying to add an identifier array as a node on the identifier array:
```
[
    {
        "op": "add",
        "path": "/identifier/-",
        "value": [
            {
                "system": "example-system",
                "value": "5678"
            }
        ]
    }
]
```

## Replace with JSON Patch 
```
[
     {
        "op": "add",
        "path": "/replace/2/value",
        "value": "5678"
    }
]
```
Jumping off a previous example of adding an identifier at position 2, if we realized we wanted to replace the value of 1234 with 5678 for our example-system identifier at position 2 in the identifier array, we can specify the location in the path and drill down further to value node on the identifier in question so we just provide the string. You are not restricted to doing this however, you could still just replace the whole identifier object:
```
[
    {
        "op": "replace",
        "path": "/identifier/2",
        "value": {
            "system": "example-system",
            "value": "5678"
        }
    }
]
```
Or alernatively, if you knew you only wanted that identifier, you can scope out further and replace the entire identifier array: 
```
[
    {
        "op": "replace",
        "path": "/identifier",
        "value": [
            {
                "system": "example-system",
                "value": "5678"
            }
        ]
    }
]
```

## Remove with JSON Patch 
```
[
    {
        "op": "remove",
        "path": "/identifier/0"
    }
]
```
Remove is straightfoward as long as you know where the node you want to remove is. Again, you can scope the size of the remove operation based on the path you provide it. Be careful not remove critical swathes of data from your FHIR resource. There is nothing to stop you from removing the entire identifier node on the Patient.

## Move with JSON Patch 
```
[
    {
        "op": "move",
        "from": "/identifier/1",
        "path": "/identifier/0"
    }
]
```
Moving is also quite simple as long as you know the locations what you need to move and where it needs to go.

## Copy with JSON Patch 
```
[
    {
        "op": "copy",
        "from": "/identifier/0",
        "path": "/identifier/-"
    }
]
```
Copy is a decent reason for using JSON Patch over FHIR Path but much like most of the JSON Patch, you need to the know the ordinal position of the what you're copying and where you're copying it too. Here we copy the first identifier to the end of the array.

## Test with JSON Patch 
```
[
    {
        "op": "test",
        "path": "/identifier/0",
        "value": {
            "system": "very-real-system",
            "value": "5678"
        }
    },
    {
        "op": "test",
        "path": "/identifier/1",
        "value": {
            "system": "example-system",
            "value": "1234"
        }
    },
    {
        "op": "move",
        "from": "/identifier/0",
        "path": "/identifier/1"
    }
]
```
Because you need to know the positions of various nodes on the object you're seeking to patch, it can be useful to use the test operation to verify the shape of the data prior to beginning your patch. You can use a series of tests at the beginning of your Patch to verify that things are as expected before proceding to affect change. Here, I am testing that both the first and second position in the identifier array contian the values I expect before swapping their positions. If any given test operation fails inside a patch, the entire patch is aborted and no changes will be applied. Therefore, there may be instances where you choose to layer transformations as a serious of syncronous patches, applied on request after another using tests to verify that the resource has not changed in interim. This design choice should be made with an understanding of how many potential REST requests you may end up making to the FHIR server.

### Example Negative Response from a JSON Patch Test Operation
```
{
    "resourceType": "OperationOutcome",
    "id": "a544052b4b3d4b653fd3a2f7eea18bda",
    "meta": {
        "lastUpdated": "2023-03-01T12:52:52.6717253+00:00"
    },
    "issue": [
        {
            "severity": "error",
            "code": "processing",
            "diagnostics": "The current value '{\r\n  \"system\": \"example-system\",\r\n  \"value\": \"1234\"\r\n}' at position '0' is not equal to the test value '{\r\n  \"system\": \"very-real-system\",\r\n  \"value\": \"5678\"\r\n}'."
        }
    ]
```
Similar to error handlings with FHIR Patch Path, you will need to handle OperationOutcomes from 400 responses where the test fails. The failures do provide you the current value of that which you are testing so you can provide a retry pattern. Unfortunately, patches with multiple tests return at the first failed test meaning you a retry pattern may need to retry multiple times due to hidden failed tests.

## Additional reading
- [JSON Patch on Azure FHIR](https://learn.microsoft.com/en-us/azure/healthcare-apis/azure-api-for-fhir/fhir-rest-api-capabilities#patch-with-json-patch)
- [JSON Patch](https://jsonpatch.com/)
- [Example JSON Patch Requests](https://github.com/microsoft/fhir-server/blob/main/docs/rest/JsonPatchRequests.http)
