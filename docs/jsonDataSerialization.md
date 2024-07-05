# Data serialization as JSON-LD 

There are three types of artefact relevant to the data serialisation as JSON-LD: 
- context files which map JSON representation of the data to RDF/Linked Data; 
- JSON Schema that may be used to validate the JSON; and, 
- SHACL expressions [TBD] that may be used to validate the RDF represented by the JSON-LD.

## Notes

### This is a draft

Much of this can be changed as needs become apparent through application to real data in particular:

- **fields may be added** where extra detail is required
- **mandatory fields may be made optional** if data is not alway available
- **default URIs may be changed** (currently they are all examples).

### Identifiers (`id` / `@id`)

In linked data entities being described are identified with HTTP URIs, which in JSON-LD are provided as the value of the @id reserved keyword, and which may be used to link between objects in a dataset. Our context file aliases `@id` to `id`, which is more convenient for programming in some languages. We also provide default URI stems so that strings can be used as a value for the `id` property and these will be translated to http uri values for `@id`. For example, `"id": "123456"` for a Provider will be translated to `"@id": "https://example.edu/Provider/123456"`. 

### Languages

Currently the JSON-Schema validation requires that human readable entries such as names and descriptions are entered without any information about the human language being used. The context will automatically cause these to be tagged as English. A possible improvement is to allow language - string pairs to be entered so that as well as this it will also be possible to enter values such as `{"es": "Algo en Español."}` when other languages than English are used.

### Sample Data

An example of valid JSON is [available here](Samples/example1.jsonld).


## Context Files

[`context.json`](context.json) provides a mapping from the key words used in the JSON serialisation to URIs for CTDL and other RDF terms.  It also provides as a convenience to programmers some defaults that allow just the name part of a URI to be provided, these are documented below as relevant.

[`context-quals.json`](context-quals.json) provides key to URI mappings for UK Qualification types, again as a convenience for programmers. (Note: currently this only has two entries used for testing.)

## The Top Level Data Object

RDF in JSON-LD is represented as a "graph" of linked objects of different types. The context aliases "data" to the reserved JSON-LD key @graph, and defines it as a container where the JSON property names are mapped to the rdf type of the entity being described. Thus all objects in the array of values for Providers will be of the appropriate CTDL-defined RDF type, ceterms:LearningOpportunityProfile, and so on.

To validate this level of data a JSON-Schema file is provided  which requires:

- an @context array with exactly two URIs that correspond to the `context.json` and `context-quals.json` context files; and
- a data object with allowed properties AwardingBodies, Courses, Locations, Presentations, Providers (which are all optional), each of which has an array of values that are validated by an imported schema for the relevant object type.

## Sample

A valid data snippet looks like:

```
{
	"@context": [
        "https://data-for-change-makers.github.io/courses-and-occupations-open-data/context.json",
        "https://data-for-change-makers.github.io/courses-and-occupations-open-data/context-quals.json"
	],
    "data": {
    		"Courses": [{...}...],
    		"Presentations": [{...}...],
    		"Providers": [{...}...],
    		"Qualifications": [{...}...],
    		"Locations": [{...}...]
    }
}
```

## Course Data

The value for the Courses property must be an array of one or more JSON objects describing a course. A "Course" is a structured or unstructured learning and development opportunity based on direct experience, formal and informal study, observation, and involvement in discourse and practice. As such the concept of course includes a wide range of learning opportunities including academic programmes, apprenticeships, work-based training etc. A course may be offered in various locations and times: the data here relates to what is common between all those offering; see Presentation Data for details on how to describe what is particular to each offering of a course.

The `@context` will cause each object to have the RDF type ceterms:LearningOpportunity assigned as the value for the `@type` JSON-LD keyword.

The objects in the Courses array are validated using the course-schema JSONSchema files, which **requires** the following properties and values:

- **id** – which must be a string and may be an HTTP URI and must uniquely identify the Course. The recommended way to achieve this is to use the ESFA Course ID (or similar). This value should match a value for the offers property of an object describing a Provider. See the note on identifiers.
- **name** – a string, the name of the course. See the note on languages.
- **description** – a string, a general summary of the course. See the note on languages. 
- **assessmentMethodDescription** – a string, describing the way the learner will be assessed. See note on languages.
- **audience** – an object that must have two properties, type, which must have the string value "EducationalAudience" and description, a string value providing Information that will help the learner decide whether this course is suitable for them, the learning experience and opportunities they can expect from the course.
- **entryCondition** – an object that must have two properties, type, which must have the string value "ConditionProfile" and description, a string value describing specific skills, licences, vocational or academic requirements.
- **equipmentRequired** – a string, describing what the learner will need to access or bring to the course.
codedNotation – a string, the local code or Identifier of the course used by the provider.
- **identifier** – an object that provides the Course ID (as used for example in the ESFA: course directory) or similar. The properties are: `type` which must be "IdentifierValue", `identifierTypeName` which will normally be "ESFA_COURSE_ID" but may be "OTHER_GUID" (other recommended values may be added later), and `identifierValueCode` which should be the value of the ESFA Course ID or a valid GUID as a string.
- **isPreparationFor** – an array of strings that each match the id of one object in the Qualifications data.
- **hasOffering** –  an array of strings that each match the id of one object in the Presentations data.
- **subject** – an array of objects each having the following properties and values that describe the topic of the course using terms from the Sector Subject Area classification: `type` which must be the string "Concept", `inScheme` which must have the value "SSA", `notation` which should be code used in the SSA and  `prefLabel` which must be the text name of the subject in the SSA.
- **subjectWebpage** – a URI, the web page for this course. 
- **teaches** – an array of objects each of which has one property, `competencyText` with a string value that describes the main topics that the learner can expect to be taught. See note on languages.
- **whereNext** – a string, describing further opportunities a learner can expect after successfully completing the course.  See the note on languages. 

### Sample

A valid data snippet looks like:

```
{
    "id": "22222222-aaaa-1111-2222-2b2b2b2b2b2b",
    "name": "Example Learning Opportunity",
    "description": "An example learning opportunity.",
    "assessmentMethodDescription": "Example assessment methods.",
    "audience": {
        "type": "EducationalAudience",
        "description": "Example audience information."
    },
    "entryCondition": {
        "type": "ConditionProfile",
        "description": "An example learning opportunity"
    },
    "equipmentRequired": "Example equipment required.",
    "codedNotation": "Dent001",
    "identifier": {
        "type": "IdentifierValue",
        "identifierTypeName": "ESFA_COURSE_ID",
        "identifierValueCode":        "22222222-aaaa-1111-2222-2b2b2b2b2b2b"
    },
    "isPreparationFor": ["Q001"],
    "hasOffering": [
        "11111111-aaaa-1111-1111-1a1a1a1a1a1a",
        "22222222-bbbb-2222-2222-2b2b2b2b2b2b"
    ],
    "subject": [
        {
            "type": "Concept",
            "inScheme": "SSA",
            "notation": "1.1",
            "prefLabel": "Medicine and dentistry"
        }
    ],
    "teaches": [
        {
            "competencyText": "An example learning objective"
        },
        {
            "competencyText": "Another example objective"
        }
    ],
    "subjectWebpage": "https://example.edu/courses/Dent01",
    "whereNext": "Example further opportunities."
}
```

## Presentation Data

The value for the Presentations property must be an array of one or more JSON objects describing an offering or presentation of a Course, that is the details of a Course that may vary when it is offered in different locations or times. 

The `@context` will cause each object to have the RDF type `ceterms:ScheduledOffering` assigned as the value for the `@type` JSON-LD keyword.

The objects in the Presentations array are validated using the presentation-schema JSONSchema files, which requires the following properties and values:

- **id** – which must be a string and may be an HTTP URI and must uniquely identify the Presentation. The recommended way to achieve this is to use the ESFA Course Run ID (or similar). This value should match a value for the hasOffering property of an object describing a Course. See the note on identifiers.
- **scheduleTimingType** – an object with one property: `targetNode`, which must be a string indicating the availability required in order to take part in the course. Must be one of Daytime,Evening, Weekend, Weekdays, Day-BlockRelease. 
- **scheduleFrequencyType** – an object with one property: `targetNode`, which must be a string indicating how often a learner must be available to take part.  Must be one of: "MultiplePerWeek", "Weekly", "SelfPaced", "Irregular".
- **estimatedCost** – an object that describes the cost of the course to the learner with the following required properties:  type, which must be "CostProfile", `currency`, which should be "GBP" (for GB Pound), `price`, an integer value for the cost in £, and `description`, a string description of what the cost includes and additional costs to the learner. 
- **deliveryType** – an object that indicates the means by which the course is presented, it has two properties: `targetNode`, which must have one or more of the values: Classroom, Online, Work based, and `targetNodeDescription` which may optionally be used to provide a textual description of the means of presentation.
- **identifier** – an object that provides the Course Run ID (as used for example in the ESFA: course directory_) or similar. The properties are: `type` which must be "IdentifierValue", `identifierTypeName` which will normally be "ESFA_COURSE_RUN_ID" but may be OTHER_GUID (other recommended values may be added later), and `identifierValueCode` which should be the value of the ESFA Course run id or other GUID (globally unique ID) as a string.
- **start** – The date at which a presentation begins, provided as a string in YYYY-MM-DD format.
- **end**  –  The date at which a presentation begins, provided as a string in YYYY-MM-DD format.
- **estimatedDuration** – an object specifying the number of days, weeks, months or years the course runs for, it has two properties, `type` which must be  "DurationProfile" and `duration` which must be the duration of the course in ISO 8601 date format.
- **learningMethodDescription** -- a string describing the methods used to deliver the course.  See the note on languages. 
- **maximumAttendeeCapacity** – an integer indicating the maximum number of participants.
- **placesTaken** – an integer indicating the number of enrolments
- **regionServed**  – all the places where the course can be delivered, an array of objects, each with one property, `addressRegion` that provides a textual description of one region.
- **availableAt** – The location where a course is presented,  either a string that matches the id of a Location object for the address, or an object providing the address and which conforms to the Location schema with the additional requirement of having `type`: "Place".

The following properties are Conditional, that is, they must be used where appropriate (see their definitions for when they are appropriate). In other circumstances their use is optional.

- **offerFrequencyType** – an object with one property "targetNode" that indicates how frequently the course is run. The allowed values are  Annually, SemiAnnually, Monthly, Quarterly, OnDemand, Irregular. The value "OnDemand" must be used to indicate that there is a flexible start date.
- **nationalDeliveryIndicator** – Use with the boolean value `true` if the course can be delivered anywhere in England. The only other allowed value is `false`.

### Sample

A valid data snippet looks like:

```
{
    "id": "11111111-aaaa-1111-1111-1a1a1a1a1a1a",
    "scheduleTimingType": {
        "targetNode": "Weekdays"
    },
    "scheduleFrequencyType": {
        "targetNode": "MultiplePerWeek"
    },
    "estimatedCost": {
        "type": "CostProfile",
        "price": 500,
        "currency": "GBP",
        "description": "An example description of what is included ...."
    },
    "deliveryType": {
        "targetNode": "ClassRoom",
        "targetNodeDescription": "An example delivery type."
    },
    "identifier": {
        "type": "IdentifierValue",
        "identifierTypeName": "ESFA_COURSE_RUN_ID",
        "identifierValueCode":         "11111111-aaaa-1111-1111-1a1a1a1a1a1a"
    },
    "start": "2022-01-01",
    "end": "2022-12-30",
    "estimatedDuration": {
        "type": "DurationProfile",
        "duration": "P1Y"
    },
    "offerFrequencyType": {
        "targetNode": "Annually"
    },
    "learningMethodDescription": "Example learning methods",
    "nationalDeliveryIndicator": false,
    "maximumAttendeeCapacity": 25,
    "placesTaken": 25,
    "regionServed": [
        {
            "addressRegion": "London"
        }
    ],
    "availableAt": "101"
}
```

## Provider Data

The value for the Provider property must be an array of one or more JSON objects describing a provider. The `@context` will cause each object to have the RDF type of `ceterms:CredentialOrganization` assigned as the value for the @type JSON-LD keyword.

The objects in the Provider data array are validated using the provider-schema JSONSchema file, which requires the following properties and values:

- **id** – which must be a string and may be an HTTP URI and must uniquely identify the Provider. The recommended way to achieve this is to use the UKPRN of the provider. See the note on identifiers.
- **name** – a string, the name of the provider. See the note on languages.
- **address** – either a string that matches the id of a Location object for the provider's address, or an object providing those details that conforms to the Location schema with the additional requirement of having `type`: "Place".
- **offers** – an array of strings that each match the id of one object in the Presentation data.
**identifier** – an object that includes the provider's reference number (UKPRN). The properties of the object must be: `type`: "IdentifierValue", `identifierTypeName`: "UKPRN", `identifierValueCode`: "nnnnnnnn" where "nnnnnnnn" is the provider's UKPRN as a string.

The following properties are optional:
- **type** – which must be a URI for a relevant RDF class for the provider.
- **subjectWebpage** – which must be the URI of the organization's home page on the web. 

### Sample

A valid data snippet looks like:

```
{
    "id": "123435678",
    "name": "Example College",
    "identifier": {
        "type": "IdentifierValue",
        "identifierTypeName": "UKPRN",
        "identifierValueCode": "12345678"
    },
    "address": {
        "type": "Place",
        "streetAddress": "1 Example Street",
        "addressRegion": "West London",
        "addressLocality": "London",
        "postalCode": "W1A 1EG"
    },
    "subjectWebpage": "https://example.edu/",
    "offers": ["001"]
}
```

## Qualification Data

The value for the Qualifications property must be an array of one or more JSON objects describing a qualification. The `@context` will cause each object to have the RDF type of `ceterms:Credential` assigned as the value for the @type JSON-LD keyword. Additional more specific qualification types can also be provided

The objects in the Qualification data array are validated using the qualification-schema JSONSchema file, which requires the following properties and values:

- **id** – which must be a string and may be an HTTP URI and must uniquely identify the Qualification. The recommended way to achieve this is to use the LARS reference of the qualification. See the note on identifiers.
- **type** – which must be a string that identifies the specific type of Qualification. Currently only one type is allowed, "BTech", but more will be added very soon. 
- **name** – a string, the name of the qualification. See the note on languages.
- **description** – a string, a general summary of the course. See the note on languages. 
- **awardedBy** – a string that matches the id of an AwardingBody.
- **identifier** – an object that includes the learning aims code for the qualification. The properties of the object must be: `type`: "IdentifierValue", `identifierTypeName`: "LARS", `identifierValueCode`: "nnnnnnnn" where "nnnnnnnn" is the 8-digit LARS QAN as a string.
- **educationalLevel** – a string indicating the qualification level in the English Regulated Qualifications Framework, must be one of "Level1", "Level2" … "Level8".
- **industryType** – An array of objects describing industries for which the which the qualification is relevant, each object must have values of `type` as "CredentialAlignmentObject", `alignmentType` as "Industry",  and `framework` as "UKSIC2007"; the value of the `codedNotation` property must be the SIC code, and the value of the `targetNodeName` property may be the label used by SIC for the industry.
- **occupationType** –  An array of objects describing occupations for which the which the qualification is relevant, each object must have values of `type` as "CredentialAlignmentObject", `alignmentType` as "Occupation",  `framework` as "UKSOC2020", and `frameworkName`: "UK SOC 2020"; the value of the `codedNotation` property must be the SOC code of the occupation, and the value of the `targetNodeName` property may be the label used by SOC for the occupation.

## Sample

A valid data snippet looks like:

```
{
    "id": "Q001",
    "type": [
        "BTech"
    ],
    "name": "Example Qualification",
    "description": "An example qualification.",
    "awardedBy": "AB001",
    "identifier": {
        "type": "IdentifierValue",
        "identifierTypeName": "LARSQAN",
        "identifierValueCode": "12345678"
    },
    "educationalLevel": "Level5",
    "industryType": [
        {
            "type": "CredentialAlignmentObject",
            "alignmentType": "industry",
            "codedNotation": "86230",
            "targetNodeName": "Dental practice activities",
            "framework": "UKSIC2007"
        }
    ],
    "occupationType": [
        {
            type": "CredentialAlignmentObject",
            "alignmentType": "occupation",
            "codedNotation": "6133",
            "targetNodeName": "Dental nurses",
            "framework": "UKSOC2020"
        }
    ]
}
```

## Location Data
Information about locations, including addresses, can be provided either directly as object values for properties or linked by reference to an object in the Locations array. If they are in the Locations array they will automatically be assigned the RDF type of ceterms:Place, if they are provided as object values for properties this type must be added explicitly using the property: value pair "type": "Place".

Location data is validated with the location-schema file, which requires the

- **addressLocality** – a string, the locality in which the street address is located, and which is in the address region
- **addressRegion** – a string, the region of the county in which the locality is located.

Further properties are required under certain conditions:

- **id** –  which must be a string and may be an HTTP URI and must uniquely identify the Place. This is required if the data is provided in the Locations array so that it may be linked to from other objects, otherwise it is optional. See the note on identifiers.
- **type** – which must be a URI for a relevant RDF class for the location. The value "ceterms:Place" must be used if the data is provided as an object not in the Location array; this value is added automatically to objects in the Locations array.

The following properties are optional:

- **name** – a string, the name of the location See the note on languages.
- **identifier** – an object providing the identifier for the location and the type of identifier being used.  The properties of the object must be: `type`: "IdentifierValue", `identifierTypeName`: "«string»", `identifierValueCode`: "«string»".
- **streetAddress** – a string, the room number, building number, and street name.
- **postalCode** –a string, the postal code for the location.
- **addressCountry** – a string, the country of the location.

## Sample

A valid data snippet looks like:

```
{
    "id": "101",
    "name": "Rm101 Example Campus",
    "identifier": {
        "type": "IdentifierValue",
        "identifierTypeName": "Example",
        "identifierValueCode": "Eg101"
    },
    "streetAddress": "Room 101, 1 Example Street",
    "addressRegion": "West London",
    "addressLocality": "London",
    "postalCode": "W1A 1EG"
}
```