# Investigation Patterns Reference

## Research Loop

The research loop is the core execution pattern. Unlike fixed-round debate, the lead agent evaluates after each round whether more investigation is needed.

### Evaluation criteria (after each round)

Ask these questions:
1. Are there BLOCKING unknowns in the unknowns map? If yes: target them with specific deep-dive tasks.
2. Did investigators discover new facets not in the original plan? If yes: spawn new investigators for those facets.
3. Is the evidence quality sufficient? If most findings are [speculative], direct agents to do more grounding.
4. Are investigators converging or still finding new information? If the uncertainty map is stable (no new unknowns discovered), stop.

### When to stop

- Remaining unknowns require action outside the codebase (user testing, vendor consultation, production data)
- The uncertainty map has stabilized across two rounds
- Scope score was focused (4-6) and one round of cross-pollination is complete
- The lead agent judges that further investigation would not materially change the conclusions

### When to continue

- A BLOCKING unknown has a plausible resolution path via codebase investigation
- The Scope Sentinel flagged a facet that no investigator has explored
- Cross-model divergence on a factual question suggests one model may be wrong — a targeted follow-up can resolve it

## Cross-Pollination Brief

Produced by the lead agent after Round 1. This is an internal working document, not part of the final report.

Structure:
- **Findings map**: What each investigator discovered, organized by facet. One paragraph per investigator summarizing their key findings and evidence quality.
- **Connection points**: Where one investigator's findings affect another's facet. Template: "[Finding A from Investigator X] affects [Facet Y] because [reason]."
- **Uncertainty hotspots**: Facets where evidence is thin or speculative. List the specific unknowns and what investigation would resolve them.
- **Blind spots flagged**: Anything the Scope Sentinel identified as uncovered. These become priority targets for Round 2.

## Verification Protocol

The Integrator performs verification after the final investigation round.

### What to check

For each finding in the investigation:

**[grounded] findings:**
- Does the cited file/URL actually exist?
- Does the cited content actually support the claim?
- Is the evidence current (not from a deprecated version, old docs, etc.)?
- Could the same evidence support a different conclusion?

**[informed] findings:**
- Is the domain knowledge current for the tech stack in question?
- Are there known exceptions or edge cases that would invalidate the general knowledge?
- Would a different domain expert reach the same conclusion?

**[speculative] findings:**
- Could this be resolved with one more search or file read?
- Is the speculation directionally useful even if unverified?
- What is the cost of acting on this speculation if it turns out to be wrong?

### Verification output

The Integrator produces a verification report:
- Findings confirmed (evidence holds)
- Findings downgraded (evidence weaker than claimed)
- Findings upgraded (additional evidence found)
- Findings that could be resolved with targeted follow-up
- Overall investigation quality assessment

## Consolidation

The Integrator produces a draft consolidation. The lead agent refines it.

### Consolidation structure

1. **Findings summary by facet**: For each investigator, the key findings with final evidence tags and confidence levels.
2. **Cross-facet dependency map**: Which findings depend on which others. Format as a list: "Finding A -> Finding B: [relationship]"
3. **Risk matrix**: For each identified risk: likelihood, impact, mitigation, and evidence quality.
4. **Unknowns map**: For each unknown: why it matters, how to resolve it, and priority (BLOCKING/IMPORTANT/NICE-TO-KNOW).
5. **Investigation quality assessment**: What percentage of findings are grounded vs informed vs speculative. Flag areas where the investigation was shallow.

### Confidence levels

- **HIGH**: Multiple grounded sources confirm. No contradicting evidence. Independent investigators reached the same conclusion.
- **MEDIUM**: Some grounded evidence, some informed. Minor gaps remain. The conclusion is likely correct but has not been fully verified.
- **LOW**: Mostly informed or speculative. Significant gaps. The conclusion may change with more investigation.

### Risk priority

BLOCKING unknowns must be highlighted prominently. They represent risks that could invalidate the entire investigation's conclusions. The final report should make clear what decisions can be made now and what must wait for BLOCKING unknowns to be resolved.

## Source Quality Heuristics

Prefer authoritative sources in this order:
1. Code and configuration in the actual codebase (highest authority)
2. Official documentation for the technologies in use
3. RFCs, specifications, and standards documents
4. Academic papers and peer-reviewed research
5. Reputable technical blogs and conference talks
6. Community forums and Stack Overflow (corroborate with other sources)

Flag when findings rely primarily on sources from category 5-6. Note when authoritative sources are unavailable for a finding.

Avoid SEO-optimized content farms — they often contain outdated or superficial information that can mislead investigations.

For software engineering topics, prefer current APIs and modern approaches. Search results frequently surface deprecated patterns, legacy APIs, and outdated frameworks. When multiple approaches exist, default to the current recommended one unless there is a specific reason to use an older approach (e.g., backward compatibility constraint). Flag deprecated APIs as a finding if the codebase uses them.

## Anti-Patterns

**Premature certainty**: Reporting "no issues found" without demonstrating thorough investigation. The absence of evidence is not evidence of absence.
**Facet tunnel vision**: Investigating only the assigned facet without considering cross-facet effects. The cross-pollination round exists to prevent this.
**Speculation cascading**: Building implications on top of speculative findings without flagging the uncertainty. If Finding A is speculative and Implication B depends on A, B is also speculative.
**Scope creep**: Investigating interesting tangents that don't serve the research question. The task boundaries exist to prevent this.
**Search addiction**: Continuing to search after finding sufficient evidence. Per Anthropic's experience, agents default to over-searching. Respect the tool call budget.

## Quality Checklist

Before finalizing any consolidation:
- Does every finding have an evidence tag and confidence level?
- Are BLOCKING unknowns clearly identified and prioritized?
- Does the risk matrix include mitigation strategies?
- Are cross-facet dependencies mapped?
- Is the investigation quality assessment honest about gaps?
- Does the report actually answer what was asked?
- Would the recommendations change if any LOW-confidence finding turned out to be wrong?
