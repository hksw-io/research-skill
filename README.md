# research-skill

Multi-agent research and investigation. Spawns a team of investigators to explore different facets of a topic in parallel. One is always Codex (different model family) for cross-validation.

Inspired by [Anthropic's multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system).

## Quick start

```sh
/research Technical spike: Japanese character support impact assessment
/research size:5 depth:comprehensive Should we migrate from REST to GraphQL?
/research Do we need offline indicators on our Apple Watch app?
```

## Arguments

| Arg | Example | Description |
|-----|---------|-------------|
| `size:N` | `size:5` | Team size (3-7) |
| `depth:X` | `depth:comprehensive` | focused (1-2 rounds), standard (2-3), comprehensive (4+) |
| *(rest)* | `Japanese character support` | Research topic (required) |

## What happens

1. **Context** — explores codebase (or skips for non-code topics), asks high-impact questions
2. **Scope** — scores topic on 4 dimensions (4-12), sets team size and tool call budget
3. **Investigators** — decomposes topic into facets, assigns domain-specific investigators (e.g., "Storage Engineer", "Filesystem Specialist", "Search Infrastructure Engineer")
4. **Team** — spawns one agent per investigator, all working in parallel
5. **Research loop** — investigate -> evaluate -> cross-pollinate -> evaluate -> deep-dive if needed
6. **Verification** — Integrator validates all findings against cited sources
7. **Report** — key findings with evidence tags, risk matrix, unknowns map, recommendations

## Design

- **Orchestrator-worker pattern** — lead agent coordinates independent investigators
- **Codex scales with team** — 1-3 Codex-powered investigators depending on team size, never more than half
- **Facet-based decomposition** — each investigator has clear boundaries (what's in/out of scope)
- **Adaptive rounds** — lead agent decides if more investigation is needed, not fixed round count
- **Evidence tagging** — findings tagged [grounded], [informed], or [speculative]
- **Start wide, then narrow** — investigators begin with broad searches, then focus
- **Verification step** — Integrator validates citations before final report
- **Mandatory question enforcement** — agent questions surfaced to user via AskUserQuestion

## How it differs from /debate

| | /debate | /research |
|---|---------|-----------|
| Goal | Decide between options | Map unknowns, assess impact |
| Method | Hegelian dialectic (thesis/antithesis/synthesis) | Investigate/cross-pollinate/consolidate |
| Roles | Contrarian + Synthesizer | Scope Sentinel + Integrator |
| Output | Decision + consensus labels | Findings + risk matrix + unknowns map |

## Requirements

- [Codex CLI](https://github.com/openai/codex) installed
- `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` in Claude Code settings

## License

MIT
