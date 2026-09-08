---
status: "accepted"
---
# Initial Design Assumptions

## Context and Problem Statement

Overall, TADS is a set of interface definitions for writing timeline data to a store, reading it from the store, and searching by the data.
It provides a framework for sharing that timeline data between systems, solutions and organisations using a cloud-native approach.
TADS is inspired by TAMS data model, those of related timeline data formats, and is designed to work alongside them as well as all other forms of media.

This document identifies an initial set of working assumptions used when starting work on TADS.

## Terminology

This document refers to "Tracks" and "Events" as key components of the TADS data model.
This is based on early identification of the requirement for editable and overlapping data payloads.
This goes against the TAMS data model primaries of Sources, Flows, and Segments.
As such, a new data model is required.
This initial data model consists of "Tracks" (roughly equivelant to TAMS Flows) and "Events" (roughly equivelant to TAMS Segments).
This data model will be described elsewhere in due course.

## Use Cases

### Annotation

Our primary initial use case.

The storing/attachment of descriptive metadata related to existing media content on an existing timeline.
This may include transcriptions, subtitles, speaker ID etc.
The specifics of the data and how it is generated/used is out of scope.
But serves as a guideline to how we approach ingest, outgest, storage, and querying of data.

## Requirements

* Timeline data may be generically associated with a time-based content item (which could be a media asset such as a file or TAMS Flow, or an entity such as a Programme Version or a TAMS Source)
* The API must allow accumulation of timeline data relating to a media timeline as independent “Tracks”
* The API should allow write/read of one or more “Events” in a single request covering arbitrary timerange
  * i.e. suitable for multiple scenarios such as live media annotation and batch processing of media files
* The API allows querying Events by ID/time/metadata
  * timerange/batch size of Events used for writing does not affect timerange/batch size of Events on reading
* Data is provided within requests/responses and not by reference (which TAMS does)
* The API should support event notification of changes to clients e.g. via webhooks
* Detailed metadata about the timeline data can be managed by the API
  * e.g. with tags/taggings -- info such as versions of software tools used to generate the data
* Metadata/properties/hooks are provided to aid larger management activities like payload schema management, timeline data discovery etc
  * But the management activities themselves are out of scope of the API
* Overcome the limitations of storing timeline data directly in TAMS
  * Beyond already stated: assumption of segmentation, assumption of periodicity, and multi-transaction write semantics
* The API should be applicable to different timeline data payloads such as structured text, vectors, linked data subgraphs etc

## Principles

TADS has a set of [principles](https://github.com/bbc/tads/blob/main/PRINCIPLES.md), and statements of what TADS is, derrived from from [TAMS](https://github.com/bbc/tams/blob/main/PRINCIPLES.md).
This is in order to:

* Facilitate alignment with TAMS and the broader Time-Addressable Media ecosystem
* Aid fast and effective design and iteration of TADS
