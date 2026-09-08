---
status: "accepted"
---
# Support for Overlapping Events

## Context and Problem Statement

We are aware of various use cases that may want to make use of overlapping events.
A basic example being representing people/items on screen.
Do we want to allow overlapping events, or require tracks to be structured such that events are de-conflicted?
If we allow overlapping events, how should we individually identify them?
Time alone might not be workable where events exactly overlap.
TAMS does not allow overlapping segments.
SSTDF allows overlapping events.

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

## Considered Options

* Option 1: Allow overlapping events
* Option 2: Allow overlapping events, but not identical event timeranges
* Option 3: Disallow overlapping events

## Decision Outcome

Chosen option: Option 1: Allow overlapping events, because:

* There are practical use cases that require overlapping events
* Prohibiting overlapping events that have identical timeranges is an extra complication to data modelling
* If we’re allowing editing and have an ID for each event anyway, then there’s no need to use the timerange as an ID for the event i.e. no strong case for prohibiting overlapping events

## Pros and Cons of the Options

### Option 1: Allow overlapping events

* Good, because we have identified use case that will need overlapping events of some form
  * People on screen
  * Transcripts of people talking over each other
* Good, because we believe it wouldn't be possible to "normalise" these events into multiple non-overlapping event tracks in all cases
* Neutral, because the addition of an overlapping events at a point on the timeline where an event already exists could be considered an edit at the point
  * This would break immutability with respect to the existing TAMS data model
* Neutral, because we would not be able to use timerange to uniquely identify events on a timeline

### Option 2: Allow overlapping events, but not identical event timeranges

* Neutral, because the addition of an overlapping events at a point on the timeline where an event already exists could be considered an edit at the point
  * This would break immutability with respect to the existing TAMS data model
* Neutral, because we would be able to use timerange to uniquely identify events on a timeline
* Bad, because we have identified use case that will need overlapping events of some form
  * People on screen
  * Transcripts of people talking over each other
* Bad, because we believe it wouldn't be possible to "normalise" these events into multiple non-overlapping event tracks in all cases

### Option 3: Disallow overlapping events

* Neutral, because the addition of an overlapping events at a point on the timeline where an event already exists could be considered an edit at the point
  * This would break immutability with respect to the existing TAMS data model
* Neutral, because we would be able to use timerange to uniquely identify events on a timeline
* Bad, because we have identified use case that will need overlapping events of some form
  * People on screen
  * Transcripts of people talking over each other
* Bad, because we believe it wouldn't be possible to "normalise" these events into multiple non-overlapping event tracks in all cases
