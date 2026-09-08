---
status: "accepted"
---
# One Payload Format or Multiple

## Context and Problem Statement

There are many common data formats in use within the media and IT industries.
The design of certain aspects of the TADS API may change based on the data format chosen, or if we choose to support multiple formats.
These include how event payloads are presented against their TADS metadata, primarily the timerange and higher level list structure in event listings.
Additionally, the query language chosen may be dependent on our approach to data formats.

## Relevant Principles

What is TADS?\
1 - TADS is designed as an interoperable timeline data framework.
Having TADS support should enable integration with other TADS solutions, removing or minimising the need for bespoke integrations.\
4 - TADS should contain minimal high-level data management functionality (data discovery etc)\
8 - TADS is primarily focused on media annotation.
But it is not opinionated about what you store in it, or how that gets used.

Guiding Principles:\
1 - TADS is a small sharp tool.
It does not solve all problems in all ways.\
2 - TADS and the API should be as simple as possible, and always strike a balance across aspects such as complexity, capability, scalability.\
3 - TADS API servers and clients with compatible versions should interoperate.
The specification is prescriptive and opinionated where necessary to enable this.\
4 - However we aim to give users as much flexibility as possible while ensuring interoperability.\
5 - The specification is agnostic to implementation, and we avoid implementation details driving decision-making (however we strike a balance in writing a specification that can be implemented)

## Considered Options

* Option 1: Single payload format
* Option 2: Multiple payload formats
* Option 3: Arbitrary payload formats
* Option 4: Specify the characteristics of the payload, but not the format itself

## Decision Outcome

Chosen option: Option 1 Single payload format, because:

* This makes everything about designing and using the API so much simpler
* TADS is (at least initially) about ANNOTATING content timelines with DATA i.e. we’re interested in the INFORMATION — we're not interested in the ASSETS e.g. data files (and so people can just convert the format to/from JSON if they want to use TADS)
* For certain use cases (maybe TTML subtitles etc) and/or where you are interested in storing/retrieving arbitrary “assets” then TAMS might be a better answer

## Pros and Cons of the Options

### Option 1: Single payload format

This option would see us choose a single format (i.e. one of JSON, XML, etc).
All payloads would use this format.

* Good, because it simplifies design and implementation
* Good, because we may be able to make the API body format match the payload format
* Good, because implementations not operating on the data itself can be implemented without adaptations to the payload formats
* Neutral, because this puts less complexity in the API, but potentially more in the client in some cases where translation to/from other format are required
  * "Neutral" as this is a trade off, and many clients will just implement the specified format
* Bad, because data originating in/exporting to other formats must be translated or wrapped in the specified format
  * Additionally, embedded payloads may not be searchable/filterable

### Option 2: Multiple payload formats

This option would see us choose multiple formats, or place restrictions on formats, to ensure we can effectively integrate search functionality, event listing, etc.
This option may see us limit formats to, for example, string-based formats to enable string-based or other generic query mechanisms.
Or we may choose a format-specific query language per format.
We shall appropriately signal the format (as with media formats in TAMS) to enable implementations to determine compatibility.

Note that in this option, the severity of the "Neutral" and "Bad" options may be minimised by minimising the number of payload formats supported.

* Good, because implementations not operating on the data itself can be implemented without adaptations to the payload formats
  * Although this may depend on consistency of API body format, and how variation of payload format interacts with the API
* Neutral, because implementations will have a fixed, though greater than one, number of formats to support
* Neutral, because data originating in/exporting to other formats must be translated or wrapped in one of the specified formats
  * Additionally, embedded payloads may not be searchable/filterable
  * "Neutral" because we would likely aim to support most major formats directly
* Bad, because this puts more complexity in the API, and more in the client to support multiple formats
* Bad, because clients may choose to support a subset of formats resulting in fragmentation of the ecosystem

### Option 3: Arbitrary payload formats

This option would see us place no restriction on formats.
Binary formats would be allowed, for example.
We shall appropriately signal the format (as with media formats in TAMS) to enable implementations to determine compatibility.

* Good, because implementations not operating on the data itself can be implemented without adaptations to the payload formats
  * Although this may depend on consistency of API body format, and how variation of payload format interacts with the API
* Good, because writing clients are free to work in their native format
* Bad, because consuming clients will have to make a judgement call on formats to support resulting in fragmentation of the ecosystem
* Bad, because the format of the API bodies would likely differ from the payload
  * While we we could use the Content-Type header, this would mean specifying the API in multiple formats
  * We would not, however, be able to anticipate all formats to be used
  * Further, some formats aren't appropriate for HTTP APIs
* Bad, because this puts more complexity in the API, and more in consuming clients to support multiple formats, but less in writing clients

### Option 4: Specify the characteristics of the payload, but not the format itself

This option would see us specify the characteristics of the payload, but not the format.

Good/neutral/bad as with Option 4 plus:

* Neutral, because some functionality may be generalised based on the characteristics
  * "Neutral" because its unclear if this would actually provide benefits in practice
