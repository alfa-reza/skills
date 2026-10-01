---
name: unslop
description: Removes AI writing patterns from prose. Use when writing or editing text.
---

# Unslop

Remove AI writing patterns from prose. Preserve meaning and match the intended tone.

## Editing principles

- Treat these patterns as editing heuristics. They do not prove AI authorship.
- Make the smallest changes needed to improve the text. Leave good writing alone.
- Preserve the author's meaning, tone, intent, factual claims, and level of certainty. Flag unsupported claims or qualify them when necessary.
- Preserve established technical terminology, identifiers, commands, code, quotations, citations, and required document formats.
- Match the conventions of the target format, language, and audience. The example words and phrases below are English. In other languages, apply the underlying pattern and do not translate the word lists literally.
- Do not invent facts, sources, examples, measurements, personal experiences, or verification results.
- Do not introduce deliberate mistakes or awkward phrasing to imitate human writing.
- Fix the underlying problem, not only the recognizable vocabulary.

## Patterns to detect and fix

Rules are grouped by topic.

### Content

1. **Superficial -ing phrases.** Trailing phrases such as "highlighting...", "ensuring...", or "reflecting..." that add shallow analysis. Delete them, or expand only with sources already provided.
2. **Vague attributions.** "Experts believe", "Industry reports suggest", "Some critics argue". Name a source already provided. Otherwise flag the claim or delete it.
3. **Inflated significance.** Do not present routine changes as milestones, breakthroughs, fundamental shifts, or major advancements without evidence. State what changed and why it matters, if it matters.
4. **Promotional language.** Replace adjectives such as "seamless", "robust", "intuitive", or "cutting-edge" with specific behavior or evidence. If there is none, remove them.
5. **Unsupported assurances.** Do not claim correctness, compatibility, security, completeness, or successful testing without evidence. State what was verified and keep relevant uncertainty.
6. **Vague connections.** "Associated with", "connected to", "in connection with". State the actual relationship if the source gives it. Otherwise flag it.
7. **Source disclaimers.** Filler such as "based on available information" or "this does not by itself establish". State what is known. Keep a limitation that changes how the reader should use the claim.
8. **Borrowed importance.** Lists of media outlets or remarks such as "maintains an active social media presence" used to make a subject sound notable. Keep them only when coverage or presence is the topic.
9. **Suspect references.** Broken links, invalid DOIs or ISBNs, and citations that may not support the text. Check them if you have the means. Flag what you cannot confirm and never substitute a guessed reference.

### Language

10. **AI vocabulary.** Additionally, crucial, delve, enduring, enhance, foster, garner, interplay, intricate, landscape (abstract), multifaceted, pivotal, quietly, showcase, steady, tapestry (abstract), testament, underscore, vibrant. A single use may be fine. Look for clusters and replace them with plain words.
11. **Fancy ways to say "is".** "Serves as", "stands as", "boasts", "features". Say "is" or "has".
12. **Contrived contrasts.** Avoid formulas such as "not just X, but Y", "not X, but Y", "less X, more Y", "no X, no Y, just Z", and unnecessary "rather than" comparisons. State the point directly unless the contrast conveys a real distinction.
13. **Rule of three.** Do not force ideas into groups of three. Use the natural number.
14. **Synonym cycling.** Protagonist, main character, central figure, and hero all in one paragraph. Pick one and repeat it.
15. **False ranges.** "From X to Y" where X and Y are not on a meaningful scale. List the topics directly.

### Style

16. **Em dash overuse.** Avoid em dashes in ordinary prose. Use periods, commas, or a restructured sentence. Do not swap in another dash. Keep parentheses when they hold useful details, definitions, or qualifications.
17. **Colon overuse.** Colons are fine before a list or example. Avoid them as mid-sentence connectors. "The reason is simple: the cache expired" becomes "The cache expired."
18. **Boldface overuse.** Do not bold every proper noun or acronym.
19. **Inline-header lists.** Fix bold labels that repeat the line, as in "**Performance:** Performance improved...". A bold lead-in that names the item and is followed by new detail is fine.
20. **Title case headings.** Use sentence case unless the target style requires title case.
21. **Decorative emojis.** Remove them from headings and bullets.
22. **Curly quotes.** Use straight quotes in plain text, Markdown, and code-related docs. Keep curly quotes where the language or publication style calls for them, and inside exact quotations.
23. **Formulaic structure.** Do not force an introduction, background, benefits, challenges, future outlook, or conclusion onto every document. Use only the sections the content needs. Preserve required templates and repository conventions.

### Communication artifacts

24. **Chatbot phrases.** "I hope this helps!", "Let me know if...", "Of course!", "Certainly!" Remove them from documents. Keep a normal courtesy line in correspondence when it serves the reader.
25. **Sycophantic tone.** "Great question! You're absolutely right!" Respond directly.
26. **Generation leftovers.** Unfilled placeholders such as "[Your Name]" or "2025-XX-XX", tracking parameters such as "utm_source=chatgpt.com", internal citation markers, and Markdown in a format that does not render it. Remove the debris. Flag placeholders that need real values. Keep placeholders in templates.

### Filler

27. **Excessive hedging.** "Could potentially possibly be argued that it might" becomes "may".
28. **Generic conclusions.** "The future looks bright." State specific plans or facts, or cut the sentence.
29. **Repeated information.** Delete sentences or sections that restate an earlier point without adding facts, reasoning, or instructions. Do not summarize a short passage right after writing it.
30. **Unnecessary signposting.** Remove sentences that announce, preview, or transition to a point without adding information. Keep transitions that express a real logical connection.

### Plain speech

31. **Say what it does, not how it feels.** "The database stays close at hand" becomes the mechanism or a number. "We are deeply committed to timely replies" becomes "We reply within two days", if the source says so. Ask what the sentence tells the reader to do or know, then write that. Cut it if it says neither. Keep standard instructions and established technical descriptions.
32. **Dense sentences.** If the reader has to backtrack, split the sentence or drop clauses. Vary sentence length. Do not chop sentences that already read well.
33. **Active voice.** Prefer it. "Queries are validated" becomes "The compiler validates queries." Passive is fine when the actor is unknown or does not matter.
34. **Adverbs.** An adverb often signals a weak verb. "Runs quickly" becomes "is fast" or the measured time. "Significantly improves" becomes the measured change. Use numbers only if the source provides them.
35. **Plain words.** "Utilize" and "leverage" become "use", "facilitate" becomes "help", "numerous" becomes "many", "in order to" becomes "to", "due to the fact that" becomes "because", and "in the event that" becomes "if". Delete "it is important to note that".
36. **Mannered prose and metaphor nouns.** Replace aphorisms, rhetorical fragments, personified code, figurative verbs, and stock framing with literal wording. "A dial worth turning" becomes "a parameter worth varying". "At the end of the day" can usually go. Watch for nouns such as substrate, wedge, vector, locus, vantage, nexus, primitive, harness, surface, bedrock, scaffolding, modality, paradigm, ratchet, endgame, north star, and flywheel. Keep them when they have a precise technical meaning in context.
37. **Over-compression.** Do not drop articles or verbs, or use arrows and unexplained abbreviations, where the reader has to decode them. "Parser rejects bad date → exit 2, no write" becomes "The parser rejects a bad date, exits with code 2, and writes nothing." Fragments are fine in commit messages, issue titles, changelogs, UI labels, and similar formats.
38. **Unnecessary nominalization.** "Perform validation of the input" becomes "Validate the input". Keep established technical terms when they are more precise.
