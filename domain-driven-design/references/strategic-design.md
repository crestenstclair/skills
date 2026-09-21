# Strategic phase contracts

Read the section for the assigned phase. Each output also follows the handoff/result contract in [SKILL.md](../SKILL.md). The phases establish dependencies; feedback can reopen earlier decisions.

## 1. Domain / problem-space discovery

Investigate the business problem before architecture: desired outcomes, actors, incentives, workflows, decisions, exceptions, and language. Trace representative success and failure scenarios. Separate business complexity from incidental framework, storage, deployment, and serialization details.

For existing systems, inspect actual code paths, tests, documentation, APIs, and integrations. Treat disagreement between them as evidence to investigate, not permission to choose whichever version suits the design. Existing behavior is evidence of the current model, not proof of the intended business rule. For greenfield systems, use the brief and stakeholder examples; list facts that only a domain expert can supply. Never invent an expert interview or business policy.

Return a concise domain narrative, actor/outcome/workflow map, source-backed terminology, and unresolved questions. Capture important past occurrences and identity distinctions in business language without selecting aggregates, services, or event infrastructure. Assess where rich modeling might pay off and where ordinary data maintenance may suffice.

## 2. Initial ubiquitous language

Build a working language from users, domain experts, workflows, documents, tests, APIs, and code. Describe behavior as well as nouns. Use examples to reveal meaning; a list of class names is insufficient.

For each important term, record meaning, example/non-example, source, and the actor/workflow where it applies. Preserve overloaded meanings rather than forcing a single enterprise definition. These scopes are provisional until bounded contexts are established; do not call an unvalidated glossary a shared language already adopted by experts.

Return the working vocabulary and specific ambiguities. Refine it in every later phase and reconcile it per bounded context in phases 4–5. Keep good domain language; do not rename a business concept to “Aggregate,” “Manager,” or other technical jargon.

## 3. Subdomain analysis

Group cohesive business responsibilities in the problem space, using goals, expertise, decisions, and workflows. Technical layers such as controllers or persistence are not subdomains merely because they are separate packages.

Classify provisionally, with business evidence:

- **Core Domain:** the specialized capability central to the system's value and differentiation; focus modeling investment here.
- **Supporting Subdomain:** necessary business-specific work that supports the core without providing that differentiation.
- **Generic Subdomain:** a broadly understood capability for which established models or solutions may suffice.

Return each responsibility, classification rationale, relative complexity, and implications for effort or reuse. Classification depends on this organization and may change; a large codebase or technically difficult component is not automatically core. Do not force all three categories to exist. Evans supplies the Core Domain and Generic Subdomains patterns; “Supporting” names the remaining business-specific support work here, not a separate pattern from his Reference.

## 4. Bounded contexts

Identify where a model and its language can remain internally consistent. Look for the same word with different meanings, different rules or lifecycles, incompatible representations, ownership, team practices, and integration seams. Test candidate boundaries against actual scenarios and changes.

Return each context's purpose, model/language, included and excluded responsibilities, ownership evidence, and boundary rationale. Bind provisional vocabulary to these contexts. Relate contexts to subdomains explicitly: subdomains divide business responsibility; bounded contexts delimit the applicability of models. Their mapping need not be one-to-one, especially in existing systems.

Do not infer a context solely from a table, API, folder, team, or deployable. Each can provide evidence but is not itself proof of semantic consistency. If existing models are mixed, record that honestly rather than invent clean current boundaries. Multiple contexts can live in one process; deployment is a later choice.

## 5. Context map and language reconciliation

Map the existing terrain first, including external systems and implicit legacy models. Record for each connection: contexts involved; participating owners; what crosses the boundary; translation or shared meaning; influence on change; and evidence. **Upstream/downstream describes influence and dependency, not merely message direction.** Unknown relationships remain unknown.

Describe the real relationship before applying vocabulary:

| Evans pattern | Evidence that warrants the name |
|---------------|--------------------------------|
| Partnership | The contexts' delivery success is mutually dependent and the teams coordinate planning and integration. |
| Shared Kernel | Teams explicitly share a small part of the model and associated implementation, with joint change and integration discipline. A shared utility library alone is insufficient. |
| Customer/Supplier Development | Downstream needs are negotiated into upstream planning; merely consuming an API does not establish this relationship. |
| Conformist | The downstream adopts the upstream model rather than translating it, with little influence over upstream priorities. |
| Anticorruption Layer | A downstream translation boundary protects its model from an incompatible external model. Ordinary serialization alone is insufficient. |
| Open-host Service | An upstream offers a defined common access protocol for multiple consumers rather than tailoring every integration. |
| Published Language | A documented shared interchange language conveys domain information across contexts; a wire format alone is not enough. |
| Separate Ways | Integration is deliberately absent because its value does not justify the cost. |
| Big Ball of Mud | Existing models and boundaries are entangled; mark and contain the mixed region instead of claiming precise internal contexts. |

These labels describe different aspects and can coexist, such as Open-host Service with Published Language. Do not force exactly one pattern onto every edge or use the vocabulary as a target architecture checklist.

Return the current map, remaining unknowns, and reconciled language within each context, preserving legitimate differences across contexts. For greenfield work, show known external reality and mark internal relationships as proposed. Put desired future relationships and incremental translation seams in a separate proposal. Walk a representative workflow across the map to test semantic handoffs before tactical work begins.

## Source grounding and ordering

The Evans-derived summaries above are adapted and condensed from Eric Evans, *Domain-Driven Design Reference: Definitions and Pattern Summaries* (2015), © Eric Evans, licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). The phase contracts and operating guidance are adaptations, not quotations or an Evans-prescribed process.

- [DDD Reference page](https://www.domainlanguage.com/ddd/reference/) and [PDF](https://www.domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf): definitions (printed p. vi), model/language (pp. 1–4), context mapping (pp. 28–38), and distillation (pp. 39–45). The context-map instruction is to map existing terrain before transformations.
- [Evans' DDD Immersion](https://www.domainlanguage.com/training/ddd-immersion/): model exploration and ubiquitous language precede strategic design and implementation concerns. [DDD Overview](https://www.domainlanguage.com/training/ddd-overview/) also starts with discovery/language, but introduces aggregates before the strategic afternoon; neither course establishes a mandatory phase order.
- [Fowler: Bounded Context](https://martinfowler.com/bliki/BoundedContext.html): internally consistent models, differing meanings of common words, and explicit interrelationships.
- [Library example: discovery](https://github.com/ddd-by-examples/library/blob/master/docs/big-picture.md) and [example mapping](https://github.com/ddd-by-examples/library/blob/master/docs/example-mapping.md): workflows reveal that “book” means different things in lending and catalogue; examples deepen the initial exploration. Borrow the reasoning, not its Java packages, frameworks, or architectural choices.

The skill therefore starts language work early, scopes it as contexts emerge, and uses subdomains to focus effort before detailed boundary design. Language, context mapping, and Core Domain distillation must inform each other on subsequent passes.
