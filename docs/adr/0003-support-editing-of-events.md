---
status: "accepted"
---
# Support Editing of Events

## Context and Problem Statement

TAMS has considered Flows and their Segments to be immutable.
This allows workflows to use media by reference and know they will always get the same media when retrieving it.
Do we want to keep this approach with TADS, or should we allow editing of content?
Should edit be implemented at a layer above?

Note that if events may be edited, this breaks the immutability property of Sources/Flows/Segments.
As such, the equivalent concepts in TADS will NOT be Source/Flows/Segments and will need new naming.
Cross-referencing between TAMS and TADS will NOT be able to re-use the existing fields such as Collects relationships, or re-use existing IDs (i.e. the same ID for the Source/Flow in TAMS, and for the related entity in TADS).

## Relevant Principles

What is TADS?\
3 - TADS supports fast-turnaround/near-live workflows, but also works well for file-based workflows, and in some cases can take the place of live signal-centric workflows, if appropriate latency tradeoffs can be made.\
4 - TADS should contain minimal high-level data management functionality (data discovery etc)\
8 - TADS is primarily focused on media annotation.
But it is not opinionated about what you store in it, or how that gets used.

Guiding Principles:\
1 - TADS is a small sharp tool.
It does not solve all problems in all ways.\
2 - TADS and the API should be as simple as possible, and always strike a balance across aspects such as complexity, capability, scalability.\
6 - Optional features and capabilities are used cautiously, to simplify client implementations and reduce integration engineering work.\
7 - We re-use patterns and approaches where possible: both within TADS, and drawing on existing approaches in other technologies.

## Decision Drivers

* Users will want to edit their timeline data given how easy that is (compared to media) e.g. to correct a typo in a transcript
* We know that existing workflows have timeline data editing functions

## Considered Options

* Option 1: Disallow editing of events
* Option 2: Allow editing of events
* Option 3: Allow editing of events in exceptional circumstances only

## Decision Outcome

Chosen option: Option 2 Allow editing of events, because:

* Practical use cases require editing of events
* The practical considerations in TAMS around needing media assets to be immutable to streamline otherwise costly operations (such as edit by reference) are not such a concern for data
* TADS is primarily concerned with ANNOTATING media timelines — it makes sense for the thing we’re referencing (the media e.g. in TAMS) to be immutable but the things we layer on top (the annotations) don’t need to be immutable

### Consequences

Some “mitigations”/consequences/ideas to consider:

* Each event probably needs to have a (globally unique) ID
* TADS (or the backend DB etc) could potentially keep track of the history of an event i.e. log the changes to it
* There could be a means of locking a set of events (perhaps over a specified timerange of the media timeline); editing of locked events is prohibited.
This sort of functionality could probably be layered on top, if needed (it could be just like fine-grained permissions/access).
  * Maybe like `read_only` on a Flow in TAMS

## Pros and Cons of the Options

### Option 1: Disallow editing of events

This option would see editing of events being prohibited entirely.
Events are immutable.

* Good, because it better supports "edit-by-reference" workflows
  * The combination of ID + Time will always return the same data
* Good, because it maps to the existing TAMS data model
* Neutral, because data can't be "improved" over time
* Bad, because it precludes the use of overlapping events
  * Writing overlapping events may be considered an edit where ID + time is considered the primary identifier
* Bad, because corruption/errors cannot be corrected

### Option 2: Allow editing of events

This option would see editing of events allowed.
TADS would in effect be usable as a work-in-progress store.
Events are not immutable.

Note: If we choose to support overlapping events, that may imply that we choose this option.

* Good, because it allows the use of overlapping events
  * Writing overlapping events may be considered an edit where ID + time is considered the primary identifier
* Good, because corruption/errors can be corrected
* Good, because data may be "improved" over time
  * e.g. Better transcriptions
* Neutral, because it supports "edit-by-reference" workflows less well
  * The combination of ID + Time won't always return the same data
* Bad, because it doesn't directly map to the existing TAMS data model

### Option 3: Allow editing of events in exceptional circumstances only

This option is similar to the approach taken by TAMS.
Editing of events isn’t directly allowed.
Events can be deleted and re-created with new data.
But this is strongly discouraged.
Events are considered immutable, but mistakes may be fixed in exceptional circumstances.

* Good, because it better supports "edit-by-reference" workflows
  * The combination of ID + Time will always return the same data
* Good, because it maps to the existing TAMS data model
* Good, because corruption/errors can be corrected
* Neutral, because data can't be "improved" over time
* Bad, because it precludes the use of overlapping events
  * Writing overlapping events may be considered an edit where ID + time is considered the primary identifier
