# Principles

These principles are designed to help us benchmark what is/isn’t in scope, and how we approach design and development of TADS.
They are derived from the existing [TAMS principles](https://github.com/bbc/tams/blob/main/PRINCIPLES.md).

## What is TADS?

1. TADS is designed as an interoperable timeline data framework.
   Having TADS support should enable integration with other TADS solutions, removing or minimising the need for bespoke integrations.
2. TADS is agnostic to clouds, and is intended to deploy anywhere, including on-premise and at the edge.
   It may describe how to integrate with a particular technology, but the core API can be implemented in many ways for many purposes.
3. TADS supports fast-turnaround/near-live workflows, but also works well for file-based workflows, and in some cases can take the place of live signal-centric workflows, if appropriate latency tradeoffs can be made.
4. TADS should contain minimal high-level data management functionality (data discovery etc)
5. TADS will be open source to reduce barriers to entry and maximise adoption: anyone should be able to participate in the ecosystem, providing maximum choice.
6. TADS is a specification and not an implementation/product.
7. TADS will be a living specification.
   It will evolve over time to meet the needs of the community.
8. TADS is primarily focused on media annotation.
   But it is not opinionated about what you store in it, or how that gets used.

## Guiding Principles

1. TADS is a small sharp tool.
It does not solve all problems in all ways.
2. TADS and the API should be as simple as possible, and always strike a balance across aspects such as complexity, capability, scalability.
3. TADS API servers and clients with compatible versions should interoperate.
   The specification is prescriptive and opinionated where necessary to enable this.
4. However we aim to give users as much flexibility as possible while ensuring interoperability.
5. The specification is agnostic to implementation, and we avoid implementation details driving decision-making (however we strike a balance in writing a specification that can be implemented)
6. Optional features and capabilities are used cautiously, to simplify Client Implementations and reduce integration engineering work.
7. We re-use patterns and approaches where possible: both within TADS, and drawing on existing approaches in other technologies.
    * e.g. TAMS and other existing timeline data formats
8. Breaking changes are possible, but we strike a balance to minimise impact and maximise benefit.
   We make decisions with strong engineering justification and consider the impact of change, through an open decision process.
    * Though we’re probably not there yet…
9. When common requirements arise, or the need for a common piece of functionality becomes clear, we attempt to standardise an approach either in the API Specification or an Application Note to enable interoperability.
