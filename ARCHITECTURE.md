# Architecture (current state — overwrite as design evolves)

Ingestion → Static Scan → Reasoning Swarm → PoC Validation → Memory → Delivery

| # | Layer | Status | Notes |
|---|---|---|---|
| 1 | Ingestion/Indexing | Designed | crytic-compile + Slither + Aderyn + codeql-solidity, graph persisted, diffed per PR |
| 2 | Static/deterministic scan | Designed | Slither+Aderyn union, Semgrep custom rules, normalize/dedupe schema defined conceptually |
| 3 | Reasoning agent swarm | Not designed | LangGraph chosen. Node/edge design not started. Patterns to borrow: vuln-agent's discovery->triage staging |
| 4 | Dynamic PoC validation | Not designed | Foundry fork tests required for every High/Critical. Non-negotiable per earlier decision. Implementation TBD |
| 5 | Memory/knowledge layer | Not designed | Persist triage feedback per repo so it doesn't re-flag same thing every PR |
| — | Delivery (PR comments/dashboard) | Not designed | |
| — | Benchmark harness (EVMbench) | Not designed | Needed to produce a credible recall/exploit-success number |

## Next step
Design Layer 3: the actual LangGraph graph — nodes, conditional edges, and
what makes a "reasoning agent" different from "GPT reads Slither output."
