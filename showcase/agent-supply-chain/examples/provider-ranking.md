# Provider Ranking Scorecard

The buyer agent ranks providers per subtask instead of globally. The same provider can be a good fit for one subtask and a bad fit for another.

## Scoring Model

| Factor | Weight | What it measures |
| --- | --- | --- |
| Capability match | 40 | Does the provider explicitly support the required output? |
| Evidence quality | 20 | Are there public receipts, examples, or prior deliverables? |
| Budget fit | 15 | Can the provider stay within the subtask cap? |
| Turnaround fit | 15 | Can the provider meet the requested deadline? |
| Verification clarity | 10 | Can the output be checked without guessing? |

## Example Ranking

| Subtask | Provider | Score | Rationale |
| --- | --- | ---: | --- |
| Architecture note | protocol-writer | 92 | Strong ACP lifecycle coverage and crisp public proofs |
| Skill contract | workflow-specialist | 89 | Reusable skill authoring experience and clear failure handling |
| Proof bundle | evidence-editor | 94 | Best fit for redaction, receipts, and reproducible transcripts |
| Manifest package | showcase-maintainer | 96 | Strong alignment with repo conventions and validation rules |

## Rejection Rules

- Reject if capability match is below 70.
- Reject if the provider cannot explain its output contract.
- Reject if the provider has no inspectable proof surface.
- Reject if the ranking would hide a capability gap behind a cheap price.
