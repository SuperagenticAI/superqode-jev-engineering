# Scope

This repository covers live context selection, evidence retrieval and explicit
enforcement in hosted PiPy. It uses selected Jev engineering principles.

| Decision surface | Relationship to this example |
| --- | --- |
| Model routing | Automatic model/provider/effort switching is not configured. |
| Tool selection | Separate SuperQode discovery/routing features can constrain the available catalogue; this example focuses on context. |
| Context compaction | A Noul probability selects full evidence or a bounded excerpt with a retrieval reference. The full keep/summarize/drop ladder is outside this example. |
| Loop detection | SuperQode has deterministic repeated-call guards; this example does not use a Jev loop classifier. |
| Risk gate | Core has policy, permissions and optional Jev checks. Hosted PiPy checks call/result policy but has no interactive tool approval or OS sandbox. |

## Relationship to the FAQ

- **Typed decisions:** the context policy branches on a Noul probability
  rather than parsing a free-text answer.
- **Where to start:** the coding-agents guide recommends risk gates and tool
  selection. The instructions here cover the separate context-selection path.
- **MCP versus a direct API:** hosted context selection uses a direct
  integration. External coding agents and a Jev MCP server are not configured.
- **Speed and cost:** this example does not establish a latency, price or
  correctness advantage. It is not a benchmark.
- **Stable thresholds:** the targeted release uses a pinned model version and
  fixed policy. Calibration and threshold suitability need task-specific tests.

The coding-agents guide supplied during development comes from an independent
playground that states it is not affiliated with or endorsed by TypeSafe AI.
Use official TypeSafe documentation for API contracts and an official TypeSafe
key for this direct API integration.

## Interpreting results

A successful run can establish that the service returned a decision, the policy
applied a keep/excerpt action, and the retained evidence remains retrievable
under current permissions. It does not establish superior coding quality,
cheaper runs, universal sandbox security or parity with Pi Durable.

A new user turn can invalidate a cached context decision. Saying "continue"
does not guarantee a cache hit. WorkOrder crash recovery is a separate workflow.
