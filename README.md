# AgentBurp

### The web workbench your AI agent actually uses.

**Agent-native · Local-first · Evidence-backed · Token-efficient**

AgentBurp is a planned, installable browser and web inspection workbench for **Claude Code, Codex, and compatible coding agents**. It gives an agent the instruments a developer or pentester expects: a browser, interception proxy, traffic history, site map, inspector, comparer, controlled replay, and persistent project evidence.

Its core is **WebCore**: a local representation of the application that stores requests, responses, pages, sessions, and their relationships. Instead of repeatedly reading raw traffic and full pages, the agent works with compact references, targeted queries, and verified changes.

**Your agent reasons. AgentBurp captures, remembers, retrieves, and executes supported operations.** An optional small local model helps select relevant evidence; it does not replace the proxy or your main coding agent.

> **Status: final-product blueprint, not a released product.** Everything described as an AgentBurp capability is planned. Commands, tool names, directory layouts, and examples are proposed interfaces. There is no published installation package, validated compatibility matrix, trained AgentBurp model, or measured token-saving claim in this README.

---

## Contents

[Vision](#vision) · [Installation experience](#installation-experience) · [The workbench](#the-workbench) · [WebCore](#webcore-the-applications-working-memory) · [Local intelligence](#webcore-local-optional-local-intelligence) · [Token efficiency](#token-efficiency-is-a-product-requirement) · [Browser interaction](#a-different-way-for-agents-to-use-the-browser) · [Agent interface](#agent-interface) · [Architecture](#architecture) · [Privacy](#privacy-trust-and-control) · [Evaluation](#how-we-will-measure-success) · [Roadmap](#development-roadmap)

## Vision

**An agent should use AgentBurp the way a developer or pentester uses a web workbench—not operate a human dashboard by taking screenshots of its tables.**

The intended experience is simple:

> Install AgentBurp in a project, connect your existing coding agent, and give it a persistent, inspectable web environment that it can work with without flooding its context.

A developer could ask why a local settings page fails, inspect the associated request, change the application code, and compare the next recording. A pentester could organise an authorised assessment's captured evidence, inspect unusual responses, compare known sessions, and prepare human-reviewed checks and report material.

The product should support the entire **observe → inspect → understand → review an action → verify → retain evidence** loop, rather than stop at returning a browser screenshot or a proxy log.

### What AgentBurp is—and is not

| AgentBurp is | AgentBurp is not |
| --- | --- |
| A tool your existing coding agent uses | Another chatbot you must switch to |
| A browser and HTTP inspection workbench | Primarily a dashboard for watching the AI itself |
| A local evidence store and retrieval runtime | A requirement to route your model-provider traffic through another service |
| An agent-native alternative for defined Burp/ZAP-style workflows | A claim of complete feature parity with every existing proxy and extension |
| A deterministic core with optional local intelligence | An LLM expected to implement TLS, preserve network evidence, or enforce permissions |

### The intended end state

For supported daily workflows, an agent should be able to work through AgentBurp **without separately operating Burp or ZAP**. A mature capture engine can still run underneath; the product does not need to reinvent network interception.

Existing integration already matters: PortSwigger publishes a Burp MCP server, and ZAP exposes an API. Connecting an agent to proxy tools is therefore not, by itself, the differentiator.[^burp-mcp][^zap-api]

The differentiation we want to earn is **persistent application memory, precise evidence retrieval, state-aware browser operation, and lower total model usage at equal or better task quality**.

---

## Installation experience

The final product should be usable in three ways:

| Mode | Intended experience |
| --- | --- |
| **Agent integration** | Connect a local MCP server and install a small companion skill for the selected coding client. |
| **Project installation** | Initialise a working directory; keep project-specific state and evidence under `.agentburp/`. |
| **Human workbench** | Open an optional local UI to inspect the same records, review actions, and take over the managed browser. |

A user-level runtime may serve multiple projects, but each project must have an explicit identity, isolated credentials, and its own evidence boundary. Installing globally must not silently share project data.

### Proposed setup

The following illustrates the desired experience. **These are not currently executable installation instructions.**

```text
agentburp init .
agentburp connect claude-code --project .
agentburp connect codex --project .
agentburp doctor
agentburp ui
```

The user connects whichever client they use; connecting both is optional. Distribution format and package names remain undecided.

The installer should preview changes, merge existing configuration, obtain approval, record what it created, and support clean disconnection. It should not overwrite a user's agent configuration or inject this entire README into every conversation.

**MCP exposes the tools. The skill teaches the workflow. The runtime does the work.** A skill alone is not a proxy, browser, or database.

### Integration targets

Codex documents local MCP connections and trusted-project `.codex/config.toml` configuration; repository skills use `.agents/skills/`.[^codex-mcp][^codex-skills] Claude Code documents project MCP configuration in `.mcp.json` and project skills under `.claude/skills/`.[^claude-mcp][^claude-skills]

These are the planned integration surfaces, not a claim that AgentBurp has already been tested with those clients. Client versions and supported transports must be published with releases.

The first target is **local coding clients**. Cloud-hosted agents require a separately deployed, authenticated runtime or a supported remote connection; they cannot be assumed to reach a developer's laptop loopback interface.

A companion skill can encourage the agent to choose AgentBurp. It cannot guarantee that every native browser, shell command, or network operation in the host will pass through it.

---

## The workbench

These are the intended final-product capabilities. Availability will depend on the selected adapter and release stage.

| Area | Planned capabilities |
| --- | --- |
| **Proxy and interception** | Capture supported HTTP(S) exchanges; inspect messages; pause supported flows; review edits; preserve original and forwarded versions. |
| **Traffic history** | Search and filter by origin, route, method, status, time, session, headers, body fields, labels, and associated browser actions. Save views and bookmarks. |
| **App Atlas / site map** | Organise observed endpoints, pages, links, forms, route variants, parameter locations, and response shapes. Distinguish observed, extracted, inferred, excluded, and unvisited entries. |
| **Inspector and decoder** | Inspect supported structured bodies, headers, cookies, encodings, and nested values locally, while preserving the original captured representation. |
| **Comparer** | Compare headers, JSON, text, schemas, redirects, streams, and page observations. Offer raw and normalised views with visible transformation rules. |
| **Repeater** | Create editable drafts from captured exchanges, show changes, resolve approved session references, dispatch reviewed development checks, and compare the results. Opening or editing a draft never sends it. |
| **Sessions and identities** | Isolate operator-provided accounts and browser profiles; track expiry and identity changes; compare captured behaviour without mixing credentials. |
| **Browser** | Managed tabs, semantic controls, scoped navigation, selected page regions, visual evidence, console events, downloads, and manual takeover. |
| **Breakpoints** | Pause supported browser actions or message dispatch and inspect the actual operation before continuing. Preserve a record of edits and approval decisions. |
| **Streams** | Inspect supported WebSocket and server-sent event messages with timestamps, sequence information, bounded buffers, and explicit gaps. |
| **Passive review** | Apply documented rules to recorded material, including configuration, cookie attributes, schema changes, and suspected sensitive-data exposure. Produce review signals with evidence—not automatic vulnerability verdicts. |
| **Local test lab** | Use response fixtures, reviewed variations, resettable workflows, and regression assertions to investigate development behaviour without repeatedly involving the main model. |
| **Evidence and reporting** | Preserve exact excerpts, source references, annotations, comparisons, limitations, and review status. Export selected, redacted bundles and report drafts. |
| **Developer context** | Link requests to operator-supplied logs, traces, source locations, and code revisions. Keep observed links separate from inferred correlations. |
| **Human-agent collaboration** | Share history, browser state, notes, drafts, and approvals between the agent and the optional UI. No separate investigation copy is required. |
| **Extensions** | Add versioned capture adapters, parsers, renderers, importers, exporters, passive checks, and local fixture integrations through restricted interfaces. |

### Familiar instruments, different interaction

A human can open History and send a request to Repeater. An agent should be able to request the same operation through a typed interface, receive a small evidence-backed result, and leave a reviewable record.

The human UI is a companion—not the only API and not something the agent should have to navigate to read its own traffic.

### Optional AI-application inspection

Later inspectors may add MCP message timelines, tool-result inspection, RAG/context provenance, and review of AI-enabled applications. Controlled prompt-injection and robustness experiments belong in isolated local fixtures.

These extend the workbench. They do not turn the core product back into a model-provider gateway or a platform whose primary purpose is monitoring Claude or Codex internals.

---

## WebCore: the application's working memory

WebCore is the local runtime that makes captured web activity addressable, searchable, and reusable. **It is useful without any local model.**

Instead of representing an investigation as an ever-growing conversation, WebCore maintains structured objects and relationships.

### An object-based application representation

| Object | Example handle | Meaning |
| --- | --- | --- |
| Endpoint | `E17` | An observed method, origin, and route identity |
| Exchange | `R82` | One captured request/response occurrence |
| Page state | `P6` | A versioned browser observation |
| Control | `C9` | An actionable element within a particular page state |
| Session | `S2` | A scoped identity/profile reference, not its credentials |
| Action | `A14` | A proposed or executed operation and its receipt |
| Difference | `D4` | A comparison of specified recorded objects |
| Evidence bundle | `B3` | A curated selection of records, excerpts, and limitations |

The short handles are local aliases. Canonical references must include project/run identity and the relevant object version. A new agent must be able to resolve a handle before relying on it.

Illustrative relationships:

```text
P6 contains C9
A14 used C9 in session S2
A14 is associated with captured exchange R82
R82 belongs to endpoint E17
D4 compares R82 with R91
B3 contains D4 and its supporting excerpts
```

Instrumented associations and timing-based correlations must have different labels. A request occurring after a click does not prove that the click caused it.

### Store once, inspect many ways

Keep captured artifacts locally, subject to retention and privacy settings. Build indexes and derived views without repeatedly sending the underlying content to the model.

The same exchange can support a history query, a schema view, a header comparison, a browser action receipt, and a report excerpt. Those views point back to the recorded source rather than creating unrelated copies of the truth.

Body deduplication must retain every occurrence's timestamp, session, headers, and provenance. Similar route shapes must not erase meaningful differences between origins, tenants, identities, values, or encodings.

### Freshness and provenance

Every derived observation should record its source, capture time, app/run identity, transformations, and completeness. Separate:

- **Captured evidence:** what the instrument recorded.
- **Deterministic derivation:** what a named parser or comparison computed.
- **Interpretation:** what an agent or optional local model inferred.

An older conclusion is not evidence of current application behaviour. Browser controls expire with state changes; reusable memory must retain enough context to detect stale assumptions.

### Not a new universal tokenizer

`E17` is an object reference, **not automatically one token in Claude, Codex, or another model**. The model still needs enough information to understand what it points to.

The practical innovation is a compact application representation and query interface—not pretending that renaming URLs changes a closed model's vocabulary. Bespoke tokenisation or specialist model training remains a separate research direction, not a dependency for the product.

---

## WebCore Local: optional local intelligence

The long-term product may include a small, locally executed selector that helps decide **which evidence to read**, rather than generating another long analysis of everything captured.

The main coding agent handles the task. Deterministic code handles storage, parsing, queries, and permissions. The local selector proposes relevant objects and views.

### Three development levels

| Level | Role | Requirement |
| --- | --- | --- |
| **Deterministic core** | Exact filters, indexing, field selection, structural comparison, grouping, and cursors | Required; the whole workbench must function in this mode |
| **Optional retrieval models** | Embeddings and reranking for task-relevant evidence selection | Add only when evaluation demonstrates a useful quality/latency trade-off |
| **Specialist selector research** | Learn to choose object handles, evidence fields, and observation modes from reviewed examples | Experimental; requires its own dataset, evaluation, and release decision |

Qwen publishes embedding and reranking models, including a 0.6B reranker, which makes that family a candidate for evaluation—not a proven AgentBurp backend or hardware recommendation.[^qwen]

Pointer Networks are an existing research example of selecting positions from variable-sized inputs. They are conceptual inspiration for choosing from a changing set of recorded objects, not evidence that a particular architecture will work for this product.[^pointer]

### A selector, not a second chatty agent

A proposed selector output might be:

```json
{
  "selected": ["R82", "R91"],
  "view": "json_diff",
  "fields": ["stock", "availability"],
  "needs_expansion": false
}
```

This is an illustrative contract, not an implemented model response. The runtime validates object existence, access, field availability, and the requested view before returning any evidence.

The selector may rank candidate records, suggest a useful page region, or choose between a structured observation and a screenshot crop. It must not invent endpoints, silently discard inconvenient evidence, approve itself, or independently send network requests.

### Training and fallback principles

Training, if pursued, should use explicitly approved, sanitised examples with reviewer-labelled evidence relevance and known task outcomes. Customer recordings must not automatically become training data. Split evaluation by application and unseen route patterns, not only by random records from the same app.

Evaluate rare headers, ambiguous pages, stale state, capture gaps, and cases where ranking hides the important clue. A selector must be able to abstain. Exact queries and authorised source expansion remain available when ranking is unhelpful or wrong.

**The model is an optimisation layer. It is never the evidence store or the security boundary.**

---

## Token efficiency is a product requirement

The aim is **less total model usage per correctly completed task**, not merely shorter-looking JSON.

Filtering data before it reaches model context is an established efficiency pattern.[^efficient-mcp] AgentBurp's proposed implementation applies that idea to recorded web traffic, browser state, and evidence retrieval.

### Keep bulk data outside the conversation

```text
Captured traffic + browser observations
                 │
                 ▼
           Local evidence store
                 │
                 ▼
     Query / project / compare / rank
                 │
                 ▼
       Bounded evidence-backed result
                 │
                 ▼
          Existing coding agent
```

Do not first send the full recording to the main model and then ask it to summarise what it just read.

### Planned efficiency mechanisms

| Mechanism | Intended behaviour |
| --- | --- |
| **Progressive detail** | Return an index first; expand to structure, selected evidence, and captured source only as needed. |
| **Local field projection** | Extract selected JSON fields, headers, text ranges, and aggregates before delivery. |
| **Delta-first results** | Send changes relative to a known baseline instead of repeating the complete state. |
| **Recorded-evidence reuse** | Answer from the existing capture when the task does not require a fresh observation. |
| **Reversible grouping** | Collapse repetitive traffic in views while preserving every occurrence and meaningful difference. |
| **Adaptive browser observations** | Choose structured controls, selected DOM regions, screenshot crops, or full visual evidence according to the task. |
| **Small tool schemas** | Expose a compact typed core; load specialist references and capabilities only when needed. |
| **Local batches** | Run bounded, read-only analysis steps over recordings locally and return one useful result. |
| **Reviewed routines** | Reuse known development navigation steps with prerequisites, outcome checks, and explicit stop conditions. |
| **Durable project memory** | Resume from a scoped brief and evidence references rather than reloading the entire conversation. |
| **Per-agent delivery state** | Track what each connected agent has received; avoid assuming every agent knows the same baseline. |
| **Output budgets** | Respect requested result sizes with visible omissions, pagination, and an expansion path. |

### Evidence before compression

A response envelope should identify its source revision, matching count, returned count, redactions, omissions, capture gaps, and continuation cursor when relevant. Never drop those disclosures just to fit a smaller budget.

If a conversation restarts or its baseline is lost, return a compact checkpoint before sending deltas. A patch against forgotten context is not efficient information.

Raw captured data, decoded data, redacted views, and generated summaries are different representations. Preserving a source does not make a summary lossless. Where privacy settings exclude capture or retention, report that limitation instead of promising that all raw content remains available.

### What the product cannot promise

AgentBurp cannot erase an existing host conversation, change a closed model's tokenizer, control private model reasoning, or guarantee a provider billing reduction. Its control is over its own tools, observations, local computation, and delivered results.

A local model still consumes compute and can add latency. Ten tiny retrieval calls may be worse than one well-chosen larger result. Benchmarks must count these trade-offs.

---

## A different way for agents to use the browser

The proposed browser workflow is **state-aware, evidence-linked, and outcome-checked**, rather than an endless sequence of unconnected observations.

### State-bound controls

An agent acts on a control reference associated with a known page state. The runtime checks whether that reference is still valid before acting. Changed pages, ambiguous matches, and expired sessions require a new observation rather than a guessed click.

### Observation receipts

A supported operation returns a small receipt containing the executed action, observed result, relevant page changes, associated exchanges, and limitations.

A successful click is not proof that a save succeeded. The receipt should distinguish browser execution from the application outcome and state which outcome checks actually ran.

### Browser Twin

Show the live or recorded page beside the exact structured observation delivered to the agent. Cross-link controls, actions, exchanges, and evidence excerpts.

The operator can see the difference between **captured**, **delivered**, **omitted**, **redacted**, and **unavailable** information.

### Context Lens

Inspect what AgentBurp sent to each agent, why it selected that view, which baseline it used, and what can be expanded. This is a view of AgentBurp's own data delivery—not access to model-private reasoning or unrelated tools.

### Teach Once

Record an ordinary human development workflow and turn it into a proposed reusable routine. Review its allowed destinations, inputs, identity, prerequisites, result checks, and stop conditions before reuse.

The runtime can execute the approved deterministic steps and return an outcome receipt, reducing repeated planning and observation. An unexpected page or consequential operation stops the routine.

### Evidence bundles

Package the minimum useful source material for a question, review, or handoff. A bundle contains references, selected excerpts, freshness, and limitations—not an ungrounded summary that replaces the source.

A second agent can read the same bundle without receiving the whole capture. Project permissions still apply, and short references must remain resolvable.

### BranchLab — research

Compare alternative reviewed workflows in isolated, resettable local fixtures. Keep each branch's sessions, outputs, and evidence separate.

A browser snapshot does not roll back a server. Executable branching therefore requires explicit backend reset support; ordinary website sessions cannot be advertised as reversible simulations.

### Shadow review — later

Let additional agents inspect scoped recordings without live browser control. Compare their conclusions against source evidence and fixture outcomes, not by majority vote.

One executor controls a live tab at a time. Multi-agent access must not create concurrent, conflicting clicks or duplicate consequential actions.

---

## Agent interface

The final interface should be **small enough to learn, precise enough to trust, and complete enough to avoid repeated raw dumps**.

Proposed tools—not implemented APIs:

| Tool | Purpose |
| --- | --- |
| `workspace_status` | Read project identity, scope, capabilities, freshness, capture health, and current browser ownership. |
| `traffic_query` | Filter, group, and project recorded exchanges under a result budget. |
| `evidence_read` | Expand selected fields or ranges from known records and bundles. |
| `evidence_diff` | Compare explicit artifacts and disclose any normalisation. |
| `browser_observe` | Retrieve a page view or a baseline-aware change set. |
| `browser_act` | Request a supported browser action tied to an observed state; the runtime applies scope and approval rules. |
| `replay_prepare` | Create a reviewable draft without dispatching traffic. |
| `workspace_checkpoint` | Save or retrieve a compact, evidence-linked project handoff. |
| `evidence_export` | Prepare an explicitly selected, redacted export for review. |

Specialist capabilities should use discoverable typed extensions, not an unrestricted `execute_anything` escape hatch. Runtime policy evaluates the actual operation; naming a tool “read” does not make its effects harmless.

### Example: inspect a development regression

All identifiers and values below are fictional examples, not benchmark results.

User:

> “Use AgentBurp to compare the product response before and after my local change. Show the relevant difference and keep the source available.”

The agent selects the two existing records. WebCore compares them locally and returns a compact view:

```json
{
  "workspace": "local-demo",
  "revision": 42,
  "endpoint": {"ref": "E17", "method": "GET", "path": "/api/products/{id}"},
  "sources": ["R82", "R91"],
  "diff": {"ref": "D4", "added_fields": ["stock"]},
  "selection": "response.json",
  "other_sections": "not_compared",
  "capture_gaps": [],
  "truncated": false
}
```

The agent can request `stock` values, relevant headers, or an expanded source view. “Not compared” is deliberately different from “unchanged.” No new network request is needed just to inspect an existing recording.

### Companion skill

The installed skill should teach a short habit:

```text
Start with workspace status and a compact query.
Reuse recorded evidence before requesting another live observation.
Ask for selected fields, differences, or page regions before full artifacts.
Resolve object references and check freshness.
Keep capture gaps and unknowns visible.
Support factual conclusions with evidence references.
Separate inspection from dispatch and respect runtime approvals.
Load specialist documentation only when needed.
```

Do not preload the complete feature catalogue into every agent session. Keep longer examples and schemas in supporting references.

---

## Architecture

```text
             Human
               │
     Claude Code / Codex / compatible agent
               │
       MCP adapter or CLI
               │
               ▼
      AgentBurp project runtime ◄──────── Optional local UI
               │
      Scope · identity · approvals
               │
       ┌───────┼──────────────┐
       ▼       ▼              ▼
    Browser  Capture      Reviewed replay /
    adapter  adapter      local fixture runtime
       │       │              │
       └───────┴──────┬───────┘
                      ▼
               Normalised events
                      │
                      ▼
              WebCore evidence store
                      │
         Index · query · diff · state graph
                      │
             Optional local selector
                      │
                      ▼
       Budgeted, source-backed tool results
```

The host agent's model-provider connection is outside this path and remains unchanged.

### Implementation direction

The proposed starting stack is **Python for the local runtime and parsers, SQLite for indexed metadata, a filesystem artifact store, and an optional local web UI**. Browser and capture engines remain adapters behind a stable event contract. Choose frameworks and inference runtimes through implementation tests rather than lock the product to an untested stack.

Playwright documents browser network observation and proxy configuration, making it a candidate managed-browser adapter.[^playwright] mitmproxy documents interception and replay features, making it a candidate default capture engine.[^mitmproxy]

The standalone path should not require a running Burp or ZAP instance. Optional adapters and recording imports can support existing workflows later. Do not begin by chaining multiple proxies or making every backend mandatory.

### Proposed workspace layout

```text
my-project/
├── .agentburp/
│   ├── project.toml           # Reviewed project settings and scope
│   ├── state/                 # Index, graph, revisions, cursors
│   ├── artifacts/             # Captured evidence; not committed
│   ├── profiles/              # Sensitive browser state; not committed
│   ├── recipes/               # Reviewed development routines
│   ├── notes/                 # Conclusions with evidence and freshness
│   ├── evaluations/           # Local benchmark outputs
│   ├── exports/               # Reviewed/redacted export candidates
│   └── install-manifest.json  # Installer-owned changes
├── .mcp.json                  # Optional Claude Code project connection
├── .claude/skills/agentburp/   # Optional Claude Code skill
├── .codex/config.toml         # Optional trusted-project Codex connection
├── .agents/skills/agentburp/   # Optional Codex skill
└── ...your existing code...
```

The client configuration locations follow the cited documentation; the `.agentburp/` layout is a proposal.[^codex-mcp][^codex-skills][^claude-mcp][^claude-skills]

The installer should exclude sensitive state from Git and explain shareable configuration separately. Model weights and browser binaries may use an explicit shared cache; project data must not silently move there.

`doctor` should show workspace resolution, client connection, capture coverage, missing dependencies, model availability, and unsupported capabilities. Uninstall should remove only managed entries and explicitly report retained data, shared dependencies, credential-store entries, and certificate trust changes.

---

## Privacy, trust, and control

**Local-first does not mean nothing ever leaves the machine.** Anything AgentBurp returns to a cloud-backed coding agent may enter that agent's model context. Redaction and delivery policy must run before that boundary.

| Requirement | Intended control |
| --- | --- |
| **Project isolation** | Bind each connection to an explicit workspace. Separate credentials, profiles, evidence permissions, and concurrent browser ownership. |
| **Local control protection** | Use loopback defaults, authenticated non-stdio interfaces, origin checks, and protected privileged operations. |
| **Credential handling** | Keep secrets behind session-specific references. Do not include them in ordinary outputs or silently forward them across origins. |
| **Untrusted content** | Treat captured pages, responses, imported files, and tool output as data. Their text cannot grant new permissions or change project scope. |
| **Action boundaries** | Separate offline analysis, ordinary navigation, and consequential operations. Check actual destinations and effects; HTTP methods alone are insufficient. |
| **Interception setup** | Prefer dedicated test profiles. Preview certificate-trust changes and provide explicit reversal instructions. |
| **Evidence integrity** | Preserve originals separately from edits and derivations. Record lineage and hashes without claiming that a locally editable store is tamper-proof. |
| **Resource bounds** | Limit storage, parser/decompression work, streaming buffers, browser resources, and extension capabilities. |
| **Retention and export** | Make retention configurable, redact exports, and require selection of the destination and included artifacts. |

Captured content must not execute inside the workbench UI. Extension renderers and importers need the same distrust boundary as network content.

Capture is never assumed complete. Publish adapter-specific behaviour for TLS pinning, cache hits, service workers, streaming, unsupported protocols, and traffic outside the managed browser/proxy path. A missing record means **not observed in this capture**, not that the event never occurred.

Active security work is operator-directed. Automated variation and prompt-injection experiments belong in isolated, resettable local fixtures. Autonomous third-party exploitation is not a product mode.

---

## How we will measure success

**Primary question: does the agent complete the same task correctly with less total model usage and acceptable local overhead?**

Compare AgentBurp against both a straightforward full-output baseline and a competent existing browser/proxy workflow. Compare deterministic WebCore against optional model-assisted retrieval separately.

| Metric | What must be reported |
| --- | --- |
| **Task correctness** | Independently verifiable outcomes, unsupported conclusions, and failed tasks—not just fluent answers |
| **Evidence recall** | Whether the relevant fields, headers, page states, and capture limitations were found |
| **Total model usage** | Prompts, tool schemas, results, retries, summaries, and extra model calls, where observable |
| **Tool-result usage** | Tokens delivered by AgentBurp, with the tokenizer or estimation method stated |
| **Cache accounting** | Cached and uncached usage separately where available; no assumed billing equivalence |
| **Retrieval effort** | Tool calls, expansion requests, repeated observations, and unnecessary network actions |
| **Latency and resources** | Cold start, indexing, inference, end-to-end latency, RAM, CPU/GPU use, and disk growth |
| **Resume reliability** | Whether a fresh agent can recover state and avoid stale references |
| **Selection quality** | Local-model ranking errors, abstentions, and fallback behaviour |

For comparable tool results:

```text
tool_result_reduction = 1 - agentburp_delivered_tokens / baseline_delivered_tokens
```

This formula measures tool-result reduction, not automatically total-task savings, cost reduction, or improved accuracy. A zero denominator or missing telemetry makes that comparison unavailable. Increased usage should be reported, not hidden.

The fixture suite should include small tasks where tool overhead may dominate, large repetitive captures, rare meaningful differences, partial recordings, stale browser state, expired sessions, and application changes across revisions.

Publish client/model versions, fixture definitions, success rates, resource usage, and failures alongside any efficiency number. **No “90% savings,” “10× faster,” or universal coverage claim without reproducible evidence.**

---

## Development roadmap

The roadmap builds toward the final product without making custom model training a prerequisite for a useful tool.

| Stage | Deliverable | Exit criterion |
| --- | --- | --- |
| **1. Installable evidence core** | Workspace contract, reversible setup, one capture adapter, local store, query/read/diff, compact MCP/CLI tools | An agent diagnoses a known local-app issue from selected evidence without ingesting the whole recording. |
| **2. Agent-native workbench** | Both target client integrations, App Atlas, inspector, session separation, replay drafts, evidence exports | Supported agent workflows work headlessly, and every interpretation can be traced to recorded sources. |
| **3. State-aware browser** | Managed observations, state-bound controls, action receipts, deltas, checkpoints, manual takeover | A fresh agent resumes the project correctly, and browser success is distinguished from application outcome. |
| **4. Shared human workbench** | History UI, comparer, supported breakpoints, Browser Twin, Context Lens, review/approval views | Human and agent inspect the same objects and can see exactly what was delivered, changed, or executed. |
| **5. Measured efficiency** | Result budgets, adaptive views, reusable routines, evidence bundles, reproducible benchmarks | Reduced total usage holds up against competent baselines without unacceptable evidence loss or latency. |
| **6. Optional local intelligence** | Evaluated embedding/reranking adapter, selection provenance, abstention, model-off fallback | Local selection improves a defined benchmark enough to justify its resource cost. |
| **7. Extensible final workbench** | Stream inspectors, passive rule packs, import/export adapters, local test lab, extension SDK | Capabilities have documented boundaries, fixtures, and stable integration contracts. |

### Longer-term research

BranchLab, specialist object-selection models, adaptive visual observation, cross-agent evidence handoffs, and optional AI-application inspectors remain independent research tracks. Each needs an explicit hypothesis and evaluation before becoming a product promise.

The goal is not to delay useful history and inspection tools until every research idea works. **The deterministic workbench should remain a complete, useful product even if a specialist local model never outperforms simple retrieval.**

## Project boundaries and open decisions

No promise of universal capture, full Burp/ZAP parity, automatic vulnerability confirmation, perfect prompt-injection prevention, or guaranteed token savings is implied.

Licensing, distribution, supported operating systems, inference backends, release policy, and final naming remain open decisions. AgentBurp is a working name and is not affiliated with Burp Suite, PortSwigger, ZAP, OpenAI, Anthropic, or Qwen.

**The destination: install one workbench, keep your existing agent, and give it a persistent web environment it can inspect precisely—without carrying the entire application in its conversation.**

---

## Technical references

Primary sources consulted on 26 September 2026. They document external capabilities and research inspirations, not implemented AgentBurp features. Recheck integration details against the versions selected for development.

[^codex-mcp]: OpenAI, [Model Context Protocol](https://developers.openai.com/codex/mcp): local MCP connections and project-scoped configuration.
[^codex-skills]: OpenAI, [Build skills](https://developers.openai.com/codex/skills): repository skill locations and progressive disclosure.
[^claude-mcp]: Anthropic, [Connect Claude Code to tools via MCP](https://code.claude.com/docs/en/mcp): project MCP configuration and client integration.
[^claude-skills]: Anthropic, [Extend Claude with skills](https://code.claude.com/docs/en/skills): project skills and supporting files.
[^burp-mcp]: PortSwigger, [MCP Server for Burp](https://github.com/PortSwigger/mcp-server): existing agent integration with Burp.
[^zap-api]: ZAP, [API](https://www.zaproxy.org/docs/desktop/start/features/api/): external programmatic access.
[^efficient-mcp]: Anthropic, [Code execution with MCP](https://www.anthropic.com/engineering/code-execution-with-mcp): processing and filtering tool results outside model context.
[^qwen]: Qwen, [Qwen3 Embedding](https://qwenlm.github.io/blog/qwen3-embedding/): embedding and reranking model families, including the 0.6B reranker.
[^pointer]: Vinyals, Fortunato, and Jaitly, [Pointer Networks](https://arxiv.org/abs/1506.03134): selection over variable-sized inputs.
[^playwright]: Playwright, [Network](https://playwright.dev/docs/network): browser network observation, routing, and proxies.
[^mitmproxy]: mitmproxy, [Features](https://docs.mitmproxy.org/stable/overview/features/): interception and replay capabilities.
