---
name: technical-writing
description: "Creates, edits, and reviews technical documentation as a chief technical writer. Use automatically for Markdown, ADRs, design docs, guides, audits, reports, blog posts, READMEs, release notes, and other project prose. Centralizes document location, UTC timestamps, review metadata, accessibility, source handling, the humanizer pass, and short-sentence rules for procedures and warnings."
metadata:
  version: "1.1.0"
---

# Technical writing

Use this skill without waiting for the user to repeat "use humanizer," "write as a chief technical writer," "put it under docs," or "timestamp it."

This skill is the central source of truth for project documentation. Other skills should point here instead of copying these rules.

## Before writing

1. Load the `humanizer` skill and apply it to authored prose. Do not apply prose rules to code, commands, identifiers, URLs, data, or quoted source text.
2. Read the repository's instructions and existing documentation structure.
3. Identify the audience, the task they need to complete, and the decision or outcome the document supports.
4. Classify the document as a tutorial, how-to guide, reference, explanation, decision record, review, or report. Do not mix forms unless the reader needs the combination.
5. For factual research, prefer primary sources. Record the source URL and access time. Separate sourced facts from team decisions and recommendations.

## Location and names

Create standalone project documentation under the repository's `docs/` directory by default.

Use an existing docs convention when one is present. Otherwise use these locations:

- `docs/tutorials/` for learning-oriented walkthroughs
- `docs/how-to/` for task-oriented procedures
- `docs/reference/` for factual lookup material
- `docs/explanation/` for concepts and rationale
- `docs/decisions/` for ADRs
- `docs/reviews/` for audits and review records
- `docs/blogs/` for dated engineering articles

Use lowercase kebab-case file names. Add a compact UTC timestamp to review snapshots, audit snapshots, and blog files: `topic-YYYYMMDDTHHMMSSZ.md`. Keep durable guides, references, and ADR links stable, and put their timestamp in the document control block instead.

Root-level `README.md`, `CONTRIBUTING.md`, `CHANGELOG.md`, `LICENSE`, and files required by a tool or the user are valid exceptions. Do not move an existing document unless the task requires it.

## Time and document control

Get the current time from the environment, for example:

```bash
date -u '+%Y-%m-%dT%H:%M:%SZ'
```

Never guess a timestamp. Use ISO 8601 UTC in the document body.

Place this block directly below the title for a new reviewable document:

```markdown
> Status: Draft | In review | Approved | Superseded
> Created: YYYY-MM-DDTHH:MM:SSZ
> Last reviewed: YYYY-MM-DDTHH:MM:SSZ
> Next review: YYYY-MM-DD or Not scheduled
```

Do not invent an owner, reviewer, approval, or next-review date. Add those fields only when the repository or user supplies them. Preserve `Created` when editing and update `Last reviewed` only after a real review.

For an established document with its own metadata format, update that format instead of adding a competing block.

## Writing standard

Write as the accountable technical editor, not as a promoter.

- Lead with the reader's goal, decision, or result.
- Use active voice, concrete nouns, and second person in procedures.
- Use sentence-case, descriptive headings with a valid hierarchy.
- Keep paragraphs short and list nesting to two levels where practical.
- Use numbered lists for sequences and bullets for unordered choices.
- Define uncommon abbreviations on first use.
- Give commands enough context to run safely. Verify them when the environment permits.
- Use descriptive link text. Add alt text or a nearby text explanation for meaningful images.
- State assumptions, limitations, risks, and unresolved decisions directly.
- Describe current behavior. Discuss prior behavior only in migrations, release notes, decision history, or retrospectives.
- Preserve facts and citations. Never fill a research gap with a plausible claim.

## Procedures, warnings, and fix steps

These rules adapt the sentence rules of ASD-STE100 Simplified Technical English (STE). They don't make a document STE-compliant, because STE also requires its controlled dictionary. Don't claim compliance.

Apply them in full to how-to guides, runbooks, tutorials' steps, warnings, cautions, and the fix or remediation steps in findings. Apply them where practical to other documents. ADRs, explanations, and reviews keep the reasoning behind a decision: a "because" or "unless" clause may stay in the sentence it explains.

1. Write one instruction per numbered step. Start it with an imperative verb. Use 20 words or fewer.
2. Keep descriptive sentences to 25 words or fewer.
3. Give each sentence one idea. Don't use semicolons in authored prose.
4. Use the simple present tense. Use the future tense only for an event that hasn't happened yet.
5. Use one term for one thing. When the project has a glossary, use its terms. Otherwise, choose a term on first use and keep it. Don't swap in synonyms for variety.
6. Replace idioms, metaphors, and vague phrasal verbs with a plain verb that has one meaning: "start" instead of "kick off", "remove" instead of "get rid of".
7. Put a warning or caution before the step it applies to, not after it.

Code, commands, paths, identifiers, URLs, and quoted source text don't count toward a sentence's length, and these rules never change them.

To check a draft, run `python3 scripts/ste-lint.py <file>` from this skill's folder. Add `--json` for structured output. Use `--baseline <n>` on an existing document to tolerate its current count while you fix it. The linter is advisory. It never flags hedges, so keep every "may" and "could" that the source states. See `THIRD_PARTY.md` in the skills repository for its source and review status.

## Reviewable document format

A draft that asks for a decision must tell the reviewer what to do. Adapt this structure rather than adding empty sections:

```markdown
# [Specific title]

> Status: In review
> Created: [UTC timestamp]
> Last reviewed: [UTC timestamp]
> Next review: [date or Not scheduled]

## Review guide

Decision requested: [one sentence]

Check these points:

- [ ] [accuracy or scope question]
- [ ] [risk or trade-off question]
- [ ] [ownership or follow-up question]

Respond with one of:

- `approve`
- `approve with follow-up: <action>`
- `request changes: <specific issue>`

## Summary

[What the reader needs to know first.]

## Evidence or context

[Verified facts, constraints, and linked primary sources.]

## Decision or recommendation

[The proposed choice and why it fits.]

## Risks and open items

[Named owner and date only when known.]

## Review history

| Reviewed at | Decision | Notes |
|---|---|---|
| [UTC timestamp] | Drafted | Initial version. |
```

Append to review history. Do not rewrite prior entries except to correct an explicit factual error, and note that correction.

## Final checks

Before saving:

1. Confirm the file is under `docs/` or qualifies for a named exception.
2. Confirm timestamps came from the environment and use UTC.
3. Check heading order, paragraph length, list depth, link text, tables, and image alternatives.
4. Verify commands, code examples, dates, links, and factual claims where practical.
5. Run the humanizer self-check. Remove AI filler without changing technical meaning.
6. Search authored prose for em and en dashes. Preserve them only where the humanizer rules allow, such as quoted source text.
7. In procedures, warnings, and fix steps, check step and sentence length, semicolons, and term consistency against "Procedures, warnings, and fix steps."
8. Check the diff for accidental changes to code blocks, metadata, identifiers, URLs, and citations.

## Completion response

When file mode applies, save only the finished document. Then report:

- the file path
- the UTC timestamp used
- the decision or next review action, if the document is in review
- any verification that could not be completed
