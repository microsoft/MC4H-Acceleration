# Ingest FHIR Data into Customer Insights
>**Objective:**<br>
>Configure Azure FHIR Power Query connector to ingest Patient and Appointment data into Customer Insights

## Pre-requisites
* Customer Insights tenant subscription
* Customer Insights environment created Start with Customer Insights
* Admin or Contributor permissions to the Customer Insights environment
* URL of the deployed Azure API for FHIR, accessible from the Customer Insights tenant
    * Should be of the format `https://[AzureAPIforFHIRName].azurehealthcareapis.com`
* User with FHIR Data Exporter access role assigned in Azure API for FHIR for Organizational authentication:  [FHIR Power Query authentication - Power Query | Microsoft Docs](https://docs.microsoft.com/en-us/power-query/connectors/fhir/fhir-authentication#azure-active-directory-organizational-authentication)

## Outcomes
* Successful authentication and connection to Azure API for FHIR via the FHIR Connector
* Patient and Appointment resources ingested into Customer Insights, ready for processing

## Step 1: Connect with the FHIR Power Query Connector

> NOTE: Use of the FHIR connector was selected as one ingestion method for FHIR data into Customer Insights. This may or may not be the appropriate ingestion pattern depending on data volume and complexity of the customer scenario.

1.	Go to the Customer Insights application and select the correct environment in the upper right corner if necessary.<br>
`https://home.ci.ai.dynamics.com/`

![Customer Insights: Select Environment](./Images/CIEnvironmentSelection.png)

2.	Within the Audience Insights capability (default), expand into **Data sources**, click **Add data source** and follow through the Wizard to retrieve Appointment and Patient from the Azure API for FHIR:

    * Leaving **Microsoft Power Query** selected, name the data source **AzureAPIforFHIR** and click **Next**.
    * Find and select the **FHIR** connector in the list of available power query connectors (hint: use the search bar) and click **Next**.
    * Enter the **URL** to the Azure API for FHIR of the below format, and leave the **Query** blank<br>
    `https://[AzureAPIforFHIRName].azurehealthcareapis.com`
    * Authenticate with **Organizational account**, using the account that is authorized with the FHIR Data Exporter access role on the Azure API for FHIR (see pre-requisites for more information). Click **Next** once authenticated.
    * Once the list of available tables in the FHIR Server load, check the **Appointment** and **Patient** tables, and click **Transform data**.

![Configure New Data Source](./Images/NewDataSource.png)

<img
    style="display: block;
            margin-left: auto;
            margin-right: auto;
            width: 60%;"
    src="./Images/PatientsConnectorSettings.png"
    alt="FHIR Connector Connection Settings">
</img>

## Step 2: Transform the Patient Data with Power Query

1.	Select the **Patient** table in the **Queries** pane.
2.	Use **Choose Columns** to remove all columns except **id, meta, name, telecom, birthDate**.

![Patient Table: Choose Columns](./Images/PatientsTransformChooseColumns.png)

3.	Note that the **meta**, **name** and **telecom** columns contain nested records and tables due to the FHIR data structure. 
4.	We can use expand for the **meta** column, as it contains a single record in each row:
    * Click the **Expand** button on the **meta** column header.
    * Select only the **lastUpdated** column of the nested record.
    * Click **OK** to replace the meta column containing a record with a **meta.lastUpdated** column containing a timestamp for the FHIR resource.

<img
    style="display: block;
            margin-left: auto;
            margin-right: auto;
            width: 60%;"
    src="./Images/PatientsTransformExpandMeta.png"
    alt="Patient Table: Expand Meta.LastUpdated">
</img>

5.	Since **name** contains a nested table and we only want to return data from one record, we will use custom columns to filter and return only the first (given) and last (family) names from the first record found tagged as **official**:
    * From the Add column tab of the ribbon, click **Custom Column**.
    * Define the **lastname** column and formula as in the table below. Refer to the sample FHIR JSON object to better understand how the formula is returning the value in yellow.
    * Repeat the steps above to add the **firstname** column with the formula in the table below.
    <br>

    | **Column name** | **Custom column formula** | **Sample FHIR JSON name object** |
    | --- | --- | ---| 
    | lastname | `List.First ( Table.FirstN ( [name] , each [use] = "official" ) [family] )` | ![FHIR JSON example: Patient last name](./Images/FHIRJSONLastName.png) |
    | firstname | `List.First ( List.First (Table.FirstN ( [name] , each [use] = "official" ) [given] ) )` | ![FHIR JSON example: Patient first name](./Images/FHIRJSONFirstName.png)|
    <br>
6.	The **telecom** data element can contain multiple entries for contact information including phone and email. Since we only want the email address of the patient, we will use a custom column to filter and return only the first email address:
    * From the Add column tab of the ribbon, click **Custom Column**.
    * Define the **email** column and formula as in the table below. Refer to the sample FHIR JSON object to better understand how the formula is returning the value in yellow.
   <br>

    | **Column name** | **Custom column formula** | **Sample FHIR JSON name object** |
    | --- | --- | ---| 
    | email | `List.First (Table.FirstN ( [telecom] , each [system] = "email" ) [value] )` | ![FHIR JSON example: Patient email](./Images/FHIRJSONemail.png) |
    <br>
7.	Finally, select the **name** and **telecom** table columns and click **Remove Columns**
8.	The result is a clean table of patient data with only: **id, brithDate, firstname, lastname and email**.
<br>
> NOTE: Do not Save the changes until after transforming the Appointment data, as it will close the Power Query editor, and initiate the fetch and transform. You will not be able to edit the data source again without canceling the process first. 

## Step 3: Transform the Appointment Data with Power Query

1.	Select the **Appointment** table in the **Queries** pane.
2.	Use **Choose Columns** to remove all columns except **id, status, serviceType, appointmentType, start, participant**.
3.	Note that the **serviceType**, **appointmentType**, and **participant** columns contain nested records and tables due to the FHIR data structure. 
4.	Since we just want to return values from **serviceType** with no filtering required, we can use the expand feature. However, note that the nested JSON structure of this node will require two expansion steps to return the values:
    * Click the **Expand** button on the **serviceType** column header.
    * Select only the **coding** column and click **OK** to replace the serviceType column with a new **serviceType.coding** column containing a table.
    * Click the **Expand** button on the **serviceType.coding** table.
    * Select the **code** and **display** columns to replace the serviceType.coding column with two new **serviceType.coding.code** and **serviceType.coding.display** columns.
5.	While the **appointmentType** contains a record rather than a table, the formation is similar. Repeat **Step 4** above to expand out to **appointmentType.coding.code** and **appointmentType.coding.display** columns.
6.	The participant column contains a table. Within the nested table, there is an **actor** column, which contains nested records of the people involved in the appointment. We will define custom columns to filter and return the patient’s name and ID from those nested records.
    * Add a **Custom Column** named **patientName**, with formula below to traverse into the **actor** records in the **participant** table and return the value of the **display** column. We will look for an entry where the **reference resource** is **Patient** and use the first we find:  
<br>

    | **Sample FHIR JSON name object** | **Custom column formula** | 
    | --- | --- |
    | ![FHIR JSON example: Participant Patient Name](./Images/FHIRJSONAptPatientName.png) | `List.First ( [participant] [actor] , each Text.StartsWith ( [reference] , "patient" ) ) [display]` |

    * For the key id back to the patient, we can remove the FHIR resource reference out of the **reference** value of the **actor** record, leaving only the ID. Create a **Custom Column** named **patientId** with the below formula to do that. 
<br>

    | **Sample FHIR JSON name object** | **Custom column formula** | 
    | --- | --- |
    | ![FHIR JSON example: Participant Patient ID](./Images/FHIRJSONAptPatientID.png) | `Text.Replace ( List.First ( [participant] [actor] , each Text.StartsWith ( [reference] , "patient" ) ) [reference] , "Patient/" , "" )` |

    * Finally, select the participant table column and click Remove Columns.
7.	The result is a clean Appointment table with **id, status, serviceType.coding.code, serviceType.coding.display, appointmentType.coding.code, appointmentType.coding.display, start, patientName** and **patientId**.
8.	Click **Save** to complete the data source definition and initiate the first retrieve for both Patients and Appointments. The data source will be listed with status **Refreshing**.

## Step 4: View Ingested Tables

1.	Once the initial data source refresh job completes, it will show with a **Successful** Status in the **Data Sources** area. Navigate into the **Entities** area to find two new tables: **Appointment** and **Patient**. 
2.	Clicking into each table allows you to review the Attributes being ingested, as well as the actual data. Validate that the data is coming in as expected. 

> You completed the lab!<br>
Continue to the next lab: [Ingest Dataverse Data](https://github.com/microsoft/MC4H-Acceleration/tree/main/PatientOutreach_UserStoryTraining/4_Customer_Insights_Config/Lab_CI2)