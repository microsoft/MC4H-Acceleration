# Patient Merges and FHIR
When patient records are merged or unmerged in an EMR/EHR, there are multiple ways to represent this in the FHIR spec. The more common approach is to use patient linking.

##  Patient Link in the FHIR Spec
The FHIR specification for Patient contains an optional property named “link”. “Link” is defined as a “link to another patient resource that concerns the same actual person”.

Resources:
* https://www.hl7.org/fhir/patient.html
* https://www.hl7.org/fhir/patient-definitions.html#Patient.link
* https://www.hl7.org/fhir/valueset-link-type.html#expansion

The “link” element is not required and Patient resources may have more than one “link” element defined.
The “link” element has two attributes:
1.	**other**: the value of this attribute is a reference to another Patient (or RelatedPerson) resource. A “link” element must have one and only one “other” attribute. Referencing a RelatedPerson here removes the need to use a Person record to associate a Patient and RelatedPerson.
2.	**type**: this is a code field and the options for this field include: “replaced-by”, “replaces”, “refer”, and “seealso”. A “link” element must have one and only one “type” attribute. 
    * **replaced-by**: 
        * The Patient resource containing this link must no longer be used. 
        * The link points forward to another Patient resource that must be used in lieu of the Patient resource that contains this link.
    * **replaces**: 
        * The Patient resource containing this link is the current active patient record. 
        * The link points back to an inactive Patient resource that has been merged into this resource, and should be consulted to retrieve additional referenced information.
    * **refer**: 
        * The Patient resource containing this link is in use and valid but not considered the main source of information about a patient. 
        * The link points forward to another Patient resource that should be consulted to retrieve additional referenced information. 
        * This option is often used in systems called Patient indexes where a summary of a patient and other sub-systems contain more comprehensive and/or authoritative information about that same patient.
    * **seealso**: 
        * The Patient resource containing this link is in use and valid, but points to another Patient resource that is known to contain data about the same person. Data in this resource might overlap or contradict information found in the other Patient resource. 
        * This link does not indicate any relative importance of the resources concerned, and both should be regarded as equally valid. 
        * This option is often used in systems where Patient records are intentionally distributed and each sub-system contains different aspects of a single Patient.
 
## Merging Patients in FHIR with Patient Link

Occasionally in clinical data systems, two Patient resources need to be merged together because they are found to represent the same individual. Here we lay out potential logic to be implemented in your data transformation and orchestration layer to represent this merge in FHIR.

**For the purposes of this example, we will assume an HL7v2 A18 ADT Event is generated when patients are merged in the source system, including two MRNs representing each Patient being merged.**
* The message includes the Patient to merge and the Patient to merge to.

> IMPORTANT NOTE: <BR>
The specific event to be captured and the data contained will depend on the source system. 

1.	Capture the merge event
2.	Update one or both Patient resources using FHIR API (ensure transaction of both resources in a single API call)
    * You must add a “link” element to the Patient resource you no longer wish to get updates (PatientToMerge). Set this Patient’s “active” property to false. The “link” element’s “other” option should be set to the Id of the primary Patient and the “type” option should be set to “replaced-by”. The “active” attribute should be set to false.
    * Optionally, you might want to add a “link” element to the Patient resource you do want to keep updating (PatientMergeTo). The “link” element’s “other” option should be set to the Id of the Patient to be replaced and the “type” option should be set to “replaces”. (The “active” attribute should remain set to true. You shouldn’t merge any Patient into another inactive Patient.)

__**EXAMPLE OF FHIR CALL: Get each Patient**__

if you have the Patient’s unique ID… 

`GET https://[fhirUrl]/Patient/[PatientId]`

If you only have the Patient’s MRN…

`GET https://[fhirUrl]/Patient?identifier=[MRN]`

__**EXAMPLE OF FHIR CALL: Update one Patient with “replaced-by” link**__

`PATCH https://[fhirUrl]/Patient/[PatientId]`

__**EXAMPLE OF FHIR CALL: Get combined Patients**__

Both Patient resources remain active in FHIR. To get the “complete” Patient, queries made to FHIR should check for “link” elements and return those linked Patients as well.

`GET https://[fhirUrl]/Patient/[PatientId]?$everything`

To be clear, the result of merging patient resources through links makes both Patient records available if you query for them by ID, but if you use the $everything parameter in your FHIR query for either Patient resource, you will only get the Patient that replaces – aka the “merged to” Patient. To be even clearer…
* If you query for the “merged to” Patient by ID, you will get that Patient and only the data associated with that Patient – including it’s “link” to the “merge” Patient. 
* If you query for the “merge” Patient by ID, you will get that Patient – including it’s “link” to the “merged to” Patient.
* If you query for the “merge” Patient using the $everything parameter,  you will get the “merge to” Patient and a warning that this “merge” Patient  has been replaced by the “merge to” Patient.

### Merged Patient Example(s) in FHIR with Patient Link:

Data before merge request:
| id | identifiers | name | active | link |
| -- | -- | -- | -- | -- |
| 1234-5678-9012-3456 |	“mrn”: “123” | “text”: “Clark Kent”	| true	| |
| 0987-6543-2109-8765 |	“mrn”: “098” | “text”: “Kal El” | true | |

Data after request to merge Clark Kent into Kal El:

| id | identifiers | name | active | link |
| -- | -- | -- | -- | -- |
| 1234-5678-9012-3456 | “mrn”: “123” | “text”: “Clark Kent” | false | “other”: “0987-6543-2109-87650987-6543-2109-8765” <br> “type”: “replaced-by” |
| 0987-6543-2109-8765 | “mrn”: “098” | “text”: “Kal El” | true | “other”: “1234-5678-9012-3456” <br> “type”: “replaces” |

## Unmerging Patients Merged with Patient Link

Occasionally, two Patient resources are merged incorrectly and they need to be unmerged.  Here we lay out potential logic to be implemented in your data transformation and orchestration layer to handle an unmerge event.

**For the purposes of this example, we will assume an HL7v2 A28 "new patient" ADT Event is generated when an unmerge in the source system results in new patient with the same MRN of the patient being unmerged.** 
* The message represents a copy of the unmerged patient record with the original MRN, and has a new unique identier in the source system. 
* In this example, there is nothing in a Z segment distingquishing this message as an unmerge vs. and typical new patient event. 

> IMPORTANT NOTE: <BR>
As with a merge event, the specific event representing an unmerge and the data contained will depend on the source system.

The recommended option for unmerging Patients that used “link” elements as described above (see Merging Patients) is to simply remove the appropriate “link” element(s).

1. Event A28 is captured
2. Query FHIR to locate any patients with existing MRN that matches the MRN provided by the A28 message
    * If provided MRN is found, this indicates an unmerge is needed… <br>
    **All of the following should be completed in a single transaction:**
        1. Create NewPatient resource with new Epic-generated FHIR ID as “id”
            * Add provided MRN as an “identifier” and other information present in A28 message
            * Add “link” element to NewPatient with “other” property set to Id of PatientToUnmerge and “type” property set to “replaces”
        2. Query FHIR for Patients referenced by “link” element. For each Patient…
            * Remove “link” elements where “other” property is equal to PatientToUnmerge
        3.	Update PatientToUnmerge
            * Set “active” to false
            * Remove “link” elements
            * Add “link” element to PatientToUnmerge with “other” property set to Id of NewPatient and “type” property set to “replaced-by”
    * Else if provided MRN is not found, this indicates a new patient is needed… <br> **All of the following should be completed in one HTTP request:**
        1.	Create Patient resource with Epic-generated FHIR ID as “id”
        2.	Add provided MRN as an identifier
    * Else if provided MRN is found on more than one Patient… <br> 
    **We don’t know which to unmerge so log an error for manual intervention later**

__**EXAMPLE OF FHIR CALL: Get patients with provided MRN in identifiers array**__

`GET https://[fhirUrl]/Patient?identifier=[MRN]`

__**EXAMPLE OF FHIR CALL: Update each Patient to remove the appropriate link attributes**__

`PATCH https://[fhirUrl]/Patient/[PatientId]`

### Unmerged Example(s) in FHIR with Patient Link

__Example #1: standard unmerge__ 

Data before unmerge request:

| id | identifiers | name | active | link |
| -- | -- | -- | -- | -- |
| 1234-5678-9012-3456 | “mrn”: “123” | “text”: “Clark Kent” | false |  “other”: “0987-6543-2109-87650987-6543-2109-8765” <br> “type”: “replaced-by” |
| 0987-6543-2109-8765 | “mrn”: “098” | “text”: “Kal El”	| true | “other”: “1234-5678-9012-3456” <br> “type”: “replaces” |

Data after unmerge of Clark Kent from Kal El:

| id | identifiers | name | active | link |
| -- | -- | -- | -- | -- |
| 1234-5678-9012-3456 | “mrn”: “123” | “text”: “Clark Kent” | false | “other”: “5678-9012-3456-7890” <br> “link”: “replaced-by” |
| 0987-6543-2109-8765 | “mrn”: “098” | “text”: “Kal El” | true | |	
| 5678-9012-3456-7890 | “mrn”: “123” | “text”: “Clark Kent” | true | “other”: “1234-5678-9012-3456” <br> “link”: “replaces” |

> NOTE: A new Patient was created to replace the patient to unmerge. Unless other updates or new data are received and processed to reference this new patient, this new Patient doesn’t have any other information from the original Patient resource related to it. That data can be pulled based on the “link” element in a subsequent request.

__Example #2: unmerge, but multiple MRNs discovered__

Data before unmerge request:

| id | identifiers | name | active | link |
| -- | -- | -- | -- | -- |
| 1234-5678-9012-3456 | “mrn”: “123” | “text”: “Clark Kent” | false | “other”: “0987-6543-2109-87650987-6543-2109-8765” <br> “type”: “replaced-by” |
| 1234-5678-9012-3456 | “mrn”: “123” | “text”: “Anyone Anywhere” | true | |
| 0987-6543-2109-8765 | “mrn”: “098” | “text”: “Kal El” | true | “other”: “1234-5678-9012-3456” <br> “type”: “replaces” |

> NOTE: If multiple Patients with the provided MRNs are uncovered, we can’t assume which Patient to unmerge and we need to log an error to be dealt with at a later date.

