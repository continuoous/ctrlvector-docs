# Govern submissions and approve the Workspace

!!! warning "Page status: Draft"
    Single- and multi-Analysis approval journeys have been exercised in development workshops. Product-owner and implementation-lead review is still required before this chapter is promoted from Draft.

## Purpose

Turn individually owned Factor submissions into one controlled business decision over a Version–Period Workspace.

This stage separates two responsibilities:

- contributors prepare assumptions and explain their isolated effects;
- the Workspace approver decides whether the complete, recombined business result may advance.

Approval is therefore more than confirming that every expected row exists. It evaluates what the submissions mean together, why material changes occurred, and whether unresolved risk is explicit.

## Learning objectives

After completing this chapter, you should be able to:

- distinguish submission completeness from business approval;
- use a submission baseline without confusing it with a population reference;
- interpret policy exceptions at Workspace, Analysis, Node, or Factor-event scope;
- review one shared business event across several Analyses without combining incompatible measures;
- route a precise Contribution revision without rejecting unrelated work;
- record explanations that can support the later story behind the numbers;
- distinguish Workspace approval from SUBFN materialization.

## Prerequisites

- The Workspace exists in `SWS03` with the intended Version, Period, and optional submission baseline.
- Every required Analysis is attached as a Sub-Workspace and visible in `SSW03`.
- Expected Contributions have been materialized from governed Data Collation Groups.
- Approval Policy Templates have been assigned for the required review scope.
- Factors and Nodes have the classifications needed by those policies.

## The control boundary

```text
SST01 or SST09 → SST02 when needed → SST03 → SUBAP → SUBFN → SUBTV
```

| Stage | Business question | Scope |
| --- | --- | --- |
| `SST01` | What governed reference-based values am I initially submitting? | Factor and selected Contributions |
| `SST09` | What direct values am I uploading in bulk? | Upload package and matching Contributions |
| `SST02` | Which previously submitted Contribution must change? | Targeted Contribution |
| `SST03` | What values and lineage are currently submitted? | Read-only submission evidence |
| `SUBAP` | Is the complete recombined Workspace acceptable? | One Version–Period Workspace |
| `SUBFN` | Can the approved Analysis artifacts be materialized? | Approved Sub-Workspaces |
| `SUBTV` | Do finalized totals match the independent control? | Finalized result |

The contributor owns the assumption. The approver owns the Workspace decision. `SUBFN` does not repeat that decision.

## 1. Review impact before Apply

A population reference answers **where the Draft value came from**. A submission baseline answers **what the Draft would change**. They may point to different Version–Period contexts.

When the Workspace has a valid submission baseline:

1. calculate the Draft submission;
2. review the selected Contribution member scope;
3. calculate baseline impact;
4. verify the baseline Version and Period shown by the application;
5. interpret the result as isolated, first-order impact;
6. complete required policy evidence before Apply.

The preview freezes other inputs at baseline values and excludes higher-order interactions. Use BRDGE and BRRES later for complete decomposition.

## 2. Record structured contributor evidence

If a provisional policy check is triggered, record information that another person can act on:

| Evidence | Question answered |
| --- | --- |
| Reason category | What kind of business event is this? |
| Explanation | What changed, at which Factor and member scope, and why? |
| Mitigation | What action will reduce or manage the exposure? |
| Residual risk | What may remain unresolved? |
| Owner | Who is accountable for the next action? |
| Target date | When should the action or decision be revisited? |

Avoid explanations such as “updated forecast” or “business judgment” without the affected scope. A useful statement identifies the driver, coordinates, assumption, and intended action.

Example:

> Carrier pricing for the selected Country and Mode combinations increases by 3 USD/Kg against the submission baseline. Logistics Procurement will negotiate rate cards and evaluate eligible mode shifts by 30 September.

The policy preflight is provisional because the contributor sees one isolated event. SUBAP evaluates the complete Workspace after all Factors are recombined.

## 3. Check completeness before business review

Before calculating the approval candidate, confirm:

- every expected Contribution is covered;
- the authoritative submitted values are retrievable in `SST03`;
- duplicate, withdrawn, or superseded package evidence is not treated as current;
- compositional and other Factor behavior checks pass;
- the correct baseline is configured;
- every required Sub-Workspace is complete.

Completeness proves that the candidate can be calculated. It does not prove that the result is reasonable or authorized.

## 4. Review the SUBAP cockpit

SUBAP should answer four questions:

1. **What is being authorized?** Review baseline, submitted result, change, and change percentage for each governed approval Node.
2. **Where did the change occur?** Inspect the affected Analysis, Factor, Contributions, and Dimension members.
3. **Why did it occur?** Read the contributor's structured evidence and reference context.
4. **What decision or follow-up is required?** Resolve each business event and then decide the Workspace.

A policy threshold creates a review requirement. It does not permanently prohibit approval. After explicit review, the approver may:

- accept with explanation;
- request a targeted revision;
- create a conditional approval or open forecast item;
- waive the issue with a governed reason.

Overlapping policies on one submission event should appear as one issue with all applicable policy labels. Separate submission events must retain separate resolutions even when they affect the same Factor.

## 5. Request a targeted revision

Use revision when a specific submitted scope must change:

1. select **Request revision** for the relevant issue;
2. select only the Contributions that must change;
3. state an unambiguous required outcome;
4. open the routed work in `SST02`;
5. revise and resubmit the selected values;
6. return to SUBAP and recalculate the candidate.

Workspace approval remains unavailable while the revision is pending. Recalculation must replace superseded evidence and test the revised Workspace against the applicable policy condition.

Do not reject the entire Workspace merely because one Contribution needs correction.

## 6. Use conditional approval carefully

Conditional approval helps prevent an endless submission–rejection loop when the result can proceed within explicit authority.

Example:

- current isolated impact: `+1,000 K$`;
- authorized condition: Workspace impact must be `≤ 500 K$`;
- submitter revises the assumptions;
- if the recalculated result meets the condition, the issue can resolve automatically;
- if the result cannot be reduced but the approver decides to proceed, the residual remains visible as an open forecast item with owner, mitigation, and due date.

The condition must contain a measurable metric, comparison operator, target, scope, and unit. Never apply one numeric target to incompatible measures.

## 7. Review several Analyses as one Workspace decision

One governed Contribution can affect Nodes in several Analysis Sub-Workspaces. For example, a utilization assumption may affect both a financial Freight Spend Analysis and an operational Tonnage Analysis.

SUBAP should show that shared submission once as one business event, with every affected Analysis clearly identified. Each consequence must retain its own:

- Analysis and Node;
- observed movement;
- format and unit;
- governed boundary;
- condition or required response.

Do not add or compare incompatible measures. A financial movement in K$ and an operational movement in Tonnes belong in the same causal context but remain separate observations and conditions.

Separate submission scopes must also remain separate decisions. Two Rate Per KG submissions for different Country and Mode scopes may require different explanations, owners, or approver outcomes even when they affect the same Node.

## 8. Approve once, then materialize

After every issue is resolved and no revision is pending, approve the Workspace once. `SUBFN` then materializes the approved Analysis artifacts.

For a Workspace containing several Analyses:

- the same Workspace approval authorizes every ready Analysis Sub-Workspace;
- each Analysis receives its own immutable finalized artifact;
- the parent Workspace becomes finalized only after the required artifacts exist;
- a technical retry must reuse or skip completed artifacts rather than duplicate them;
- finalized artifacts do not reopen the business approval decision.

`SUBFN` is therefore a technical materialization and recovery boundary. It is not the place to reconsider an approved business outcome.

## 9. Preserve the decision story

Finalization should preserve the complete approved decision context, not only the submitted totals. Each Analysis artifact must remain traceable to the same Workspace-level evidence:

- the approved candidate and decision;
- applicable policy checks;
- headwind, tailwind, or neutral Node movements;
- Factor- and Node-level resolutions;
- scoped contributor and approver explanations;
- accepted residual items;
- mitigations, risks, action owners, and due dates;
- the Contribution scope associated with each action.

This frozen context supports a later story behind the numbers without reconstructing history from approval screens that may have changed. It also preserves the difference between the operational facts held by one Analysis artifact and the holistic Workspace decision that authorized all related artifacts.

## Verification

- [ ] All expected Contributions are covered
- [ ] Submitted values and lineage are retrievable
- [ ] Baseline and submitted Node totals reconcile independently
- [ ] Every issue identifies its Factor and Contribution member scope
- [ ] Separate business events have separate resolutions
- [ ] Overlapping policies do not duplicate the same business event
- [ ] Targeted revisions reopen only the selected Contributions
- [ ] Recalculation removes superseded evidence
- [ ] Structured explanations include owner and follow-up where required
- [ ] Workspace approval occurs once
- [ ] SUBFN receives only an approved, current candidate
- [ ] Incompatible Analysis units remain separate
- [ ] Every ready Analysis produces its own immutable artifact
- [ ] A technical retry does not duplicate finalized artifacts
- [ ] Finalized artifacts retain explanations, decisions, owners, and due dates

## Common errors

| Symptom | Likely cause | Corrective action |
| --- | --- | --- |
| SUBAP shows incomplete scope | One or more expected Contributions or Sub-Workspaces are not complete | Return to SST01, SST09, or SST02 and reconcile coverage |
| An explanation lacks business context | Free text was recorded without Factor or member scope | Reopen the issue and attach structured, scoped evidence |
| The same submission appears as several decisions | Policy checks were treated as events instead of labels on one event | Resolve the shared evidence once while retaining all policy checks |
| Old evidence remains after revision | The approval candidate was not recalculated | Recalculate SUBAP from current submitted facts |
| Approval is disabled | A revision, blocking review item, or stale candidate remains | Resolve the item or recalculate before deciding |
| A total combines K$ and Tonnes | Unlike Node formats were aggregated | Govern and display them at separate Analysis or Node scopes |
| One shared submission appears as unrelated issues | Cross-Analysis consequences were detached from their common evidence | Present one business event with separate scoped observations |
| A finalization retry creates another artifact | The operation was not treated as idempotent technical recovery | Stop downstream use and reconcile the approved candidate and existing artifacts |

## Practice

A Scenario Workspace contains Freight Spend and Tonnage Analyses. A Rate Per KG submission affects only Freight Spend; a volume submission affects both Analyses.

1. Define which event should name only the Freight Spend Analysis.
2. Define which event should show both Analyses but retain separate K$ and Tonnes observations.
3. Assume only the Canadian volume Contributions require correction. Describe the targeted revision route without reopening unrelated submissions.
4. List the evidence needed if the approver accepts the remaining exposure conditionally.

Expected design: two business events remain distinguishable; the shared volume event has one resolution flow with scoped consequences; only Canadian Contributions route to `SST02`; and any residual obligation has a measurable condition, owner, mitigation, risk, and target date.

## Knowledge check

1. What is the difference between a population reference and a submission baseline?
2. Why does complete Contribution coverage not prove business acceptability?
3. At what scope is the final SUBAP decision made?
4. When should a targeted revision be used?
5. Why must K$ and Tonnes retain separate conditions?
6. Does SUBFN make another business approval decision?

??? note "Answers"
    1. A population reference calculates a proposed value; a submission baseline measures the proposed value's isolated change.
    2. Coverage proves expected data exists, not that the combined result is reasonable or authorized.
    3. The complete Version–Period Workspace.
    4. When a specific Contribution scope must change without reopening unrelated submissions.
    5. They are incompatible measures with different business meaning and units.
    6. No. SUBFN technically materializes artifacts after Workspace approval.

## Related material

- [Load and govern data](data-workflow.md)
- [Populate inputs from reference data](reference-population.md)
- [Prepare and submit data](../operating/prepare-and-submit-data.md)
- [Finalize databases](../operating/finalize-databases.md)
- [Auditability and controls](../administration/auditability-and-controls.md)

## Product applicability

The procedures reflect development-workshop review through 27 August 2026. The multi-Analysis workshop verified shared and Analysis-local events, unit-safe conditions, targeted revision, candidate recalculation, one Workspace approval, separate immutable Analysis artifacts, and idempotent finalization retry. It stopped before multi-Analysis totals verification in `SUBTV`.
