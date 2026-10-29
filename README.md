# MATACS AI Proposal Triage Design

**Candidate:** Anam Fatima  
**Assessment reference:** MATACS-TH-2026-02  

## Deliverables

- [Process flow diagram](diagram/flow.png)
- [Design write-up](WRITEUP.md)
- [AI prompts](PROMPTS.md)
- [Expected ranking](ranking.json)

## AI tool disclosure

I used ChatGPT to help structure the design, draft the extraction prompt, and check the scoring approach. I independently checked the sample proposals against the mandatory requirements and weighted scoring rules. I corrected the initial approach so that a proposal's embedded instruction to rank it first is treated as untrusted document content, and a missing completion timeline is flagged rather than invented.

## Assumptions

- A mandatory requirement is passed only when the proposal explicitly confirms it for the bidder.
- A licensed subcontractor that is not yet identified does not satisfy the requirement that the bidder hold and state its own Ontario electrical contractor licence.
- A proposal missing a weighted criterion that is necessary to score is flagged for clarification and is not assigned a final rank until the missing information is verified.
- Scores are calculated by deterministic code from versioned admin criteria and extracted, human-confirmed facts. AI does not independently decide the award.
