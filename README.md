# flops-compute-prices — a Claude Skill

A self-contained [Agent Skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills) that teaches an AI agent to look up **GPU / compute rental reference prices** and cite them with a **verifiable source**, using the public [FLOPS Index](https://flopsindex.com) API. **No API key required.**

## Features

- **No API key, no signup** — the public FLOPS surface is anonymous, so the skill works the moment it is installed.
- **Three accelerator vendors** — NVIDIA data-center and workstation GPUs, AMD `MI300X`, and Intel `GAUDI2`.
- **All three market types** — on-demand, spot and DePIN, e.g. `FLOPS-H100-OD`, `FLOPS-A100-SPOT`, `FLOPS-A100-DEPIN`.
- **Cite-ready by construction** — every value carries a `verify_url` the reader can independently check.
- **Fact-checking built in** — `verify` tests a claimed price against the published reference instead of trusting it.
- **Zero dependencies** — `scripts/flops.py` is stdlib-only Python 3.8+, no third-party packages.
- **Honest about what it is** — values are indicative and delayed to the most recent 6-hour UTC mark, never presented as live quotes or settlement marks.

## What it does
When a user asks what an accelerator costs to rent — "what's an H100 going for?", "cheapest A100 right now?", "is $1.50/hr for an H100 spot right?" — the skill drives the **price → verify → cite** flow against the public FLOPS API:

- **Price** — `GET /v1/price/{slug}` returns the public envelope (value, unit, `as_of`, confidence, verify/citation URLs).
- **Cheapest** — lowest rate across a chip's markets (spot / on-demand / DePIN).
- **Verify** — fact-check a claimed value against the published reference.
- **Cite** — present the value with its timestamp, the delayed-reference caveat, and a link the reader can independently check.

## Contents
- `SKILL.md` — the skill definition (instructions + when-to-use).
- `scripts/flops.py` — a zero-dependency (stdlib `urllib`) helper: `price`, `catalog`, `search`, `verify`, `cheapest`.

## Requirements
Python 3.8+ — standard library only, no third-party packages. On macOS/Linux use `python3` in place of `python` below.

## Try it
```bash
python scripts/flops.py cheapest h100        # cheapest across a chip's markets (flags decentralized/DePIN rates)
python scripts/flops.py price FLOPS-H100-SPOT
python scripts/flops.py verify FLOPS-H100-SPOT 1.50
python scripts/flops.py catalog              # every index + its value
python scripts/flops.py search h100          # find slugs by keyword
```
The helper mirrors the public API. Note `data_tier: "LIVE"` is a *coverage* tier, **not** a freshness claim — public values are always delayed to the most recent 6-hour UTC mark.

## Note
FLOPS publishes **indicative, delayed, source-opaque reference levels** — not live provider quotes, settlement marks, or investment advice. The skill carries that caveat on every citation. Same data is also available as [MCP tools](https://app.flopsindex.com/mcp) and SDKs (`pip install flopsindex`, `npm i @flopsindex/sdk`).

Apache-2.0.
