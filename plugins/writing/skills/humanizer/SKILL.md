---
name: humanizer
description: |
  Rewrite AI-sounding text so it reads naturally without changing what it says.
  Use when editing or reviewing prose for inflated claims, sales language,
  vague sources, repetitive structure, stock AI words, passive voice, filler,
  accessibility problems, or chatbot artifacts. Based on Wikipedia's
  "Signs of AI writing."
license: MIT
metadata:
  version: "3.0.0"
---

# Humanizer

Rewrite AI-sounding text so it reads like the writer, not a chatbot. Preserve meaning and do not invent details.

## Required method

1. Identify patterns that make the prose sound generated, vague, inflated, or repetitive.
2. Keep every claim. You may shorten dull parts, expand useful parts, and reorganize paragraphs, but do not lose information.
3. Do not add a fact, name, number, date, quote, citation, ranking, or source. Ask for a missing detail or write a simpler sentence.
4. Match the writer's voice and the document's purpose.
5. Check accessibility for technical and factual prose.
6. Return the result required by the input mode.

Use [the detailed pattern catalog](references/patterns.md) when a draft contains a suspected pattern, the user requests an analysis, or this checklist is not enough to make a confident edit.

## Core pattern checklist

Look for combinations of these patterns, not one word in isolation:

- inflated importance, legacy claims, name-dropping, or sales language
- vague experts, reports, critics, or unsupported outlook sections
- shallow analysis added through repeated `-ing` phrases
- stock AI words, elaborate verbs where `is` or `has` is clearer, and fake depth
- forced groups of three, false ranges, synonym cycling, and repeated openings
- passive voice that hides the actor
- excessive bold text, bold mini-headings, title-case headings, or decorative emojis
- chatbot greetings, offers, agreement, disclaimers, and guessed gap filling
- filler, stacked qualifiers, generic optimism, and dramatic fragments
- announced transitions, headings repeated in the first sentence, and fake objections
- documentation that narrates an old draft instead of current behavior

Do not flatten deliberate rhythm, technical precision, real alternatives, useful warnings, or the writer's quirks merely because they resemble one item.

## Match the writer

If the user provides their own writing sample, read it first. Note sentence length, word choice, paragraph openings, punctuation, repeated phrases, and transitions. Match those habits without replacing casual words with formal ones or removing deliberate quirks.

A writing sample takes priority over default style rules. If it uses em dashes, keep them at about the same rate.

Without a sample, match the document type. Blogs, essays, and opinions may retain personality, uncertainty, humor, and uneven rhythm. Technical, legal, reference, and factual text should remain neutral. Never invent a personal reaction or factual anecdote.

## Accessibility pass

For technical and factual prose:

- use short paragraphs and put the important point first
- use concrete, sentence-case headings in a valid hierarchy
- keep list nesting to two levels where practical
- use descriptive link text
- define uncommon abbreviations on first use
- keep a procedure's sequence and a comparison's necessary structure

Accessibility edits must not change technical meaning or remove required detail.

## Punctuation and protected text

Authored prose in the final rewrite must not contain em dashes (`—`) or en dashes (`–`) unless the writer's sample uses them. Replace them with punctuation that fits the sentence or rewrite the sentence.

Do not alter code, commands, CLI flags, file names, identifiers, URLs, data, or copied and quoted source text merely to remove a dash character. Preserve those values exactly unless the user asks to change them.

Use straight quotation marks when that matches the target format. Do not rewrite quotation contents to match the surrounding voice.

## Factual integrity check

Before finalizing, ask:

1. What still sounds AI-generated?
2. Did the rewrite add or remove any claim, fact, name, number, date, quote, citation, ranking, limitation, or decision?
3. Did an edit change a command, identifier, URL, code block, metadata field, or quotation?

Treat an unsupported addition, lost claim, or protected-text change as an error. Recheck the source and fix it.

## Output modes

### Pasted text

Return:

1. a draft
2. a short list of patterns that remain or need the writer's decision
3. the final rewrite

### File mode

When the user names a file, run the full method but write only the final prose to the file. Preserve code blocks, YAML metadata, data, link targets, identifiers, and quoted source text. Then give a short completion summary.

### Embedded mode

When another skill or task invokes humanizer for a document, pull request, commit message, or other deliverable, return only the final text in the format that task requires.

## Final workflow

1. Read the complete source and identify its purpose and voice.
2. Draft by paragraph rather than replacing one watched word at a time.
3. Read for rhythm, clear actors, simple verbs, and useful detail.
4. Run the factual integrity and accessibility checks.
5. Apply the punctuation rule only to authored prose.
6. Return or save the correct output mode.

## Source

This skill is based on [Wikipedia's "Signs of AI writing"](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing), maintained by WikiProject AI Cleanup. The reference catalog preserves the detailed patterns and examples used for this workflow.
