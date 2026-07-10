Decisions Log

One entry per decision. Format: What → Chosen vs Rejected → Why → Tradeoff.
Append-only. Never delete old entries — if you reverse a decision, add a new
entry that says so, don't erase the history of why you changed your mind.


2026-07-10 — Project goal

Portfolio project that could become a real product. Primary motivation:
get hired at Hacktron AI. Secondary: it should be genuinely good, not a
demo.


2026-07-10 — Overall architecture: 5 layers


Ingestion/Indexing (deterministic)
Static/deterministic scan (deterministic)
Reasoning agent swarm (LLM)
Dynamic PoC validation (Foundry fork tests — non-negotiable)
Memory/knowledge layer (persists triage feedback per repo)


Why 5 and not "one big prompt": cost, reliability, and hallucination control.
Deterministic tools do the cheap/exact work; LLMs only reason over
already-structured output.


2026-07-10 — Indexing layer: reject GBrain, use code-property-graph stack

GBrain rejected — it's a prose/entity knowledge graph (regex-extracted
"X founded Y" style facts from markdown). Zero code semantics. Wrong domain
entirely; would just index READMEs, not attack surface.

Chosen instead:


crytic-compile — normalizes Foundry/Hardhat/Truffle builds
Slither — call graph, inheritance graph, CFG, data-dependency via SlithIR
(Python API). Industry-standard deterministic backbone.
Aderyn — faster Rust-based AST pass, complements Slither
codeql-solidity — persistent queryable graph (tree-sitter → TRAP →
QL database). Closest thing to "GBrain but for code."


Pattern stolen from GBrain (not the tool, the architecture): compile once
per commit → persist graph (Neo4j/Postgres) → diff graph per PR instead of
re-parsing → LLM agents query the graph instead of re-reading raw source.

Gap flagged: this stack is Solidity/Vyper only. Multi-chain (Rust/Solana,
Move, Cairo) needs separate extractors per chain — future step.


2026-07-10 — Static scan layer

Run in parallel on every commit, before any LLM involvement:


Slither (90+ detectors)
Aderyn (union findings with Slither — two independent detectors catch
more than one)
Semgrep with custom Solidity rules (extensibility point — write rules
from real triage findings)
Mythril/Manticore — optional, expensive, reserved for whitebox/full-repo
pentest mode only, not per-PR CI path


This layer's job: cut search space, produce structured deduplicated
findings with severity + location + graph context. NOT the source of
truth on exploitability (raw Slither output on a real repo is 60-80%
noise). That judgment is Step 3's job.

Pipeline shape:
graph → scanners run concurrently → normalize into one schema → dedupe →
attach relevant subgraph to each finding → hand to reasoning agents


2026-07-10 — Orchestration framework: LangGraph

Chosen over CrewAI, AutoGen, OpenAI Agents SDK, Google ADK.

Why: Need audit trails, checkpointing, and precise control over branching
error-recovery flows — we ARE an audit tool, so "what did the agent see
and decide, and can I roll it back" is a product requirement, not a nice
-to-have. LangGraph reached v1.0 late 2025, is the default LangChain agent
runtime, and is provider-agnostic (not locked to OpenAI/Gemini).

Rejected CrewAI: independent benchmarks show ~3x token overhead vs
LangGraph on simple workflows; abstraction becomes opaque/hard to debug
at production complexity — exactly the complexity we'll hit.

Tradeoff accepted: more boilerplate than CrewAI (~120 lines vs ~40 for a
basic agent loop). Acceptable — not building a toy.


2026-07-10 — Reference architectures found (read, don't build on)


AuditAgent (atahabilder1/AuditAgent, GitHub) — planning/reasoning layer +
tool orchestration layer (Slither/Mythril/LLM/Etherscan) + synthesis/
report layer. Good reference for the shape of Step 3. NOT production
grade — treat as a diagram to learn from, not a fork target (yet —
pending deeper research prompt results).
aiudit (leeederek/aiudit, GitHub) — same pattern, LangChain-based, wraps
Mythril/Foundry/Slither as agent tools.
Independent Sherlock-competition comparison: AuditAgent had best overall
recall but "many false positives, noise" — confirms Step 2's lesson:
raw recall without PoC validation is a liability, not a selling point.
This is the wedge against existing tools.



2026-07-10 — Validation/benchmark target: EVMbench

Built by OpenAI + Paradigm. 120 curated vulnerabilities across 40
professional audits. Three modes: detect / patch / exploit. Exploit mode
requires actual on-chain state change (drained balance) in a sandboxed
EVM — not just a text finding.

Decision: this is not just background reading, it's the credibility
mechanism. Plan is to eventually run our pipeline against EVMbench and get
a real recall/exploit-success number to put on resume/in interview. This
is the concrete, falsifiable claim that separates "I built an audit tool"
from "I built a tool that claims to audit things."


2026-07-10 — Fork-vs-build verdict: no turnkey repo exists, build the glue

Research (full dump in RESEARCH.md) confirms: no open-source repo combines
multi-agent audit swarm + Foundry exploit generation + PR delivery in one
codebase. Reusable parts exist as separate layers across different repos.

Decision — don't fork one repo. Borrow patterns from three, build on
LangGraph ourselves:


Orchestration/system-shape pattern: study Team-Atlanta/aixcc-afc-atlantis
(AIxCC CRS, 357 stars, real service architecture) for service boundaries
and task routing — not Web3-specific, pattern only.
Staged pipeline shape: study samuelberston/vuln-agent's Discovery Agent ->
tool-routing -> parallel analysis -> Triage Agent flow — matches our
Step 2 -> Step 3 handoff almost exactly. Small repo (6 stars), read for
structure, don't depend on it as a dependency.
Tool-exposure layer: semgrep/mcp — official, real, working. Use as the
actual pattern (maybe literally the tool) for exposing Semgrep to our
agents as a structured MCP tool call rather than shelling out.


Confirms the moat isn't any single layer — it's EVM-specific reasoning +
PoC proving + low-noise integration, which nobody has assembled yet. This
is now the honest one-line pitch for Hacktron: "I didn't find a shortcut,
I found the gap, and here's the piece I built to fill it."

No repo found benchmarked against EVMbench. AIxCC is the closest
"find-and-prove-real-vulns" comparable but isn't Web3-specific — we're
still the one who has to prove ourselves against EVMbench.


OPEN / NOT YET DECIDED


Memory/knowledge layer implementation (Step 5) — not yet designed
Delivery layer (PR comments, dashboard) — not yet designed
Multi-chain support beyond Solidity/Vyper — deferred
Moat / differentiation beyond "PoC validation + low noise" — needs
sharper articulation before pitching to Hacktron
