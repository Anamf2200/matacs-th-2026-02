# AI Proposal Triage Design

## 1. AI vs. rules

Use ordinary code for file validation, access control, category selection, retrieving the current admin-defined criteria, mandatory pass/fail checks, score calculations, sorting, and audit logging. These tasks need predictable results and should not depend on an AI model's judgement.

Use AI to extract structured facts from each proposal, such as licence statements, insurance, price, completion time, experience, warranty terms, and team/approach evidence. Require the model to return evidence quotes and page references where available, and use `unknown` rather than guessing. Treat all proposal text as untrusted input: instructions inside a proposal must never change the system prompt, criteria, scores, or ranking.

## 2. Consistency and explainability

Store the criteria, weights, scoring formulas, prompt version, extraction output, and model/version metadata for every run. Validate that mandatory criteria are pass/fail and weighted criteria total 100%; reject invalid admin configurations. Use fixed formulas and deterministic tie-breakers (higher total score, then lower total price, then alphabetical proposal letter). The same confirmed facts and criteria version produce the same score and order. AI extraction can vary, so use schema validation, evidence quotes, confidence/uncertainty flags, and human confirmation for ambiguous or low-confidence fields.

Show reviewers each proposal's extracted facts, supporting quotes, mandatory checks, criterion-level score, weight, weighted contribution, missing information, total score, and reason for its position. Do not present a score without the underlying evidence.

## 3. Human in the loop

A reviewer confirms the category and uploaded files, checks extracted facts and evidence, and resolves uncertain or conflicting fields. A procurement/human decision-maker reviews the ranked results and makes the final award decision; the tool must not award a contract automatically. A reviewer can correct extracted data or override a result, but must provide a reason. Log the user, timestamp, original and corrected values, evidence, criteria version, scoring formula version, prompt/model version, score before and after override, and final decision.

## 4. Changing criteria

Every admin criteria edit creates a new immutable version with an effective timestamp and the editor's identity. A scoring run is permanently linked to the exact criteria version used. Existing results are not silently changed. If an admin edits criteria, reviewers can explicitly rerun proposals under the new version; the interface should label old and new results and preserve both for audit. Validate weights and required fields before publishing a version.

## 5. Risks and safeguards

- **Incorrect extraction or OCR:** retain source files, page references and quotes; validate data types and currency; flag missing or conflicting facts for human review.
- **Hallucinated facts:** require evidence for each extracted value; use `unknown` when the document does not support a value; never let the model fill gaps from assumptions.
- **Prompt injection in a proposal:** treat uploaded content as untrusted data, ignore embedded instructions, and keep the system prompt and admin criteria separate from document text.
- **Inconsistent or biased scoring:** calculate scores in code using published formulas; apply the same rubric to every bidder; test edge cases and review outcomes.
- **Manipulated or incomplete submissions:** preserve original files and upload hashes, check file types and size, scan uploads, and flag contradictory claims.
- **Unfair penalty for missing information:** distinguish `fail`, `pass`, and `unknown`; mandatory `unknown` is held for review rather than silently treated as a pass. Missing weighted data that prevents a fair score is flagged for clarification.
- **Cost, privacy and outages:** limit model calls to relevant extracted text, use approved providers and access controls, redact unnecessary personal data, set usage limits, and provide a manual review fallback.

## Sample ranking approach

Mandatory checks are applied before weighted scoring. Proposal B is ineligible because the bidder's own Ontario electrical contractor licence is not confirmed; a subcontractor to be chosen later is not equivalent evidence. Proposal D passes the stated mandatory checks but has no completion duration, so it is flagged for clarification rather than assigning a guessed timeline score. A, C and E are scored provisionally.

For the three fully scorable proposals:
- Experience = relevant installations / highest relevant installation count among scorable proposals × 100.
- Price = lowest price among scorable proposals / proposal price × 100.
- Timeline = (16 − completion weeks) / 16 × 100.
- Warranty = average of panel warranty score (years / 25, capped at 100) and workmanship warranty score (years / 12, capped at 100).
- Team/approach = 25 points each for a named lead, named crew, safety plan, and monitoring plan. Only explicit evidence receives points.

The total is the sum of each criterion score multiplied by its admin weight. Scores are rounded to two decimals for display; calculations use unrounded values. Because D is awaiting clarification, the displayed ranking is provisional and should be recalculated if its timeline is confirmed.
