---
status: "accepted"
---

# Connect Data and Media Timelines

## Context and Problem Statement

In many situations, TADS event timestamps by themselves are not enough to unambiguously determine how the events align with the media content they are annotating.
This is because:

* identifiers for media content items might not resolve to exactly one timeline
* media timing, depending on the specifics of the standard used, might present unexpected particularities that go against commonly held assumptions (e.g. timecode rollover results in timestamp chronological order being different from numeric order; UTC leap second smearing introduces variability to the length of a second)
* characteristics of the clocks used to time the media could result in divergence between media and timeline data timing if they are not measured against the same clock
* assumptions cannot be made about how the timeline data timings relate to "SI seconds" or to "real time".
It is not guaranteed that it is known about whether the timings are linear, what clock they came from or whether there are time jumps (etc.) present.

Note: The media content is only in the scope of this ADR regarding its timeline and how that is relevant to TADS event timing.

There are many different types of relationships media content items could have with each other or with the timeline data annotating them (e.g. which content the timeline data was created from, which content the timeline data describes, which aspects of the content it describes, how it was generated, the ancestry of the content it was generated from etc).
The solution to the problem at hand should not attempt to address or model these other relationships, instead only focusing on timing relationships.

## Relevant Principles

What is TADS?\
3 - TADS supports fast-turnaround/near-live workflows, but also works well for file-based workflows, and in some cases can take the place of live signal-centric workflows, if appropriate latency tradeoffs can be made.\
8 - TADS is primarily focused on media annotation.
But it is not opinionated about what you store in it, or how that gets used.

Guiding Principles:\
1 - TADS is a small sharp tool.
It does not solve all problems in all ways.\
2 - TADS and the API should be as simple as possible, and always strike a balance across aspects such as complexity, capability, scalability.\
3 - TADS API servers and clients with compatible versions should interoperate.
The specification is prescriptive and opinionated where necessary to enable this.\
6 - Optional features and capabilities are used cautiously, to simplify client implementations and reduce integration engineering work.

## Decision Drivers

* TADS data can annotate media that is not stored in TAMS
* Timing is a complex issue with a variety of standards and particularities on a case-by-case basis that make generalisation difficult
* The use of TADS should not be predicated on information that the user might not be reasonably expected to have access to

## Considered Options

* Option 1: Do nothing in TADS and handle the timing specifics elsewhere
* Option 2: Do nothing in TADS and restrict timing information
* Option 3: Implement the full comprehensive solution to describing timing data in TADS
* Option 4a: Describe how TADS data relates to the timeline of the media it annotates
* Option 4b: Only specify which media timeline TADS data annotates

## Decision Outcome

Chosen option: Option 4b "Only specify which media timeline TADS data annotates", because

* It allows us more flexibility for potential future developments
* It involves the minimum design/implementation work necessary for TADS to be functional
* It proposes a solution that works for many different scenarios without requiring individual treatment.
* We believe that signalling 'which' or 'what kind of' media timeline is used for TADS event timings should be enough to mitigate the problem at hand.

In the current iteration of TADS, timeline data is always related to media content.
As such, the problem of specifying timing information for timeline data can potentially be reduced to specifying how data timings relate to existing media timings.
Through this approach, TADS data would always be in alignment with the media it annotates.

Performing calculations using the timing data could still be problematic depending on the type of timing used, however this would be an issue inherited from the characteristics of the media timing.

## Pros and Cons of the Options

### Option 1: Do nothing in TADS and handle the timing specifics elsewhere

This option would see the creation of a different service for managing relationships between timelines.

* Good, because it keeps TADS lightweight and straightforward
* Good, because it does not introduce restrictions to timeline data stored in TADS
* Neutral, because the problem of aligning the content and annotation timings moves elsewhere and still needs a solution
* Bad, because it creates a dependency on a different service to make full use of TADS
* Bad, because it leaves timing ambiguous inside TADS

### Option 2: Do nothing in TADS and restrict timing information

This option would see us putting out strict guidelines on the specifics of event timings, on acceptable use cases based on the timing type of the media being annotated.

* Good, because it reduces complexity in TADS
* Good, because it clarifies the scope of acceptable use of TADS
* Good, because it explicitly states which assumptions about timing information a client can hold reliably
* Bad, because it introduces significant barriers to TADS adoption
  * It potentially limits TADS to only being used in conjunction with TAMS, which goes against our stated goals
* Bad, because it requires re-timing data (potentially media as well) before being able to store in TADS

### Option 3: Implement the full comprehensive solution to describing timing data in TADS

This option would see the design and implementation of a time context store inside TADS.

* Good, because it fully solves the issue of timing ambiguities
* Bad, because it requires a significant amount of design work and iteration on a feature that does not necessarily belong inside TADS
* Bad, because it involves proposing a unified solution including use cases that might never arise in TADS
* Bad, because it requires maintenance work whenever a new timing scenario becomes a TADS use case
* Bad, because it requires factual information about media content timing that might not be available up-front (or at all) in the context of annotating media

### Option 4a: Describe how TADS data relates to the timeline of the media it annotates

This option would see the introduction of a property that specifies the details of the relationship between the timing in TADS events and the timing in the media they annotate.

* Good, because it enables the flexibility to store events timed against a different clock to the one used by the media content item being annotated
  * For example, events annotating a live stream sourced from an external dataset clocked against 'real time' as opposed to transmission time
* Good, because it keeps TADS relatively lightweight
* Good, because it makes an explicit connection between data and media timings
* Good, because it has potential for future expansion of considered use cases
* Bad, because it involves continued work on accomodating specific use cases
* Bad, because it can potentially create a complex web of timing relationships
* Bad, because it requires additional calculations on the client side to match event timings to media timing
* Bad, because it introduces complexity in ensuring the ingested data is correct/ of good quality

### Option 4b: Only specify which media timeline TADS data annotate

Similar to option 4a, this option would see the introduction of a property for describing timing relationships.
Unlike option 4a, this option would require event timings to fully align with the timeline of the media content they annotate.

* Good, because it keeps TADS lightweight and the underlying data model simple
* Good, because it makes an explicit connection between data and media timings
* Good, because it is constrained to a manageable scope
* Good, because it allows for future extensions/expanding the scope of timeline types which can be referenced
* Neutral, because it relies on the assumption that a TADS user would already have the specifics on how to use the timeline data from elsewhere
  * It is not necessarily an unreasonable assumption, and the situations in which it does not hold are potentially mitigated by TADS containing enough information to point the user towards an answer
* Neutral, because it potentially requires re-timing events to align them to the media content timeline
* Bad, because it potentially introduces the need to manage a dictionary of "timeline types"
* Bad, because it means that the event timings inherit the characteristics of the media timeline
  * If there are jumps or other discontinuities, they would be present in TADS event timings, such that it is not guaranteed that an event duration would be `end time - start time`
