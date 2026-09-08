---
status: "accepted"
---
# Data Schema Storage in TADS

## Context and Problem Statement

TADS stores data payloads in the form of JSON.
To support interoperability and re-use, these payloads must be well defined and conform to a standard structure.
[JSON Schema](https://json-schema.org/) is the standard means to specify schemas for JSON data.
TADS Service Implementations will enforce conformance of data to a Track's specified schema.
This ADR explores options for where JSON schemas may be stored for use alongside data stored in TADS.

## Relevant Principles

What is TADS?\
1 - TADS is designed as an interoperable timeline data framework.
Having TADS support should enable integration with other TADS solutions, removing or minimising the need for bespoke integrations.

Guiding Principles:\
1 - TADS is a small sharp tool.
It does not solve all problems in all ways.
2 - TADS and the API should be as simple as possible, and always strike a balance across aspects such as complexity, capability, scalability.\
3 - TADS API servers and clients with compatible versions should interoperate.
The specification is prescriptive and opinionated where necessary to enable this.\
4 - However we aim to give users as much flexibility as possible while ensuring interoperability.\
5 - The specification is agnostic to implementation, and we avoid implementation details driving decision-making (however we strike a balance in writing a specification that can be implemented)\
7 - We re-use patterns and approaches where possible: both within TADS, and drawing on existing approaches in other technologies.

## Considered Options

* Option 1: Store schema directly in Track metadata
* Option 2: Store schemas at another well defined endpoint in TADS
* Option 3: Store schemas as part of a broader Profile at a well defined endpoint in TADS
* Option 4: Refer to schemas stored in an external system
* Option 5: Store schemas at another well defined endpoint in TADS, but allow the use of other Schema Stores

## Decision Outcome

Chosen option: Option 2 - Store schemas at another well defined endpoint in TADS.
It provides the lowest barrier to entry while meeting our requirements.
It may also be implemented in a manner that allows us to move to Option 5 in future.

Option 1 has been dismissed primarily because of the inability to explicitely re-use schemas, or to filter Tracks by schema.

Option 3 has been dismissed as the concepts of Schemas and Profiles have been judged to be independent and each useful without the other.
Conflating them adds undue complexity early on in development of TADS.
Additionally, Profiles are a relatively new addition to TAMS and may be subject to change.

Option 4 has been dismissed as it requires the deployment of poorly defined complementary systems for the storage/management of schemas.
This increases the barrier to entry for simple TADS implementations, and may inhibit interoperability.

Option 5 has been dismissed, at this point in time, due it being an extension of Option 2.
While it adds flexibility, it also adds undue complexity for this early stage in TADS development.
We may, however, choose to move to Option 5 as we gain experience and feedback.

## Pros and Cons of the Options

### Option 1: Store schema directly in Track metadata

This option would see schemas stored directly within the JSON structure that stores Track metadata.
Schemas may be re-used by registering Tracks with the same schema definition.
But there is no direct means of identifying which schema a Track is using.

* Good, because it doesn't requires the existance of an external schema management service
* Good, because it keeps TADS self-contained
* Good, because it avoids brittleness and inefficiencies of coupling TADS to an external service
* Good, because the lifecycle of schemas is tied to that of Tracks
* Neutral, because it avoids additional complexity in the API specification
* Neutral, because external systems cannot easily reference schamas stored in TADS
  * i.e use TADS as a Schema store
* Bad, becuase schemas cannot be explicitely re-used
* Bad, becuase Tracks cannot be filtered/queried by the schema they are created against
* Bad, because Client implementations cannot easily verify compatibility with Tracks
* Bad, becuase it inhibits explicite re-use of schemas stored beyond TADS

### Option 2: Store schemas at another well defined endpoint in TADS

This option would see a set of endpoints created for storing Schemas as a new resource.
Each Schema would have a unique ID.
Tracks would communicate the Schema they adhere to via this ID.
Tracks adhering to specific Schemas would be identified via a filter query parameter using this ID.

* Good, becuase schemas can be explicitely re-used
* Good, becuase Tracks can be filtered/queried by the schema they are created against
* Good, because Client implementations can easily verify compatibility with Tracks
* Good, because it doesn't requires the existence of an external schema management service
* Good, because it keeps TADS self-contained
* Good, because it avoids brittleness and inefficiencies of coupling TADS to an external service
* Good, because the lifecycle of schemas is under the control of TADS, with a garbage collection approach possible
* Neutral, because it adds complexity of the API specification
* Neutral, because other technical parameters (e.g. `allow_overlaps`) cannot be controlled
  * This will likely be useful for determining Client compatibility
  * The number of such technical parameters is currently limited
  * The number of such technical parameters may grow in future
* Neutral, becuase it diverges from existing patterns in TAMS
* Neutral, because external systems could easily reference schamas stored in TADS
  * i.e use TADS as a Schema store
* Bad, becuase it inhibits explicit re-use of schemas stored beyond TADS

### Option 3: Store schemas as part of a broader Profile at a well defined endpoint in TADS

TAMS has an emerging concept of [Profiles](https://github.com/bbc/tams/pull/130).
In TAMS, these Profiles are defined at a specific set of endpoints.
They define standardised sets of technical characteristics that may be re-used by Flows.
Flows adhering to specific Profiles may be identified via a filter query parameter using the Profile's ID.
Flows may be created using a Profile, or by specifying the technical metadata directly.

This option would see this pattern re-used in TADS.
This option would likely be an extension of Option 1.
But further restrictions may be applied such that Tracks may only be created via a Profile ID.

Note that dismissal of this Option does not imply the dismissal of Profiles as a concept.
This Option necessitates the implementation of Profiles as a concept in unison with schemas, as the Profile would be the primary means of schema management.
It is possible to implement schema management via one of the other options and overlay the concept of Profiles at a later date.

* Good, becuase Profiles, and by extension schemas, can be explicitely re-used
* Good, becuase Tracks can be filtered/queried by the Profile they are created against
* Good, because Client implementations can easily verify compatibility with Tracks
* Good, because other technical parameters (e.g. `allow_overlaps`) may be controlled
  * This will likely be useful for determining Client compatibility
  * The number of such technical parameters is currently limited
  * The number of such technical parameters may grow in future
* Good, becuase it re-uses patterns from TAMS
  * Although TAMS has significantly more technical parameters that need controlling than TADS!
* Good, because it doesn't requires the existance of an external schema management service
* Good, because it keeps TADS self-contained
* Good, because it avoids brittleness and inefficiencies of coupling TADS to an external service
* Good, because the lifecycle of schemas is under the control of TADS, with a garbage collection approach possible
* Neutral, because it adds complexity of the API specification
* Neutral, because external systems could easily reference schamas stored in TADS
  * i.e use TADS as a Schema store
  * This would likely require an endpoint that serves only the `schema` component of a given Profile
* Bad, becuase it inhibits explicite re-use of schemas stored beyond TADS
* Bad, because Profiles have only just been added to the TAMS spec and may be subject to change with real world experience

### Option 4: Refer to schemas stored in an external system

This option would see Tracks reference Schemas stored in external systems via a URL.

* Good, becuase schemas can be explicitely re-used
* Good, becuase Tracks can be filtered/queried by the schema they are created against
* Good, because Client implementations can easily verify compatibility with Tracks
* Good, becuase it allows explicite re-use of schemas stored beyond TADS
* Neutral, because it avoids additional complexity in the API specification
* Neutral, because other technical parameters (e.g. `allow_overlaps`) cannot be controlled
  * This will likely be useful for determining Client compatibility
  * The number of such technical parameters is currently limited
  * The number of such technical parameters may grow in future
* Neutral, becuase it diverges from existing patterns in TAMS
* Bad, because it requires the existance of an external schema management service
* Bad, because it couples TADS to such services
* Bad, because TADS functionality may be restricted if that service becomes unavailable
* Bad, because it requires TADS Service Implementations to frequently fetch/cache schemas
* Bad, because the lifecycle of schemas is not under the control of TADS

### Option 5: Store schemas at another well defined endpoint in TADS, but allow the use of other Schema Stores

This option sees Options 2 and 4 combined.
Instead of Tracks referring to Schemas via an ID, they are referred to by a URL.
Tracks may, however, refer to schemas in other schema stored by URL.

* Good, becuase schemas can be explicitely re-used
* Good, becuase Tracks can be filtered/queried by the schema they are created against
* Good, because Client implementations can easily verify compatibility with Tracks
* Good, becuase it allows explicite re-use of schemas stored beyond TADS
* Good, because it doesn't requires the existance of an external schema management service
* Neutral, because other technical parameters (e.g. `allow_overlaps`) cannot be controlled
  * This will likely be useful for determining Client compatibility
  * The number of such technical parameters is currently limited
  * The number of such technical parameters may grow in future
* Neutral, becuase it diverges from existing patterns in TAMS
* Neutral, because the lifecycle of some, but not all, schemas is not under the control of TADS
* Bad, because it adds significant additional complexity in the API specification with multiple modes of operation
* Bad, because it couples TADS to external schema management services, where used by Tracks
* Bad, because TADS functionality may be restricted if that service becomes unavailable
* Bad, because it requires TADS Service Implementations to frequently fetch/cache schemas where managed by an external schema store
