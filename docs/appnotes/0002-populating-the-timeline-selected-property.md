# 0002: Populating the `timeline_selected` property

## Summary

[`timeline_selected`](https://bbc.github.io/tads/0.1/index.html#/operations/GET_tracks-track_id) is intended to be used as an identifier for the specific timeline used for Track Event timings where the `entity_id` does not resolve to a single timeline.
The values for this property are defined in this document.

The recommendation is to use [`timeline_reference.entitiy_id`](https://bbc.github.io/tads/0.1/index.html#/operations/GET_tracks-track_id) values that do not require disambiguation through the `timeline_selected` property in the first place, however this may not be feasible for all workflows.

## Context

The timeline data stored in a Track uses the timeline identified through the `timeline_reference` property in the format:

```plaintext
timeline_reference: {
  entity_id: [URI],
  timeline_selected: [URI]
}
```

`entity_id` is mandatory and in most scenarios should be enough to clearly specify the timeline used by the Track.
This field signals that Track Events within the Track annotate the `entity_id` timeline at their [timeranges](https://bbc.github.io/tads/0.1/index.html#/schemas/timerange).

* In a Track with `entity_id` referencing a TAMS Source, a TADS Track Event with the timerange `[0:0_5:0)` annotates the TAMS media content between `[0:0_5:0)`.

There are some situations in which `entity_id` does not resolve to exactly one timeline.
If `entity_id` references a media file, it would not be uncommon for that file to have several timeline representations.
For example:

* a synthetic timeline beginning at `0` seconds where the media content playback starts
* a timeline using timecode with the file beginning at `09:59:30:00` (and the programme content starting at `10:00:00:00`).

To illustrate why this could be a problem, consider that the content lasts for 12 hours.
If a Track Event has the timerange `[36000:0_36001:0)` (i.e. begins at 10hr and ends at 10hr 0min 1s), is this supposed to represent the content at the very beginning of the progamme (using the timecode timeline), or ten hours into the file (using the synthetic timeline)?
Without any additional information, both answers are valid.

To resolve this ambiguity, `timeline_selected` must be used to specify *which* timeline of `entity_id` the Track is referencing.

`timeline_selected` should only be populated with the values defined in the list below.
The expectation is that `timeline_selected` will be omitted where `entity_id` points to a single timeline.

## List of `timeline_selected` values

| Value                               | Meaning                                                                                               |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `urn:x-tads:timeline:playback`      | Track Events are timed using the content timeline which starts with `0` at the beginning of playback. |
| `urn:x-tads:timeline:timecode_main` | Track Events are timed using the main (primary) timecode of the item referenced by `entity_id`.       |

## Examples

### Media files

An MXF file following the example above (that sets out how a media file could have more than one timeline associated with it) might have both:

* a synthetic output (playback) timeline (represented by the Material Package)
  * The file starts at `0` seconds and the programme starts at `30` seconds on this timeline.
* a Timecode Track in the Material Package
  * This is the main (primary) timecode.
  * The file starts at `09:59:30:00` and the programme starts at `10:00:00:00`.

A TADS Track annotating this file (with an identifier for the file in the Track's `timeline_reference.entity_id`) could have `timeline_selected` as:

* `urn:x-tads:timeline:playback`, where:
  * `[0:0]` marks the start of the file
  * `[0:0_30:0)` covers the duration of the pre-programme material.
* `urn:x-tads:timeline:timecode_main`, where:
  * `[35970:0]` marks the start of the file
  * `[35970:0_36000:0)` covers the duration of the pre-programme material.

Alternatively, the Timecode Track inside an MXF file could, for example:

* have a value of `01:00:00:00` as programme start, which would be `[3600:0]` in the corresponding `urn:x-tads:timeline:timecode_main` TADS track
* represent time of day, where the programme start for an 8 PM broadcast would be `20:00:00:00`, which would be `[72000:0]` in the corresponding `urn:x-tads:timeline:timecode_main` TADS track.

## Adding to the list

Additional values could be added to the list of `timeline_selected` values presented in this document to better reflect the types of timelines that might be "bundled" in the same identifier referenced by `entity_id`, subject to review.

If you have identified a potential TADS use case that would benefit from different values for `timeline_selected`, please do get in touch.
