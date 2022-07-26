# Deleting Records in Dataverse

When a Delete is performed in FHIR, the Sync Agent (version 1.3 or earlier) will not Delete records in Dataverse, but will log a 404-Not Found error in application logs.  

>When using any delete operation in FHIR (Both hard and soft delete), a delete message will be sent to Dataverse.  
Consider turning off the sync agent before attempting a bulk  delete in FHIR.

Some common methods to consider for performing data cleanup tasks directly in Dataverse are listed below. The options in **bold** may be best suited for  clean up jobs that involve deleting records in FHIR, as you will already be using API calls.  

| **Type** | **Description** | **Reference** |
| --- | --- | --- |
| Manual | User searches for patient by FHIR ID using Advanced Find, deletes record(s) using Delete button. | [Advanced find - Power Apps &#124; Microsoft Docs](https://docs.microsoft.com/en-us/power-apps/user/advanced-find) |  
| Manual | User bulk deletes records via App User Interface | [Delete bulk records - Power Platform &#124; Microsoft Docs](https://docs.microsoft.com/en-us/power-platform/admin/delete-bulk-records) |  
| Low-code Automation | Configure a Cloud Flow to delete based on file of IDs | [Use a flow to delete a row from Dataverse - Power Automate &#124; Microsoft Docs](https://docs.microsoft.com/en-us/power-automate/dataverse/delete-row) |  
| **Script/code** | **Delete operation via Dataverse Web API** | [Update and delete table rows using the Web API (Microsoft Dataverse) - Power Apps &#124; Microsoft Docs](https://docs.microsoft.com/en-us/power-apps/developer/data-platform/webapi/update-delete-entities-using-web-api#basic-delete) |  
| **Script/code** | **Initiate a bulk delete job via Web API using the BulkDeleteRequest message** | [Delete data in bulk (Microsoft Dataverse) - Power Apps &#124; Microsoft Docs](https://docs.microsoft.com/en-us/power-apps/developer/data-platform/delete-data-bulk) |

Questions/considerations for inputs into a cleanup plan:

- What records will require delete?  
  - Even if it's a single table (i.e. Contact), be aware of relationships to other tables.  
  
- Be aware of cascade rules defined on the table relationship configuration, and any effects that may have on related records if they exist.  
[About table relationships for Microsoft Dataverse - Power Apps &#124; Microsoft Docs](https://docs.microsoft.com/en-us/power-apps/maker/data-platform/create-edit-entity-relationships#behaviors)  
In the example below, deleting the parent table record will result in orphaned child records due to the behavior being set to _Referential_.
  ![Referential Relationship](./Images/Dataverse_Referential_Rx.png)
- Is there any custom logic enabled on Contact that may be triggered by a Delete or may prevent a Delete?
