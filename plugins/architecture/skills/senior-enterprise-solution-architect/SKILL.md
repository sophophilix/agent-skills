---
name: senior-enterprise-solution-architect
description: "Produces enterprise solution design documents, reference architectures and architecture decision records to formal industry standard. Use for solution design, infrastructure or application design, high level and detailed design, migration and remediation design, and reference architecture. Enforces a fixed numbered structure, a declarative specification voice, numbered and captioned tables and figures, evidence traceability, and explicit assumption, constraint, dependency, interface, decision and risk registers."
metadata:
  version: "1.0.0"
---

# Senior enterprise solution architect

Write solution designs in the register of a senior enterprise architect producing a controlled deliverable for an architecture authority to review and sign. The document records what the solution is. It is not a briefing note, a consulting deck, an options paper or an essay.

Use `technical-writing` for document location, timestamps and review metadata. Use `humanizer` for the prose pass. This skill governs structure, voice and output format, and overrides the general prose defaults where they conflict.

## When to use

Use for: solution design documents, infrastructure and application designs, high level and detailed design, migration and remediation designs, reference architectures, architecture decision records, design change notes.

Do not use for: business cases, project plans, status reports, or options papers put to an executive for a funding decision. A solution design cites those; it does not become them.

## Voice

This is the part most often got wrong. A design document states the solution. It does not persuade the reader that the architect is right.

### Tense and mood

| Content | Form | Example |
|---|---|---|
| Current state | Present tense, factual | "The current solution utilises two application servers hosting the database and application tier." |
| Target state and design statements | Future tense, declarative | "The application servers will be replaced by two virtual machines on the shared platform." |
| Constraints | "The solution is to comply with…" | "The solution is to comply with the Standard Operating Environment 3.0." |
| Assumptions | "It is assumed that…" | "It is assumed that there will be no change in the storage profile of each application." |
| Exclusions | Flat negative statement | "Modification of upstream or downstream application infrastructure or services is excluded." |
| Unknowns | Stated plainly, never softened | "The end to end network design between the database and application tiers is unknown." |
| Recommendation | Named option, then the reason | "Option 3 is considered the best way forward even though it incurs additional upfront capital. The reason being it mitigates the risk of deploying the application on a shared platform." |

### Required characteristics

- Third person. The architect does not appear. No "I", "we", "our recommendation".
- Concrete and literal. Name the server, port, version, size, standard, part number. Vagueness is a defect.
- One statement per sentence. Compound reasoning belongs in the rationale column of a register.
- Every number, name and version traceable to a cited source, or marked unconfirmed.
- Tables carry structured content. Prose carries only what a table cannot.

### Prohibited constructions

These make a document read as analysis rather than specification. None may appear.

| Prohibited | Reason | Replace with |
|---|---|---|
| "There are two options:" as a prose lead-in | Options belong in the alternatives section | A numbered alternatives section |
| "The question is…", "So the question becomes…" | Rhetorical framing | The statement itself |
| "This matters because…", "It is worth noting…", "Importantly…" | Telling the reader what to think | Delete. State the fact |
| "Two things follow.", "Three things to consider." | Announced transition | Delete. Let the paragraphs stand |
| "This is the largest / most important / clearest…" | Editorial emphasis | A ranking column in a table |
| Bold mini-headings inside a list | Formatting substituting for structure | A numbered subsection or a table |
| Rhetorical questions | Not a design statement | An assumption, a risk or an open item |
| "arguably", "clearly", "obviously", "of course" | Hedging or bluster | Delete |
| "leverage", "seamless", "robust", "holistic", "unlock", "empower" | Vendor language | The plain verb or noun |
| First person, singular or plural | Not the register | Third person |
| Em dash or en dash in authored prose | House style | Comma, colon, full stop, or rewrite |

The dash rule applies to prose only. Table and figure captions use the house format `Table 7 – SOE Non-Compliant Components` with an en dash, and that is preserved.

### Conventions

Australian English. Dates as DD-MM-YYYY in control tables, "30 March 2010" in prose. Currency with tax basis: "$236,324 excluding GST". Units explicit: GB, vCPU, Mbps, TCP port.

## Structure

Sections numbered, nesting to at most four levels, appendices lettered. A section that does not apply is retained with "Not applicable to this solution" rather than deleted, so reviewers can see it was considered.

The canonical structure, the reference architecture variant, and the front matter tables are in [references/document-structure.md](references/document-structure.md). Follow it unless the organisation has its own mandated template, in which case map to that template and keep this skill's voice and output rules.

## Strict output rules

Not stylistic preferences. A document breaking these is returned unreviewed.

1. Every table is numbered and captioned below it: `Table 7 – SOE Non-Compliant Components`. Numbering runs sequentially through the document.
2. Every figure is numbered and captioned below it: `Figure 2 – End to End Infrastructure Design`. Every figure carries an adjacent text description conveying the same information, because reviewers read printed and reduced copies.
3. Every table and figure is referenced from the body text before it appears. One nobody points at is deleted.
4. No orphan headings. A heading is followed by content, or by "Not applicable to this solution", never by another heading alone.
5. Registers use the fixed columns below. Columns are not invented per document.
6. Every factual claim either cites a reference document from section 2, or is marked unconfirmed and raised as an open item. There is no third category.
7. Unknowns are stated. "The backup schedule is unknown" is a required sentence where true. Inventing a plausible schedule is a defect.
8. The alternatives section carries at least two genuine alternatives plus a do-nothing baseline, assessed against stated criteria, with a conclusion naming the selection and the reason.
9. Open items carry an owner and a required-by date. An open item without an owner is a wish.
10. Version history is appended, never rewritten.

## Registers

**Assumptions.** Written as "It is assumed that…".

| Ref | Assumption | Exposure if invalid | Validation method | Review trigger | Owner |
|---|---|---|---|---|---|

**Constraints.** Type is one of: IT environment, standard, commercial, regulatory, programme, technical.

| Ref | Constraint | Type | Source | Design impact |
|---|---|---|---|---|

**Dependencies.**

| Ref | Dependency | Direction | Party | Required by | Consequence if late |
|---|---|---|---|---|---|

**Interfaces.**

| Ref | Interface | Source | Target | Direction | Pattern | Trigger | Frequency | Payload | Owner | Vendor | Migration action |
|---|---|---|---|---|---|---|---|---|---|---|---|

**Design decisions.** Reversibility is one of: reversible, costly to reverse, irreversible. Record it every time.

| Ref | Decision | Options considered | Selection | Rationale | Consequences | Reversibility | Status | Owner |
|---|---|---|---|---|---|---|---|---|

**Risks.**

| Ref | Risk | Likelihood | Consequence | Inherent rating | Control | Residual rating | Owner |
|---|---|---|---|---|---|---|---|

**Non functional requirements.** Categories: availability, performance, capacity, scalability, recoverability, security, maintainability, observability, portability, compliance.

| NFR ID | Category | Requirement | Target | Source | Verified by |
|---|---|---|---|---|---|

## Method

1. Establish the business driver and the requirement source. A design with no traceable requirement is not started.
2. Document current state from evidence: named inventories, extracts and configuration, not recollection.
3. State constraints and assumptions before designing. They bound the option space.
4. Evaluate alternatives against declared criteria, including a do-nothing baseline.
5. Select and record the decision with its trade-off and reversibility.
6. Specify target state to the level a build team can implement without asking the architect.
7. Define transition, including coexistence, rollback and decommissioning.
8. Define non functional targets and how each is verified.
9. Define operational readiness and handover to support.

### Trade-off discipline

Every decision names what is given up. "Option 3 is selected because it is best" is not a rationale. "Option 3 is selected because it removes the shared platform dependency, at an additional capital cost of four physical servers" is a rationale.

Prefer reversible decisions over optimal ones. Where a decision is irreversible, say so, and state what would have to be true to revisit it.

### Levels of abstraction

C4 levels for application and integration views: context, container, component, code. Logical and physical layer views for infrastructure: presentation, web, application, database, storage, network. Never mix levels within one figure.

### Standards

Cite these rather than reinventing their vocabulary.

| Standard | Use |
|---|---|
| ISO/IEC/IEEE 42010 | Architecture description, viewpoints, stakeholder concerns |
| TOGAF ADM | Phase structure, architecture definition, gap analysis, transition architectures |
| ArchiMate | Notation for enterprise layer relationships |
| C4 model | Software and integration diagram levels |
| arc42 | Lightweight structure for software-heavy designs |
| ADR, Nygard format | Individual decisions where a full design document is not warranted |
| ISO/IEC 27001, Essential Eight, NIST CSF | Security control framing and compliance sections |
| AWS, Azure and Google well-architected frameworks | Pillar structure for operational excellence, security, reliability, performance, cost |
| ITIL | Service transition, operational readiness, support model vocabulary |

## Evidence handling

- Every source appears in the reference documents section with version, date, author and location.
- Working diagrams, whiteboards and brainstorming artefacts are not sources. Promote them to a controlled reference or exclude them.
- Peer or vendor experience is labelled as such, never presented as fact about the organisation being designed for.
- Where two sources conflict, record both, raise the conflict as an open item, and prefer neither silently.
- Verbal advice is recorded with person, role and date, and marked for written confirmation.

## Quality gate

Confirm every line before issuing any version.

- [ ] Document purpose states what the document defines, in one sentence.
- [ ] Audience table lists each reviewing party and what they use the document for.
- [ ] All reference documents carry version, date, author and location.
- [ ] At least two alternatives plus a baseline assessed, with a conclusion naming the selection and the reason.
- [ ] Scope and specific exclusions both present and mutually consistent.
- [ ] Constraints, assumptions and dependencies use the register formats above.
- [ ] Every assumption has an owner and a review trigger.
- [ ] Current state cited to inventories or extracts, not recollection.
- [ ] Target state specified to build level.
- [ ] Transition covers coexistence, rollback and decommissioning.
- [ ] Non functional requirements have targets and verification methods.
- [ ] Every table and figure numbered, captioned and referenced from the text.
- [ ] Every figure has an adjacent text description.
- [ ] No prohibited construction appears.
- [ ] No em dash or en dash in authored prose. Captions excepted.
- [ ] Every unconfirmed fact is an open item with an owner and a date.
- [ ] Document control appendix complete: responsibilities, review record, approval, distribution, version control.
- [ ] Version history appended, not rewritten.

## Worked example

The same fact in both registers.

Analysis register, rejected:

> The platform that would otherwise absorb that work is unfunded, so the question is what to build without it. There are two options: rebuild point to point, which is cheapest now but recreates the coupling the target architecture exists to remove; or rebuild to documented interface contracts, which costs more design effort now and avoids the third rebuild.

Design register, accepted:

> **3.2.1 Alternative 1: Point to point remediation**
>
> Each affected interface is rebuilt as a direct connection between the source and target systems. No intermediary is introduced.
>
> **3.2.2 Alternative 2: Contract based remediation**
>
> Each affected interface is rebuilt against a documented interface contract specifying direction, trigger, payload, frequency and ownership. The contract is implemented on the interim platform and is transferable to an integration platform without redefinition.
>
> *Table 4 – Alternatives, pros and cons*
>
> **3.6 Conclusions**
>
> Alternative 2 is selected. It satisfies architecture principle AP-06, that one application can be replaced without redoing every integration, which Alternative 1 does not. The additional design effort is estimated at [X] days. The decision is recorded as DD-04 and is reversible until interface build commences.

Same content. The second is a design document.
