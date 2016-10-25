---
title: Register a patient
keywords: foundations, patient, nhsnumber, pid, register
tags: [foundations,use_case]
sidebar: foundations_sidebar
permalink: foundations_use_case_register_a_patient.html
summary: "Use case for registering a patient with an organization."
---

{% include todo.html content="Draft..." %}

## API Usage ##

Patient Registration is faciliated by the `$gpc.registerpatient` FHIR Operation ([current DMS][http://data.developer.nhs.uk/fhir/candidaterelease-250816-appts/Chapter.5.Constrained%20FHIR%20Models/index.html]) and is currently limited to a specific business scenario around the Appointments capability.

Sending a `Patient` resource with NHS # and Date of Birth will allow the receiving system to perform a Spine Patient Demographic Service (PDS) lookup to create a patient record if it does not already exist. This will allow an appointment to be booked for a patient previously unknown to a GP Practice's system of choice.

### Request Operation ###

The `[registerPatient]` field SHALL be populated with a valid Patient resource (i.e. `http://fhir.nhs.net/StructureDefinition/gpconnect-register-patient-1`).

#### FHIR Relative Request ####

```http
GET /Location?identifier=[system]|[value]
```

#### FHIR Absolute Request ####

```http
GET https://[proxy_server]/https://[provider_server]/[fhir_base]/Location?identifier=[system]|[value]
```

#### Request Headers ####

Consumers SHALL include the following additional HTTP request headers:

| Header               | Value |
|----------------------|-------|
| `SSP-TraceID`        | Consumer's TraceID (i.e. GUID/UUID) |
| `SSP-From`           | Consumer's ASID |
| `SSP-To`             | Provider's ASID |
| `SSP-InteractionID`  | `urn:nhs:names:services:gpconnect:fhir:rest:search:location`|

#### Payload Request Body ####

N/A

#### Error Handling ####

Provider systems:

- SHALL return an [OperationOutcome](https://www.hl7.org/fhir/DSTU2/operationoutcome.html) resource that provides additional detail when one or more request fields are corrupt or a specific business rule/constraint is breached.

For example the:

- Business identifier `[system]` is not recognised/supported by the Provider system.
- Business identifier fails structural validation checks (i.e. not enough digits to be a valid ODS Site Code).

{% include important.html content="Failure to find a record with the supplied business identifier is not considered an error condition." %}

### Request Response ###

#### Response Headers ####

Provider systems are not expected to add any specific headers beyond that described in the HTTP and FHIR&reg; standards.

#### Payload Response Body ####

Provider systems:

- SHALL return a `200` **OK** HTTP status code on successful execution of the operation.
- SHALL return zero or more matching `Location` resources in a `Bundle` of `type` searchset.
- SHALL return `Location` resources that conform to the `gpconnect-location-1` profile.
- SHALL include the relevant GP Connect `StructureDefinition` profile details in the `meta` fields of the returned `Location` resources.
- SHALL include the `versionId` and `fullUrl` of the current version of each `Location` resource.
- SHALL include all relevant business `identifier` details (i.e. ODS Site Code) for each `Location` resource.

```json
{
	"resourceType": "Bundle",
	"type": "searchset",
	"entry": [{
		"resource": {
            "resourceType": "Patient",
            "id": "1",
            "meta": {
                "profile": ["http://fhir.nhs.net/StructureDefinition/gpconnect-register-patient-1"]
            },
            "identifier": [{
                "system": "http://fhir.nhs.net/Id/nhs-number",
                "value": "9999999999"
            }],
            "dateofbirth": "01/01/2001"
        }
	}]
}
```

## Example Code ##

### C# ###

```csharp
Hello World
```

### Java ###

```java
Hello World
```
