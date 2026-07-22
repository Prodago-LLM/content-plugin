# content-plugin

Claude plugin for Prodago SMEs working in PrimeRise Content Editor. Bundles live MCP data access and embedded domain skills into a single install.

## Install

```
! claude plugin install github:prodago/content-plugin
```

## What it includes

**MCP connection** — connects Claude to the live PrimeRise CE instance at `https://primerise.prodago.com/mcp`, giving it read access to practices, compliance objects, questionnaires, artefacts, and all classification mappings.

**11 skills** covering the full SME workflow:

| Skill | What it does |
|-------|-------------|
| `op-from-activity` | Draft complete OP(s) from a plain-English activity description |
| `op-from-title` | Generate a complete OP from a title — description, intended results, context, full IMR/OVP placement, PP phase, and AM mapping |
| `draft-specification` | Draft CO-specific specification text for an OP–statement mapping |
| `draft-artefact` | Suggest and draft an artefact for an OP |
| `find-similar-ops` | Search for existing OPs to avoid duplication before creating new ones |
| `map-co-statement` | Full mapping workflow: analyse a CO statement, find or draft OPs |
| `check-op-mapping` | DoD 1 — verify all CO statements have appropriate OP mappings |
| `check-specifications` | DoD 2 — review specification quality across all mappings |
| `check-questionnaire` | DoD 3 — verify every project-context OP is reachable through the questionnaire |
| `check-artefacts` | DoD 4 — review artefact linkage for all mapped OPs |
| `check-op-completeness` | DoD 5 — verify PP, OVP, AM mappings and writing standard compliance |

## Requirements

- Claude Code with a Prodago account (MCP authentication via prodago-auth)
- Access to `https://primerise.prodago.com`
