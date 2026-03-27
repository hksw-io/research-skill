# Investigator Selection Reference

## Approach

Decompose the research topic into facets, then assign one investigator per facet. Derive domain-specific roles from the topic context — do not use generic labels like "Researcher" or "Analyst."

## Facet Decomposition

1. Identify the technical systems involved. Each major system or component that the research question touches becomes a facet.
2. Identify non-technical dimensions that matter: compliance, UX, operations, cost, security, performance.
3. Identify the riskiest unknown — the area where a wrong assumption would be most costly. This gets its own investigator AND is the best candidate for the Codex-powered role.
4. Check for hidden facets: deployment, testing, monitoring, migration path, rollback strategy.

## Structural Roles

Two roles are always present. Frame them in domain-specific terms.

**Integrator**
- Maps cross-facet dependencies and identifies cascading effects
- Frame in domain terms: for a Japanese character investigation, this might be "Data Pipeline Engineer" who sees how encoding affects every stage. For an API migration, this might be "Platform Architect" who sees the full request lifecycle.
- The Integrator's structural job is to connect findings across facets and produce the draft consolidation. It also performs the verification step.
- In Round 2+, the Integrator receives special instruction to produce a dependency map.

**Scope Sentinel**
- Monitors investigation coverage and flags blind spots
- Frame in domain terms: might be "QA Infrastructure Lead" who asks about test coverage, or "Operations Engineer" who asks about monitoring gaps.
- The Scope Sentinel's structural job is to prevent facet tunnel vision — asking "what are we NOT looking at?" It does not investigate deeply itself; it surveys breadth.
- In Round 2+, the Scope Sentinel receives special instruction to identify underexplored facets and unexamined assumptions.

## Delegation Template

Each facet investigator must receive clear delegation boundaries. Use this template:

```
Objective: [What to investigate — concrete and bounded]
Output format: [Findings tagged [grounded]/[informed]/[speculative] with confidence levels]
Tool guidance: [Which tools to prefer, search strategy hints]
In scope: [Specific aspects to investigate]
Out of scope: [What NOT to investigate — prevents overlap with other investigators]
Tool call budget: [N calls based on scope score]
```

Example:
```
Objective: Investigate how the file ingestion pipeline handles multi-byte characters (Japanese, Chinese, Korean)
Output format: Findings with evidence tags, organized by pipeline stage
Tool guidance: Focus on Read/Grep of ingestion code. Check encoding handling, BOM detection, filename extraction.
In scope: File upload, parsing, filename extraction, encoding detection
Out of scope: Storage layer (assigned to Storage Engineer), search indexing (assigned to Search Specialist)
Tool call budget: 10-15 calls
```

## Examples

### "Technical Spike: Japanese Character Support Impact Assessment"

Facet decomposition: ingestion, file naming/paths, data storage, search/indexing, display/rendering.

| Role | Type | Facet | Investigates |
|------|------|-------|-------------|
| File Ingestion Engineer | Investigator | Ingestion pipeline | Multi-byte character handling, encoding detection, BOM handling, filename extraction |
| Storage/Database Engineer | Investigator | Data layer | Schema encoding (UTF-8 vs UTF-16), collation settings, index behavior with CJK, string length vs byte length |
| Filesystem/Path Specialist | Investigator | File naming | OS-level path handling (NFC vs NFD on macOS vs Linux), max path length with multi-byte chars, filename sanitization |
| Search Infrastructure Engineer | Investigator | Search/indexing | Full-text search tokenization for Japanese (no word boundaries), n-gram vs morphological analysis, CJK support |
| Data Pipeline Engineer | Integrator | Cross-cutting | How encoding issues cascade across systems, where a failure in one layer surfaces in another |
| QA Infrastructure Lead | Scope Sentinel | Gaps | CJK test data coverage, which environments lack testing, untested edge cases |

Codex assignment: Storage/Database Engineer (highest uncertainty about existing schema behavior).

### "Research whether offline indicators should be used on Apple Watch apps"

Facet decomposition: platform conventions, user behavior, technical constraints, design impact.

| Role | Type | Facet | Investigates |
|------|------|-------|-------------|
| watchOS Connectivity Engineer | Investigator | Technical constraints | WCSession states, reachability API, Bluetooth vs WiFi, what "offline" means on watchOS |
| Apple HIG Specialist | Investigator | Platform conventions | Apple's guidelines on connectivity indicators, how Apple's own Watch apps handle this, precedent |
| Watch UX Researcher | Investigator | User behavior | User expectations when disconnected, failure modes (stale vs no data vs error), complication behavior |
| watchOS App Architect | Integrator | Cross-cutting | How offline state affects data flow, background refresh, complications, notifications |
| Accessibility/Edge Case Reviewer | Scope Sentinel | Gaps | VoiceOver for connectivity state, low-power mode, cellular vs GPS-only Watch models |

Codex assignment: Apple HIG Specialist (factual question about Apple's stance — cross-model validation most valuable).

### "What are the implications of migrating from REST to GraphQL?"

| Role | Type | Facet | Investigates |
|------|------|-------|-------------|
| Backend API Engineer | Investigator | Server implementation | Schema design, resolver patterns, N+1 query risks, existing endpoint mapping |
| Frontend Integration Engineer | Investigator | Client impact | Query patterns, caching (Apollo/Relay), code generation, bundle size |
| DevOps/Infrastructure Engineer | Investigator | Operations | Rate limiting, monitoring, error tracking, deployment strategy, CDN implications |
| API Security Specialist | Investigator | Security | Query depth limits, authorization patterns, introspection exposure, field-level permissions |
| Platform Architect | Integrator | Cross-cutting | Migration path, versioning strategy, coexistence period, performance baseline comparison |
| QA Lead | Scope Sentinel | Gaps | Testing strategy changes, contract testing, regression coverage |

Codex assignment: Backend API Engineer (most code-grounded facet, highest value for cross-model code analysis).

## Codex Assignment Guidelines

Assign Codex to the role that benefits most from independent reasoning. Prioritize:
1. The facet with the most uncertainty — cross-model disagreement reveals genuine uncertainty
2. Facets requiring code analysis — different models may find different patterns
3. Factual questions where model-specific knowledge differences matter

Avoid assigning Codex to:
- The Integrator (needs full context across all findings)
- The Scope Sentinel (needs meta-level awareness)

## Format Rules

- Label by role only. No fictional names or titles.
- No personality descriptions or communication style notes.
- Keep findings direct and analytical. No "In my experience as a..." or conversational filler.
- Each investigator's output should be labeled with its role: `Storage/Database Engineer: [findings]`
