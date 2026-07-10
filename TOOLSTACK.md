Tool Stack (current state — overwrite as decisions change)

LayerTool(s)Why (one line)Ingestion/Indexingcrytic-compile, Slither, Aderyn, codeql-solidityDeterministic code-property-graph backbone, not LLM-drivenStatic scanSlither + Aderyn (union) + Semgrep (custom rules)Cut search space cheaply before any LLM touches codeStatic scan (whitebox mode only)Mythril / ManticoreToo slow for per-PR CI, fine for full-repo pentest modeOrchestrationLangGraphAudit trails, checkpointing, branching control; provider-agnosticTool exposure to agentssemgrep/mcp patternStructured tool calls instead of shelling outPoC validationFoundry fork testsEvery High/Critical must prove fund loss or get demotedBenchmark/credibilityEVMbench (OpenAI + Paradigm)Falsifiable recall/exploit-success number, not a claimMemory/knowledge layerTBDPersist triage feedback per repo — not designed yetDelivery (PR comments, dashboard)TBDNot designed yet

Explicitly rejected


GBrain — prose/entity knowledge graph, zero code semantics, wrong domain
CrewAI — 3x token overhead vs LangGraph, opaque at production complexity
Forking a single existing security-swarm repo — none exist as a full
turnkey package (see RESEARCH.md 2026-07-10). Pattern-borrow from three,
build the glue ourselves.


Patterns borrowed (not forked) from research


Team-Atlanta/aixcc-afc-atlantis — service boundaries, task routing shape
samuelberston/vuln-agent — Discovery -> tool-routing -> parallel analysis
-> Triage staged pipeline shape
