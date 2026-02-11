# Agent Skills

Reference documentation designed for AI agents working in the Moberg JupyterHub sandbox environment. These files are optimized for inclusion in LLM context windows — info-dense, minimal prose, focused on API signatures and working examples.

## data-analysis/

Skill for a data analysis agent that helps users work with CNS archives and clinical neuromonitoring data.

| File | Purpose |
|------|---------|
| `system-prompt.md` | Compact overview injected into the agent's system prompt (always in context) |
| `cns-utils-reference.md` | Full API reference for reading/writing CNS archive data |
| `moberg-dsp-reference.md` | Signal processing library API (filtering, analytics, preprocessing, epoching) |
| `analytics-guide.md` | Clinical analytics cookbook with runnable code examples (PRx, COx, CPPOpt, MAPOpt, SD detection) |
| `database-utils-reference.md` | Patient metadata and annotation query API |
| `domain-glossary.md` | Signal abbreviations, units, derived indices, clinical concepts |

### Delivery Model

- **Tier 1**: `system-prompt.md` is always loaded into the agent's context window
- **Tier 2**: Reference files are on the filesystem for the agent to read on demand
- **Tier 3**: The rest of this wiki (foundations, signals, artifacts, workshops) provides deep domain context when needed
