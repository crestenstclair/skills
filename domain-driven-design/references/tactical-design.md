# Tactical phase contracts

Use only after the parent accepts discovery and strategic analysis. Each phase receives all accepted upstream conclusions and unresolved questions, follows the result contract in [SKILL.md](../SKILL.md), and reports conflicts rather than silently revising the model.

Contents: [behavior and rules](#6-use-cases--commands--queries), [aggregates](#8-aggregates--consistency-boundaries), [identity and values](#9-entities-and-value-objects), [policies/services](#10-domain-policies--services), [events](#11-domain-events), [repositories](#12-repositories), [application](#13-application-orchestration), [infrastructure](#14-infrastructure--adapters), [example and sources](#worked-reasoning-example).

## 6. Use cases / commands / queries

Describe actor intentions and meaningful outcomes in each context's language. For each use case, give its trigger, required information, affected context, success result, rejection paths, and interaction with other contexts. Commands express intentions that may fail; queries express information needs without changing domain state.

Prefer “place a hold” over “update patron” when that behavior exists. Retain CRUD for genuine data maintenance. Distinguishing commands and queries here does not require CQRS, separate databases, a message bus, or a command class for every function. Return representative scenarios that the next phase can use to expose rules.

## 7. Business rules and invariants

Extract rules from those scenarios and evidence before choosing aggregates. For each rule, record its source, context, preconditions, postconditions, valid/invalid transitions, time semantics, concurrent actors, and consequence of violation. Distinguish:

- An invariant that must hold at an operation's commit/observable consistency boundary.
- A precondition requiring authoritative current information rather than a stale read.
- A workflow obligation that may be fulfilled later within a business-accepted delay.
- A presentation preference or implementation constraint that is not a domain invariant.

Return a rule ledger with stable IDs, examples and counterexamples, required consistency, and open questions. Try simultaneous commands, retries, expiration at a boundary time, and changes between checking and committing. Distinguish authentication/technical access control from business eligibility and authority rules; business rules remain domain-owned even when both restrict an action. Do not fabricate numeric limits, ordering guarantees, acceptable delays, or policy authority. Block dependent decisions when those facts are material and unknown.

## 8. Aggregates / consistency boundaries

Start from the accepted invariant ledger, not tables, foreign keys, ORM graphs, JSON nesting, or convenient object ownership. Identify a candidate root with domain identity and the state needed to enforce each invariant. Phase 9 refines these types; report a conflict if that refinement changes the boundary.

Apply Evans and Vernon's guidance:

1. **Model true invariants inside consistency boundaries.** Explain which rules must hold together after a command and why. Give the root responsibility for protecting internal state; external mutations must not bypass it.
2. **Design small aggregates.** Keep only state needed for that consistency. A single entity can suffice. Compare both a larger cluster and a smaller split: large graphs cause unnecessary contention; fragmentation can lose invariants and add coordination costs. “Small” is not a fixed object count.
3. **Reference other aggregates by identity.** A reference does not merge their consistency boundaries. Do not navigate and mutate a foreign aggregate as if it were an owned child. Supply external facts explicitly when needed, stating their authority and freshness.
4. **Prefer eventual consistency outside the boundary.** Ask whose job it is to make the related state consistent and what delay the business can tolerate. Specify failure, retry, duplicate handling, and compensation or intervention obligations before promising eventual convergence. Events, scheduled work, or other mechanisms may implement it; a broker is not required.

For each candidate, return: root and boundary; protected rule IDs; allowed operations; atomic changes; relevant concurrency scenarios and required protection; external identity references; delayed obligations and their owners; and alternatives rejected with reasons. Preserve hard invariants under concurrent writes; an in-memory check alone cannot guarantee them. Choose concrete locking/version/constraint mechanisms in phase 14 without weakening the requirement here.

If one use case appears to require atomic updates to several aggregates, challenge both the rule and the model with domain evidence. It may reveal a missing concept or a mistaken boundary. Do not impose eventual consistency that violates a real business guarantee, or reject it solely from technical habit. An exception must state the business reason, transaction scope, and cost. Never merge an entire context into a giant aggregate to conceal the problem.

## 9. Entities and value objects

For each proposed type, require business meaning within its context. An **entity** needs continuity and identity despite changing attributes; explain what makes it the same thing over time and which lifecycle belongs to it. A database-generated ID alone does not justify domain identity. An aggregate root is an entity; inner entities need not become separate roots.

A **value object** is defined by its meaningful attributes, not identity. Specify equality, units where relevant, validity, and useful operations; prefer immutable values and replacement. Do not wrap every primitive or create an identifier for every value. The same real-world concept can be modeled differently in different contexts.

Return the minimal types and their behavioral contracts, including construction and mutation rules. Plain records, functions, and language-native values can be sufficient; no mandatory base classes, marker interfaces, factories, or object-oriented layout. Feed contradictory identity/lifecycle evidence back to aggregates or contexts.

## 10. Domain policies / services

Assign each rule to the entity, value, or aggregate that naturally owns the behavior. A policy can name a meaningful business decision; it need not become a class or extension framework. Use a domain service only for significant domain behavior whose placement on those objects would distort their meaning.

For any standalone operation, specify its domain name, inputs, decision/result, governing rules, and why existing model objects are unsuitable owners. Keep domain services free of application sequencing and transport responsibilities. Avoid generic `SomethingDomainService` dumping grounds and services that merely manipulate otherwise passive model data. “No domain service needed” is a complete answer.

## 11. Domain events

Revisit occurrences found during discovery and use-case analysis. Formalize only facts that domain experts care about, explain state changes, or drive required domain reactions. Name them in the past tense and identify the producing operation, meaningful payload, affected identities, occurrence time where relevant, and actual consumer or purpose. Treat facts as immutable.

Separate a fact from a command requesting it, a callback implementing it, and a transport message carrying it. An integration event is a boundary-facing contract and may translate or select information from domain events; a Kafka topic is not a domain concept. Domain events imply neither event sourcing nor asynchronous transport.

Return necessary facts, their semantic ordering/identity needs, and the rule or workflow each supports. If publication crosses a transaction boundary, record the reliability requirement for phases 13–14. Do not invent events for every setter or demand them when direct domain operations suffice.

## 12. Repositories

Follow Evans' collection-like access to aggregate roots in domain language. Propose a repository only for a root type that needs direct retrieval or persistence access. Define lookup/add/remove or equivalent operations that the use cases actually need, while preserving the illusion of a complete valid aggregate even if loading is lazy.

Do not create a repository per table, child entity, or persistence record. Keep storage mapping and query syntax out of domain contracts. Application query paths may return read projections without pretending those are mutable aggregates; they must not bypass invariant protection for writes. This does not require CQRS.

Return necessary access contracts and their callers, or explain why existing/simple persistence is sufficient. Repository ownership does not imply that a repository independently commits each write; leave transaction coordination explicit in the application phase.

## 13. Application orchestration

Trace representative end-to-end use cases: validate external input, authenticate/authorize the caller, obtain required state, invoke domain behavior, persist, and coordinate external effects. Distinguish business eligibility/authority decisions from technical access enforcement. The application coordinates work; it must not decide domain rules that belong in the model.

Return the sequence, transaction boundary, rule owners, rejection/failure results, concurrency-conflict handling, and retry behavior. For multi-step workflows, identify durable progress and compensation only where needed. Do not add a saga framework by default.

Separate recording a domain fact from publishing it externally. External observers must not receive a success fact for a rolled-back change; if reliable delivery is required, define how committed changes avoid lost publication. In-process handlers may participate in the same transaction when justified; that does not make external delivery atomic. Pass these guarantees to the infrastructure phase rather than choosing a broker prematurely.

## 14. Infrastructure / adapters

Now map the accepted contracts onto the actual language, libraries, database, framework, APIs, and deployment constraints. Reuse fitting project mechanisms. Keep domain decisions independent of database schemas, ORM navigation, web requests, cloud SDKs, message formats, and serialization details unless a demonstrated tradeoff justifies coupling.

Return the smallest concrete mapping that preserves the model: boundary validation and translation; persistence and concurrency enforcement; transaction implementation; required delivery/deduplication guarantees; and tests of important invariants and integration seams. Use an existing reliable mechanism before proposing new infrastructure. An outbox or similar mechanism is warranted only when its delivery guarantee is needed.

For existing systems, distinguish current adapters from proposed changes, preserve compatibility, and prefer incremental seams over rewrites. Identify migration/rollback needs if data or contracts must change. If available infrastructure cannot enforce an accepted invariant, emit an upstream conflict; do not quietly weaken the model.

## Worked reasoning example

The [library example](https://github.com/ddd-by-examples/library) starts from lending workflows and business rules, explores examples, and distinguishes lending from catalogue. “Book” can mean a catalogued description or a lendable copy. That semantic difference informs contexts before tactical types.

In its lending model, “place a hold” leads to patron eligibility and hold-limit rules. The [`Patron`](https://github.com/ddd-by-examples/library/blob/master/src/main/java/io/pillopl/library/lending/patron/model/Patron.java) applies named [policies](https://github.com/ddd-by-examples/library/blob/master/src/main/java/io/pillopl/library/lending/patron/model/PlacingOnHoldPolicy.java) and produces hold facts; the [application operation](https://github.com/ddd-by-examples/library/blob/master/src/main/java/io/pillopl/library/lending/patron/application/hold/PlacingOnHold.java) loads state and coordinates the result. Its [design exploration](https://github.com/ddd-by-examples/library/blob/master/docs/design-level.md) weighs Patron/Book consistency and compensation. These show how rules, model behavior, and orchestration connect; they do not prove that the same aggregate split or consistency choice fits another library.

The example uses richer modeling for lending and CRUD for catalogue. Borrow that proportionality, not its Java/Spring architecture, repository publication convention, or optional CQRS. For a new domain, independently test simultaneous holds and book availability against that domain's required guarantees. An example implementation never overrides a demonstrated invariant.

## Source grounding

Evans-derived concepts here are adapted and condensed from Eric Evans, *Domain-Driven Design Reference: Definitions and Pattern Summaries* (2015), © Eric Evans, licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Added phase contracts and implementation guidance are not quotations or mandatory DDD architecture.

- [Evans' Reference](https://www.domainlanguage.com/ddd/reference/) and [PDF](https://www.domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf): entities, values, events, services, aggregates, repositories (printed pp. 11–17), and assertions (p. 22).
- [Vernon: Effective Aggregate Design](https://www.dddcommunity.org/library/vernon_2011/) and [author's publication page](https://kalele.io/effective-aggregate-design/): Part I develops true invariants and small aggregates; Part II covers identity references and eventual consistency; Part III tests boundaries through discovery and considers the costs of excessive splitting. Apply these arguments as domain-driven rules of thumb, with justified exceptions.
- [Fowler: DDD Aggregate](https://martinfowler.com/bliki/DDD_Aggregate.html): root-mediated access and the aggregate as the unit of consistency.
- [Microsoft: domain model guidance](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/microservice-domain-model): cross-check identity, behavior, and the legitimacy of simple CRUD. Its C# and microservice framing is not a requirement of this skill.
