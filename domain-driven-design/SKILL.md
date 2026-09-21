---
name: domain-driven-design
description: Design or reassess a domain model using top-down Domain-Driven Design for a greenfield or existing system. Use for domain discovery, strategic boundaries, and invariant-driven tactical modeling; substantive work requires sequential Codex subagents and an adversarial skeptic. Not a pipeline for terminology questions or isolated implementation edits.
---

# Domain-Driven Design

Act as the **overlord/coordinator**: build an evidence-backed model of the business, delegate narrow investigations, and reconcile their conclusions. Optimize for useful business semantics and the simplest sufficient design.

> A named DDD pattern is not a goal. Use it only when it solves a demonstrated domain-modeling problem.

Valid conclusions include: CRUD is sufficient; no aggregate beyond a simple entity is needed; no domain service, event, or repository abstraction is needed. A bounded context need not be a separate deployable service. DDD does not require CQRS, event sourcing, microservices, a particular programming paradigm, framework, or folder structure.

## Runtime delegation

**For substantive DDD design or reassessment, actually spawn a fresh Codex subagent for each analysis phase (1–15) below.** The parent owns phase 16. Writing multiple role headings in one thread is not delegation. A terminology answer or isolated edit does not require this pipeline.

Use the runtime subagent tools exposed by the current Codex session to spawn, wait for results, send narrowly scoped follow-ups, and close completed threads when supported. Use ordinary subagents with phase-specific prompts; no installed custom roles or fixed model overrides are required. Respect available capacity and inherited permissions. If delegation is unavailable or fails, report incomplete coverage and the precise limitation; do not silently replace the required workflow with a single-agent design or change the user's configuration.

The phase instructions in this skill and its references are **skill resources**, not custom agent definitions. Project-scoped `.codex/agents/*.toml` files configure reusable agents; they are unnecessary here. A skill's optional `agents/openai.yaml` is UI/invocation metadata, not a subagent role file. Do not create any of these files to run this skill.

## Parent preparation and handoff

1. Preserve the original request, subsequent clarifications, scope, and applicable project instructions. Determine whether the deliverable is analysis, a design, or implementation; analysis alone does not authorize a rewrite.
2. Inspect available requirements, documentation, code, tests, APIs, and integration contracts. Trace representative behavior before proposing changes. In an existing system, keep **observed current model**, **inferred domain model**, and **proposed changes** separate throughout. In greenfield work, identify known external systems and label unbuilt internal boundaries as proposals.
3. Maintain a compact evolving model with decision IDs, evidence, assumptions, dependencies on earlier decisions, and unresolved questions. Accepting a hypothesis for exploration does not turn it into a domain fact. Reuse existing decisions only after checking their evidence and applicability.

Give **every phase**, including the skeptic:

```text
PHASE: number, narrow job, expected output
ORIGINAL REQUEST: original user request plus subsequent clarifications and scope
EVIDENCE: relevant source excerpts and accessible paths/lines or URLs/sections;
          distinguish observed behavior, stakeholder statements, and hypotheses
ACCEPTED MODEL: conclusions from ALL prior phases, including justified omissions,
                decision IDs, evidence, and dependency links
OPEN QUESTIONS: all unresolved questions, conflicts, and assumptions from earlier phases
INSTRUCTIONS: applicable project constraints and this phase's reference section
```

Resolve reference paths relative to this skill before handing them to a child; do not assume the working directory is the skill directory. Give enough raw evidence to challenge the summaries. If a child cannot access a source, supply its relevant content or mark the claim unverifiable.

Tell each child: investigate only the assigned phase; read its reference section; return analysis without editing project files; do not launch the whole pipeline. Independent research within that phase may be delegated when useful, but the parent must collect it before advancing. Children must not silently change accepted upstream decisions.

Require this result contract:

```text
PHASE RESULT
Evidence inspected: source locations and concrete observations
Proposed conclusions: domain meaning, evidence, upstream decision dependencies
Alternatives / omissions: simpler option considered; why any abstraction is needed
Open questions: missing evidence, assumptions, and whether they block dependent work
Upstream conflicts: structured conflict blocks, or none
```

After each result, the parent verifies the evidence, merges compatible conclusions, identifies contradictions, and records what is accepted, rejected, or unresolved with reasons. Request missing evidence when needed. **Wait for this acceptance step before spawning the next dependent phase.** A failed or unfinished child is incomplete work, not an accepted phase. Parallelism is permitted only for genuinely independent research inside a phase, never to race strategic and tactical decisions.

## Dependency-ordered phases

Read [strategic-design.md](references/strategic-design.md) for phases 1–5 and [tactical-design.md](references/tactical-design.md) for phases 6–14. Pass only the relevant section to each child. Read the skeptic instructions below for phase 15. Every phase may return a justified “not needed” conclusion rather than fabricate a pattern.

| Phase | Fresh subagent's job and required contribution |
|-------|-----------------------------------------------|
| 1. Domain / problem-space discovery | Goals, actors, outcomes, workflows, decisions, evidence gaps, and domain complexity. No tactical architecture. |
| 2. Initial ubiquitous language | Ground terms and behavior in actual usage; retain differing meanings with provisional scopes. Refine this language in every later phase. |
| 3. Subdomain analysis | Identify cohesive business responsibilities; justify Core, Supporting, or Generic classification and investment. |
| 4. Bounded contexts | Identify internally consistent models, language, ownership, and lifecycle boundaries. Relate them to subdomains without assuming one-to-one mapping. |
| 5. Context map and language reconciliation | Map actual relationships, translations, and influence; qualify supported context-map patterns. Reconcile terms within each context and distinguish proposed changes. |
| 6. Use cases / commands / queries | Express actor intentions, outcomes, failures, and information needs in the scoped language. |
| 7. Business rules and invariants | Establish transitions, pre/postconditions, temporal and concurrency rules, and required consistency before choosing boundaries. |
| 8. Aggregates / consistency boundaries | Assign true invariants to the smallest sufficient transactional boundaries; examine cross-boundary delay and failure. |
| 9. Entities and value objects | Refine identity, equality, lifecycle, state, and meaningful value semantics within the accepted boundaries. |
| 10. Domain policies / services | Place behavior with its natural domain owner; justify any standalone domain operation. |
| 11. Domain events | Formalize meaningful facts, causes, and consumers; distinguish domain meaning from integration delivery. |
| 12. Repositories | Define necessary aggregate-root access in domain terms, preserving encapsulation. |
| 13. Application orchestration | Trace complete use cases through domain operations, authorization, transactions, and external effects. |
| 14. Infrastructure / adapters | Fit persistence, transport, and frameworks to the model and existing architecture; expose feasibility conflicts. |
| 15. DDD skeptic | Independently attempt to disprove the complete model using evidence and counterexamples. |
| 16. Overlord reconciliation | Parent resolves every significant challenge, revisits affected phases, and produces the final model. |

**Gate tactical modeling:** Do not start phase 6 until discovery and strategic conclusions (1–5) are accepted for the scoped work. Do not select aggregates until phase 7's rules and consistency needs are accepted. Material unresolved business questions block conclusions that depend on them; ask the user/domain expert for those facts and continue only independent work. Clearly labeled, nonblocking assumptions may remain provisional.

This is a dependency order, not a claim that DDD prescribes a waterfall. Evans starts model exploration and language together; language cannot wait until the context map is finished. Phase 2 establishes a working vocabulary, phases 4–5 scope and reconcile it, and every later phase tests it. Discover candidate business events and identity distinctions early when they explain workflows; defer their tactical representation to phases 8–11. Inspect existing infrastructure as evidence in phase 1, but choose new infrastructure mappings in phase 14.

## Upstream revision

Later evidence can invalidate earlier choices. Require the discovering agent to return:

```text
UPSTREAM CONFLICT
Affected decision: <ID and earlier conclusion>
New evidence: <source and observation>
Problem: <why the earlier conclusion is invalid or weaker>
Suggested change: <smallest justified revision>
```

The parent decides whether to retain, revise, or leave the decision unresolved, citing evidence. For a revision, mark all transitively dependent conclusions stale, send the changed evidence to a fresh agent for the earliest affected phase, and revisit affected downstream phases in order. Update language and the context map as needed; retain unaffected decisions. Run the skeptic again over the revised model and affected scenarios before finalizing. Do not hide uncertainty by endlessly cycling or declare completion with blocking contradictions.

## Adversarial DDD skeptic

Launch a fresh agent after phase 14. Supply the complete accepted model, source access, assumptions, rejected alternatives, and both references. Its task is to **disprove design claims**, not summarize or approve the design. It may discover missing rules or new contradictions, not just filter existing concerns.

Require it to reopen evidence and try concrete counterexamples: conflicting meanings, invalid state transitions, simultaneous commands, delayed or duplicate delivery, and a simpler design. Challenge:

- Are subdomains business capabilities, or merely technical layers? Were they conflated with bounded contexts? Are contexts real model/language boundaries?
- Does the context map describe current reality, including ownership and influence, or present aspirations as facts? Is each context's language grounded in actual usage?
- Did behavioral use cases collapse into CRUD and lose rules? Conversely, would honest CRUD preserve all needed semantics more simply?
- Are invariants, temporal rules, and concurrency scenarios missing? Were aggregate boundaries derived from consistency needs rather than storage or object graphs?
- Are aggregates too large or too fragmented? Was eventual consistency rejected without a business reason, or imposed despite a hard invariant?
- Do entities need domain identity? Do value objects encode useful meaning or invariants? Are services stealing natural entity, value, or aggregate behavior?
- Are events meaningful domain facts? Are repositories aggregate-oriented collections or DAOs with DDD names? Has infrastructure leaked into domain decisions?
- Was any pattern introduced only because DDD names it? Could a substantially simpler model preserve the same business semantics?

For each significant challenge, return: affected decision IDs; evidence or exact evidence gap; attempted counterexample and outcome; business/maintenance consequence; smallest proposed correction; confidence distinct from impact. Report unresolved questions explicitly. No quota of findings or rejections; no findings is valid after a real disproof attempt.

## Final reconciliation and delivery

The skeptic does not automatically win. The parent evaluates **each significant challenge** as accepted, refuted, or unresolved, with evidence and a reason. A supported challenge triggers the revision process; disagreement alone is not refutation. Never suppress an unresolved contradiction or incomplete phase.

Deliver the accepted model at the requested depth, keeping current observations, inferred business meaning, and proposed changes distinct. Include:

- Domain goals, scoped language, subdomains, contexts, and the current context map; show a proposed map separately if needed.
- Representative use cases traced through rules, consistency boundaries, domain behavior, and application/infrastructure responsibilities. Include only justified tactical patterns and explain material omissions.
- Decisions and tradeoffs, skeptic dispositions, unresolved questions, incomplete coverage, and the smallest next evidence check for each blocker.
- For existing systems, incremental changes and translation seams with compatibility risks; for greenfield work, explicit assumptions needing domain validation.

Use the user's requested artifact location, or summarize in the conversation when none is requested. Do not generate a report tree or implementation scaffolding merely to record phases. If implementation was requested, continue within that scope after reconciliation and validate the relevant rules and boundaries. Stop when the requested outcome is met, not when every DDD pattern has an implementation.

## Codex basis

- [OpenAI code-review orchestration skill](https://github.com/openai/codex/blob/main/.codex/skills/code-review/SKILL.md): a `SKILL.md` can require subagent delegation and parent aggregation.
- [Current Codex subagents](https://developers.openai.com/codex/subagents): runtime delegation, narrow prompts, inherited controls, and optional custom TOML agents.
- [Codex skills](https://developers.openai.com/codex/skills) and [OpenAI skill-creator](https://github.com/openai/skills/blob/main/skills/.system/skill-creator/SKILL.md): instruction-first skills with progressive disclosure; optional resources only when useful.
