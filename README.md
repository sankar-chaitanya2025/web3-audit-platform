DECISIONS.md — one-liner-per-decision log. What we chose, what we rejected, why, tradeoff. Append-only, never edit old entries. This is the file you paste into a new chat if this one dies. (already have this — commit it as-is)
ARCHITECTURE.md — current snapshot of the 5-layer design + status of each layer (not started / designed / built). Unlike DECISIONS.md, this one you do overwrite as things evolve — it's "state," not "history."
RESEARCH.md — raw dumps. Paste your research prompt output here unedited, with a date header. Messy is fine, it's an archive you grep later, not something you read top to bottom.
TOOLSTACK.md — the short, current list of locked tools per layer (Slither, Aderyn, LangGraph, Foundry, etc.) with one-line "why." Basically DECISIONS.md's conclusions, extracted and de-duplicated so you don't have to re-read history to know "what are we using right now."
Rule of thumb:

Something happened / was decided, in time → DECISIONS.md
Current truth about the system, no history → ARCHITECTURE.md / TOOLSTACK.md
Raw material, not yet distilled → RESEARCH.md
