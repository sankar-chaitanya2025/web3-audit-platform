# Research Archive

Raw dumps, unedited, dated. Grep this, don't read it top to bottom.
Distilled conclusions go in DECISIONS.md and TOOLSTACK.md instead.

---

## 2026-07-10 — Security-agent-swarm-for-audits candidate search

Source: Perplexity deep research.

Verdict: no turnkey "multi-agent smart contract audit swarm + Foundry
exploit gen + PR delivery" repo exists in one codebase. Reusable parts
exist as separate layers. This is a real gap, not a failed search.

### Candidates found

**Team-Atlanta/aixcc-afc-atlantis** ("Atlantis")
- AIxCC Cyber Reasoning System, autonomous vuln discovery/analysis
- 357 stars, real repo structure (webservice + architecture dirs), not a demo
- Custom CRS orchestration, not an off-the-shelf agent framework
- Reuse: system decomposition, service boundaries, task routing, infra layout
- Does NOT give: EVM/Solidity specifics, Foundry exploit proving — still ours to build

**samuelberston/vuln-agent**
- "Web Application AI Security Agent" — CodeQL + Semgrep + Dependency Check +
  npm audit + PyPI Safety, routed through Discovery Agent -> tool-selection ->
  parallel analysis -> Triage Agent
- Only 6 stars, last commit Jan 24 2025 - small, likely early-stage/prototype
- Custom staged pipeline, closest match to "AI-augmented static analysis
  orchestrator" pattern from the research prompt
- Reuse: discovery->tool routing->triage graph shape, report structure
- Does NOT give: Solidity parsers, Slither/Mythril/Foundry adapters, exploit
  hypothesis gen, fork-test execution - still ours to build

**calimero-network/ai-code-reviewer**
- Multi-agent code review, "orchestrates multiple LLMs" per search result
- Published 2026-02-02, security specialization unverified from fetched pages
- Treat as: possible PR-comment/review-flow plumbing donor, not an audit core

**ChrisTimperley/RepairChain**
- AIxCC repo, autonomous vuln REPAIR (not review) - LLM + search + static analysis
- Appears to be a real research system
- Useful for: patch-generation loop, validation loop, benchmark-thinking patterns
- Not a review swarm - different problem (fixing vs finding)

**semgrep/mcp**
- Official Semgrep MCP server - lets LLM clients call Semgrep as a structured tool
- Published 2025-03-16, real working integration, not a swarm itself
- Reuse: as the "tool server" layer inside our agents (Step 2 -> Step 3 bridge)

**antgroup/MCP-Security**
- Multi-stage scanner: Semgrep-based static taint analysis + dynamic LLM eval
- Published 2025-07-29, domain is MCP/plugin ecosystems, NOT smart contracts
- Reuse: template for "static scan + LLM contextual evaluation" composition pattern
- Would need full domain-rule replacement for our use case

### Gaps confirmed (= our opportunity)
- No repo found with the full package: swarm + Foundry exploit gen + PR delivery
- No repo found with verified EVMbench benchmarking - AIxCC is the closest
  "find and validate real vulns" comparable, but not Web3-specific
- Confirms: our moat is INTEGRATION (gluing these layers with EVM-specific
  reasoning + PoC proving), not any single novel component

### Suggested next research pass (not yet run)
Second-pass sweep focused only on repos with explicit EVMbench / Foundry /
Slither / PoC-generation signals together, ranked by forkability.
