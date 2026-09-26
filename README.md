
Oudere berichten worden geladen…
ik wil de tool meer hebben dat je het kan installen op je claude code of codex of in je working directory en dat ie het vooral gebruikt als pentesters en devs burp gebruiken
Gestopt met nadenken
ik wil de tool meer hebben dat je het kan installen op je claude code of codex of in je working directory en dat ie het vooral gebruikt als pentesters en devs burp gebruiken. ik wil dat ie daarmee ook de ai veel en veel en veel meer token efficient maakt
Nagedacht gedurende 3m 51s

Precies: de AI gebruikt jouw tool zoals jij Burp gebruikt. Geen extra chatbot of modelproxy.

    Installatie: lokale MCP-server + kleine skill voor Claude Code/Codex, met projectdata in .agentburp/. Die clients ondersteunen zulke MCP- en skillintegraties.
    Werkbank: browser, history, filters, comparer, breakpoints en gecontroleerde replay.
    Token-efficiëntie: captures lokaal opslaan en verwerken. Alleen relevante velden, verschillen en bewijsreferenties naar de AI—meer details uitsluitend wanneer nodig. 

Niet eerst alles naar de AI sturen en daarna samenvatten, maar vóór die stap lokaal selecteren. Dat wordt de kern; hoeveel tokens het daadwerkelijk bespaart, moet je meten.
README.mdDocument
kan ik een soortvan local model ervan maken zoals jev en qwen enzo maardan dat zijn tokens url endpoints zijn of gewoon nieuwe innovatieve manier
Nagedacht gedurende 2m 10s

Ja — een klein lokaal model dat met endpoints, pagina’s en acties werkt in plaats van steeds lange antwoorden te schrijven. Jevs gestructureerde beslissingen zijn hiervoor interessante inspiratie.
Mijn voorstel: AgentBurp WebCore

    Webgeheugen: bewaar verkeer, paginastatus en relaties lokaal. Geef objecten korte verwijzingen: E17 = endpoint, R82 = opgeslagen response.
    Lokaal selectiemodel: laat het model relevante objecten aanwijzen, in plaats van URLs en requests opnieuw uitschrijven. Pointer networks zijn een bestaand principe voor kiezen uit een veranderende verzameling objecten.
    Compacte communicatie: de grote AI krijgt alleen relevante feiten, verschillen en bewijsverwijzingen. Details haalt hij op wanneer nodig. Lokaal verwerken vóór informatie naar het model gaat, is een bestaande manier om contextgebruik te verminderen. 

Voorbeeld van jouw toekomstige interface:
Vraag: wat veranderde aan de productresponse?

Endpoint: E17 — GET /api/products/{id}
Verschil: veld "stock" toegevoegd
Bewijs: R82 → R91

Belangrijk: E17 is een verwijzing, niet automatisch één modeltoken. Echte eigen tokens toevoegen kan, maar vereist aanpassingen aan tokenizer én model; daarmee kent het model nog niet vanzelf hun betekenis.

Mijn bouwadvies: begin met geheugen + filters + een bestaande lokale ranker, bijvoorbeeld Qwen3-Reranker. Train later een eigen selectiemodel op beoordeelde voorbeelden. Qwen biedt zulke embedding- en rankingmodellen al aan.

De innovatie: de grote AI hoeft niet alles te lezen om ermee te kunnen werken. Meet daarbij niet alleen tokenbesparing, maar ook hoeveel relevante informatie je mist.
zou dat zap en burp voor agents kunnen vervangen
Nagedacht gedurende 2m

Ja — voor de dagelijkse agent-workflows. Maar als complete tool, niet als alleen een lokaal model.

Ik zou drie onderdelen combineren:

    Proxy-engine: bijvoorbeeld mitmproxy voor verkeer onderscheppen, aanpassen en opnieuw versturen. Dan hoef je die basis niet zelf te bouwen.
    AgentBurp-geheugen: bewaart endpoints, sessies, requests en responses lokaal. De agent vraagt informatie op via korte verwijzingen.
    Lokaal model: helpt relevante informatie selecteren. Originele data blijft altijd opvraagbaar, ook wanneer het model iets verkeerd inschat.

Daarmee zou de agent alleen AgentBurp gebruiken, zonder Burp of ZAP. Eerst voor history, inspectie, zoeken, vergelijken en replay — niet meteen als vervanging voor hun volledige functionaliteit.

De concurrentie bestaat wel: Burp heeft al een MCP-server en ZAP een programmeerbare API. Alleen “AI kan ermee werken” is dus onvoldoende onderscheid.

Jouw onderscheid wordt verkeersgeheugen + compacte toegang tot bewijs. Lokaal filteren vóór data naar de AI gaat, kan tokengebruik verminderen; daarvoor is eigen modeltraining niet noodzakelijk.

Meet succes in tokens per correct uitgevoerde taak — niet alleen hoeveel tekst je weglaat.
oke schrijf de readme voor de final product die we willen developen

Bereidde README-update voor


AgentBurp
Give your coding agent a web inspection workbench—not a wall of traffic.

Local-first. Agent-operated. Evidence-backed. Token-efficient by design.

AgentBurp is a planned developer and security-review tool that connects to Claude Code, Codex, or another compatible coding agent. Install it for your agent or attach it to a working directory, then let your existing agent use it the way a developer or pentester uses a web interception workbench: inspect traffic, navigate an application, compare responses, prepare controlled replays, and retain evidence.

The agent remains the assistant you already use. AgentBurp supplies the browser, instruments, project memory, and focused observations. You should not need another chatbot, another orchestrator, or a replacement model API connection.

The primary idea is not “a tool for watching AI.” It is “a tool that AI uses to work with web applications.” Inspecting AI-specific applications and MCP interactions is an optional extension, not the prerequisite or primary market.

    Status: product concept / long-term roadmap. All AgentBurp features, commands, files, and APIs below are proposed. There is no installable release, tested compatibility matrix, or measured token-saving result yet. External documentation establishes possible integration mechanisms, not that this project implements them.

The product in one minute

A human operator opens a project and tells their coding agent:

    “Use AgentBurp to inspect the network requests from this local app. Explain why the settings page is failing, compare it with the previous recording, and link the relevant evidence. Keep your context compact.”

AgentBurp keeps the recording locally. The agent receives a short traffic index, requests the relevant response fields, compares selected records, and opens additional evidence only when necessary. A human can open the same workbench and inspect exactly the same records.
Human ──→ Claude Code / Codex / compatible coding agent
                          │
                     MCP or CLI
                          │
                  AgentBurp runtime
                   /      │       \
          Managed browser │   Optional human UI
                   │      │
             Capture engine
                   │      │
          Approved web app│
                          ↓
                 Local evidence store
                          ↓
               Query / filter / diff
                          ↓
              Small, cited tool results
                          ↓
                    Existing agent

The agent's existing model connection stays unchanged. AgentBurp does not need to intercept traffic to OpenAI, Anthropic, or another model provider.

Store the observation once. Retrieve the necessary evidence when needed. Do not paste the recording into every conversation.
Three ways to use it
Planned mode	Experience
Agent integration	Add AgentBurp to Claude Code or Codex through a supported MCP connection and a small companion skill. The agent discovers and calls the workbench's tools.
Working-directory installation	Keep project configuration, runtime metadata, recordings, and reusable notes under .agentburp/. Use the same project from a compatible agent or the CLI.
Human workbench	Open an optional local UI for history, interception, comparison, replay review, and approvals. Human and agent share records rather than separate copies of the investigation.

The core should be headless-first. The UI is a useful companion, not something the model must operate by repeatedly screenshotting a dashboard.
Integration strategy

MCP provides callable tools; the skill teaches when and how to use them; the local runtime performs the work. A skill file alone is not a proxy, database, or browser.

Codex documents local MCP servers, project-scoped .codex/config.toml in trusted projects, and repository skills under .agents/skills/. These are proposed integration surfaces.[^codex-mcp][^codex-skills]

Claude Code documents project MCP configuration in .mcp.json, project skills under .claude/skills/, and plugins that can bundle MCP servers. These are proposed Claude Code packaging surfaces.[^claude-mcp][^claude-skills]

A connector can make tools available, but cannot guarantee that an agent always chooses them. The companion skill should teach an inspect-first workflow. Enforcing use of a particular browser or network path requires separately configured runtime controls; prompt instructions are not enforcement.

The first integrations target local coding clients. A cloud-hosted agent cannot be assumed to reach a laptop's loopback service. Remote deployment, authenticated access, and host-specific restrictions need separate compatibility work.
Proposed installation experience

These commands describe a future interface. They are not working installation instructions and do not identify a published package.
agentburp init .
agentburp connect claude-code --project .
agentburp connect codex --project .
agentburp doctor
agentburp ui

The intended installer should preview changes, obtain approval, merge rather than replace existing configuration, record everything it creates, and support disconnection. Distribution may be a packaged local executable or a host-native plugin; package naming and release channels remain undecided.

doctor should report the resolved project directory, client version, MCP connection, available browser, capture health, scope configuration, and exactly which capabilities are unavailable.
The token-efficiency engine

Token efficiency is a core acceptance criterion, not a marketing add-on. The objective is less model input and fewer unnecessary turns per correctly completed task, without hiding important evidence.

MCP alone does not deliver this. The proposal follows documented patterns of filtering results before model delivery, loading capabilities on demand, and retrieving context only when needed.[^efficient-mcp][^context][^tools]
1. Keep the data plane outside the conversation

Store supported request and response artifacts, browser observations, logs, and annotations in the local project store. Tool responses return record identifiers and focused views, not entire captures.

Raw capture, decoded content, redacted content, and summaries are different representations. Preserve their relationships and capture limitations. “Available locally” does not mean “already read by the model.”
2. Progressive detail instead of full dumps
Detail level	What the agent receives
Index	Stable IDs, methods, paths, statuses, counts, and capture warnings
Structure	Selected headers, parameter locations, body schema, and bounded examples
Relevant evidence	Selected JSON fields, text ranges, or a specific response difference
Fuller artifact	Explicitly requested captured content, still subject to permissions, redaction, pagination, and output limits

Moving between levels should not require another network request when the captured artifact is sufficient.
3. Deterministic local processing first

Use local parsing, indexing, grouping, searching, and structural comparison for routine work. Do not spend an extra model call summarising every response.

Start with typed, read-only query operations and bounded parsers. Later, reviewed analysis recipes may run in a restricted local worker over recordings, with no network access, no arbitrary host filesystem access, and strict resource limits. A query interface must not become an unrestricted shell.

Optional model-generated summaries or embeddings must be separately enabled, attributable, and included in end-to-end usage measurements.
4. Query the evidence, not the entire history

The agent should be able to request:

    “Show the methods and paths of recorded JSON responses with server errors.”

    “Read only the error code and message from response req-018.”

    “Compare these two recorded settings responses, including headers.”

Filtering, field projection, aggregation, and pagination happen locally. Return counts, source IDs, exclusions, and a continuation cursor where appropriate.
5. Delta-first observations

Return the changes since a known browser observation or traffic cursor, rather than resending the whole state. Keep explicit baseline IDs, schema versions, timestamps, and reset indicators.

A newly started or compacted agent conversation may no longer have the baseline. Its first result should include a compact checkpoint, not an unexplained patch against forgotten context. Automatically fall back when the referenced baseline is missing or invalid.
6. Deduplicate without erasing meaningful differences

Store identical captured bodies once when appropriate, while retaining every occurrence's timestamp, session, headers, origin, and event identity. Group repetitive traffic in views, not by silently deleting evidence.

Do not merge records merely because their route templates or response lengths match. Status, identity, tenant, cookie, redirect, body-value, and header differences may matter. Grouping must remain reversible and disclose the original records.
7. Browser observations before screenshots

Prefer task-relevant structured controls and selected page regions when they are sufficient. Use screenshot crops or full images when visual evidence is actually needed. Preserve the ability to inspect layout, canvas content, ambiguous controls, and inaccessible regions.

This is adaptive observation, not a rule that structured text is always more efficient or more accurate than an image. Compare both on representative workflows.
8. Reuse browser state and bounded workflows

Keep session and browser state in the runtime instead of asking the model to recreate it. Reuse operator-reviewed navigation routines for repetitive development tasks, with explicit prerequisites and result checks.

A routine may execute deterministic steps locally and return one outcome receipt. Stop on a changed page, ambiguous control, expired session, or unexpected effect rather than improvising an unreviewed workflow.
9. A compact tool surface

Start with a small set of clearly typed tools. Keep schemas and always-loaded guidance short. Use the host's deferred tool discovery where available; otherwise expose a genuinely small core rather than hiding hundreds of undocumented operations behind a generic execution tool.

Codex skills use progressive disclosure, and Claude Code documents on-demand MCP tool search. These host capabilities complement, but do not replace, compact tool results.[^codex-skills][^claude-mcp]
10. Reusable project memory, not endless chat history

Keep a structured project brief: app version, approved scope, sessions, observed routes, reviewed conclusions, evidence references, unresolved questions, and the last valid observation cursor.

A new agent session retrieves the brief and relevant evidence. It does not reload the entire history. Conclusions must retain scope, freshness, supporting records, and uncertainty; a previous agent's statement is not itself proof.
11. Per-result budgets with visible omissions

Accept a requested output budget and an explicit field selection. Keep results within a documented envelope, returning truncated, omitted counts, source references, and a continuation path instead of silently cutting the end off a body.

Never silently sacrifice approval decisions, capture warnings, source identity, or completeness metadata to make the result smaller. A failed search over a partial capture must not be reported as proof that an event never happened.
12. Shared evidence for multiple agents

Agents working in the same approved project should share artifact IDs and indexed observations, not repeatedly capture or copy the same dataset. Use separate access scopes and per-agent delivery cursors. Only one executor should control a live browser tab at a time.

A short reference saves nothing when the recipient cannot resolve it. Shared handles need explicit project identity, access checks, and freshness metadata.
What “efficient” must not mean

A smaller output is not automatically a better output. Avoid aggressively deleting unusual headers, changing encodings, dropping error details, treating dynamic values as irrelevant, or inventing facts in summaries.

Lossless storage and selective delivery are different things. A summary is lossy even when the underlying captured record remains available. The product should make that distinction visible.

AgentBurp also cannot erase an existing host conversation, compress private model reasoning, or guarantee a larger context window. Its primary control is over its own tool definitions, observations, result size, and local execution.
The workbench: familiar operations, agent-native interfaces

All capabilities below are planned. Active network actions remain separate from offline analysis of a recording.
Area	Planned capabilities
Proxy and interception	Capture supported HTTP(S) traffic; inspect messages; pause supported flows; review edits; preserve original and forwarded versions; show adapter limitations.
History	Search requests, responses, headers, bodies, timestamps, session labels, and annotations; use saved filters and bookmarks.
Site map / App Atlas	Build an inventory from observed traffic and extracted links; connect pages, forms, route groups, parameter locations, and response shapes. Mark observed, extracted, inferred, and excluded entries separately.
Repeater	Prepare an editable copy of a selected exchange; review a diff before dispatch; repeat approved development checks in controlled environments; compare results. Live dispatch is never implied by opening a record.
Comparer	Compare recorded headers, JSON structure, text, redirects, and selected browser observations. Preserve raw differences and identify any normalisation applied.
Inspector / decoder	Inspect supported structured formats and encodings locally, with size limits and original byte references.
Sessions	Separate operator-provided accounts and browser profiles; show expiry and identity changes; keep credentials in runtime-managed storage rather than ordinary tool output.
Streams	Inspect supported WebSocket and server-sent event messages with bounded buffers, sequence information, and explicit gaps.
Browser	Managed tabs, semantic controls, scoped navigation, structured observations, selective screenshots, console events, and manual takeover.
Breakpoints	Pause supported navigation, message dispatch, or observation delivery. Show the actual request or proposed action, not just the model's description.
Passive review	Apply documented rules to already captured content for configuration and data-handling review. Keep review signals separate from verified findings.
Evidence	Stable record IDs, exact excerpts, annotations, redaction metadata, edit history, and sanitised export bundles.
Developer handoff	Link selected application failures to operator-supplied logs, traces, or source references. Mark correlations as observed or inferred.
Regression support	Turn reviewed local-app failures into fixture-backed checks; compare captures before and after a code change.
Extensions	Versioned adapters, local parsers, renderers, exporters, and narrowly scoped review rules.

Automated robustness matrices, prompt-injection experiments, and variation testing belong in resettable local fixtures. The project is not an unattended exploit-finding or attack orchestration system. Capturing a request is not permission to replay it or change its effects.
A different browser workflow

The browser is an instrumented part of the workbench, not the product's only interface. The new workflow should connect what the agent sees to what the application does, while avoiding repeated full-page reads.
Browser Twin

Show the rendered page alongside the structured observation delivered to the agent. Cross-link controls, action records, requests, and relevant evidence. The UI should clearly distinguish information that was captured, delivered, summarised, redacted, or unavailable.
Action-to-request links

Let an agent ask which recorded requests were associated with a selected action. Prefer instrumented links; label timing-based matches as correlations. A request occurring after a click does not prove the click caused it.
Observation receipts

After a supported action, return a bounded receipt: the action, observed outcome, relevant changes, associated record IDs, and capture limitations. A successful click is not proof that a save or update succeeded.
Teach Once

Record a human's ordinary development workflow and propose a small reusable routine. Human review defines allowed destinations, inputs, sessions, prerequisites, result checks, and stop conditions. Reuse should reduce unnecessary navigation and context, not bypass approvals.
BranchLab — research

Compare alternative reviewed workflows against isolated local fixtures. Show branch differences without letting multiple agents modify the same session. Browser snapshots do not roll back a server; executable branches require resettable backend fixtures.
Context Lens

Let the operator inspect exactly what AgentBurp delivered to the agent and why. Show omitted regions, representative examples, redactions, baseline dependencies, and expansion controls. Model-private reasoning and unmanaged tools remain outside this view.
Shadow Mode — later

Give additional agents a recorded, scoped observation to review without granting live browser control. Compare their conclusions against evidence and fixture outcomes, not by majority vote or confidence alone.
Illustrative agent interface

Proposed names and schemas—not implemented tools. The default surface prioritises offline inspection of captured evidence.
Tool	Intended role
workspace_status	Read project identity, run state, scope, capture health, capability limits, and freshness.
traffic_query	Filter, group, and project already captured exchanges under a result budget.
traffic_read	Retrieve selected fields or ranges from a known record.
traffic_diff	Compare specified recorded artifacts with explicit normalisation settings.
browser_observe	Read a current structured observation or a baseline-aware delta.
browser_action	Propose a supported, scoped browser action with expected outcome; enforce runtime policy before execution.
replay_prepare	Create a reviewable replay draft without sending traffic.
evidence_export	Prepare selected, redacted evidence for an approved destination, with export review.

The reviewed runtime handles dispatch and approval separately. Client-visible tool names do not determine whether an action is read-only; the runtime evaluates the actual operation.
Example: find a local application error

Illustrative call:
{
  "tool": "traffic_query",
  "arguments": {
    "run_id": "local-demo-001",
    "filter": {"status_min": 500, "content_type": "application/json"},
    "fields": ["id", "method", "path", "status"],
    "limit": 5,
    "output_budget_tokens": 1200
  }
}

Illustrative result:
{
  "run_id": "local-demo-001",
  "capture_revision": 42,
  "matched": 1,
  "items": [
    {"id": "req-018", "method": "GET", "path": "/api/settings", "status": 500}
  ],
  "body_delivery": "omitted_by_projection",
  "capture_gaps": [],
  "truncated": false,
  "next_cursor": null
}

The agent can then request the error fields from req-018. The raw response stays outside the conversation until a relevant part is requested. These numbers are fictional examples, not measurements.

IDs shown in examples are short aliases. Real references should carry project, run, and artifact identity and remain resolvable across authorised sessions.
Companion skill design

The skill should teach a small working habit rather than preload this entire README:
Use AgentBurp for captured web traffic and instrumented browser inspection.
Start with workspace status and a compact query.
Reuse recorded evidence before making another request.
Request selected fields, differences, or page regions before full artifacts.
Distinguish missing evidence, capture gaps, and negative results.
Cite record IDs for factual conclusions.
Prepare active actions separately and respect runtime approvals.
Use the detailed reference only when an unfamiliar capability is needed.

Keep longer API references and examples in supporting files that load on demand. Do not install hundreds of lines into every project's always-loaded agent instructions.
Architecture
Agent client                            Optional human UI
    │                                          │
MCP adapter / CLI ──────────────→ Project runtime
                                       │
                         Scope + identity + approvals
                                       │
              ┌────────────────────────┼────────────────────┐
              │                        │                    │
        Browser adapter          Capture adapter      Replay review
              │                        │                    │
              └──────────── Normalised events ─────────────┘
                                       │
                        Project-local evidence store
                                       │
                  Index / query / diff / context budget
                                       │
                    Small evidence-backed tool results
Implementation direction

Use one local runtime and one capture engine first. A practical starting proposal is Python for the service and parsers, SQLite for metadata and search, filesystem-backed artifacts, and an instrumented browser. An optional web UI can use a local API; it should not be required to operate the CLI or MCP interface.

Playwright documents browser network observation, routing, and proxy configuration. It is a candidate browser integration, not proof of universal capture.[^playwright]

mitmproxy documents interception-related features and replay. ZAP exposes an API for external integration. Either could back the first capture adapter; build one initially rather than require both in a double-proxy chain.[^mitmproxy][^zap]

A ZAP adapter alone is not the product. The intended differentiation is the compact query interface, persistent evidence, baseline-aware observations, human-agent shared state, and measured reduction in model context—not simply exposing every upstream API call to the model.
Proposed project layout

This shows the future contents of a user's working directory, not files that already exist:
my-project/
├── .agentburp/
│   ├── project.toml          # Reviewed scope and settings
│   ├── state/                # Local index, cursors, and runtime metadata
│   ├── artifacts/            # Captured evidence; not committed
│   ├── profiles/             # Sensitive browser state; not committed
│   ├── recipes/              # Reviewed local workflows
│   ├── notes/                # Scoped conclusions and evidence references
│   ├── exports/              # Sanitised bundles awaiting review
│   └── install-manifest.json # Recorded installer changes
├── .mcp.json                 # Optional Claude Code project connection
├── .claude/skills/agentburp/  # Optional Claude Code companion skill
├── .codex/config.toml        # Optional trusted-project Codex connection
├── .agents/skills/agentburp/  # Optional Codex companion skill
└── ...your existing code...

Host configuration locations above follow the cited client documentation; the .agentburp/ structure is this project's proposal.[^codex-mcp][^codex-skills][^claude-mcp][^claude-skills]

Configuration and reviewed recipes may be shareable after inspection. Captures, local databases, credentials, browser profiles, and raw exports should stay out of source control. An installer must show its Git-ignore changes instead of silently assuming a hidden folder is private.

The workspace must be bound to an explicitly resolved root. Switching directories or Git worktrees must not silently attach an agent to another project's browser or credentials.

Uninstall should remove only AgentBurp-managed entries and offer explicit retention or deletion of project data. Shared runtimes, browser binaries, external credential storage, and certificate trust changes need separate cleanup reporting; deleting one directory is not guaranteed to remove every dependency.
Trust, privacy, and operational limits

Local-first storage does not mean that information is never sent to a model provider. Anything returned through the agent's tools may enter that agent's model context. Redact before delivery and show exactly what crosses the boundary.

Keep the following requirements in the runtime:

    Bind local controls to loopback by default; authenticate the UI and non-stdio APIs; protect project boundaries and privileged operations.
    Treat page content, captured responses, tool descriptions, and imported records as untrusted data—not instructions that can grant permissions.
    Separate credential-bearing artifacts from ordinary results. Use origin-bound, session-specific credential references; never silently copy credentials to another destination.
    Use dedicated testing profiles where interception needs certificate trust. Preview trust changes and record how to reverse them.
    Preserve capture limitations: pinned TLS, unsupported transports, service workers, cache hits, browser internals, and unmanaged processes may affect visibility. Publish adapter-specific capabilities.
    Distinguish offline reads, ordinary navigation, and consequential actions. HTTP method names alone do not establish safety. Check actual destinations and effects and require the configured approvals.
    Keep original captured evidence separate from edits and summaries. Hashes identify artifacts but do not independently make a locally editable store tamper-proof.
    Bound storage, parsing, decompression, browser resources, and extension permissions. Render captured content as untrusted data in the human UI.

MCP availability does not give AgentBurp visibility into every native client tool. An unconnected browser, unrelated terminal process, or provider-internal execution path is outside the capture boundary unless separately instrumented. The UI must say so.
Measuring token efficiency honestly

No fixed percentage or multiplication factor is promised. The first benchmark should compare identical tasks and controlled fixture states with and without AgentBurp's compact observation path.

Use both a straightforward full-output baseline and a competent existing tool workflow. Include tiny tasks where AgentBurp's schema and coordination overhead may outweigh its benefit.
Metric	What to measure
Tool-result input	Count the content delivered to the model, using a stated tokenizer where provider data is unavailable.
Total task usage	Include prompts, tool schemas, outputs, retries, summaries, and any additional model calls. Mark unavailable usage as unknown.
Cache treatment	Report cached and uncached usage separately when available. Do not count a local cache hit as guaranteed provider-billing savings.
Task success	Did the agent reach the correct, independently verifiable outcome?
Evidence completeness	Did it miss relevant headers, body fields, application states, or capture gaps?
Interaction count	How many model turns, tool calls, expansions, and repeated browser steps were needed?
Latency and resources	Include local indexing, startup, memory, disk, and browser overhead—not only model response time.
Resume quality	Can a fresh agent recover the project state without rereading the recording or trusting an outdated brief?

For the same completed task, the basic comparison is:
tool_result_reduction = 1 - compact_delivered_tokens / baseline_delivered_tokens

This is a tool-output comparison only. It is not automatically total-task token reduction, an invoice reduction, or a quality improvement. If the denominator is zero or telemetry is unavailable, report the comparison as unavailable.

Suggested evaluation tasks: diagnose a local JSON error, compare two recordings, locate a form's associated request, verify a development fix, recover a session after restart, and explain a partial capture. Publish model/client versions, corpus size, task results, and failure cases alongside savings.

Ship efficiency claims only when the same evidence quality and task outcome hold up under measurement.
Roadmap
0 — Nail the installable core

    Define the workspace contract, event identity, capability model, and privacy boundaries.
    Specify a previewable installer and reversible per-project configuration changes.
    Create one local demo application and fixed recordings for evaluation.
    Define compact-result and complete-result benchmark baselines.

1 — A useful headless traffic workbench

    Integrate one capture engine and preserve capture-health information.
    Store and index exchanges in a project-local evidence store.
    Implement status, query, read, and diff through a small MCP interface and CLI.
    Ship one tested local-client integration and a compact companion skill.
    Add output budgets, field projection, pagination, and explicit omissions.

Exit criterion: a coding agent can explain a known local-app failure from selected captured evidence without ingesting the full recording.
2 — Browser awareness and both client integrations

    Add managed browser observations, action receipts, and selected visual evidence.
    Implement baseline-aware deltas, session separation, and project handover.
    Test Claude Code and Codex separately against pinned supported versions.
    Add a second client adapter without duplicating the evidence store.
    Measure token usage and task accuracy on the same fixture suite.

Exit criterion: either supported local agent can resume the same approved project and retrieve only the evidence needed for a task.
3 — Human-agent shared workbench

    Add a minimal UI for history, source inspection, comparison, and approvals.
    Add editable replay drafts and explicitly reviewed development replays.
    Add evidence exports, project notes, and sanitised handoff bundles.
    Add Browser Twin and supported interception breakpoints.

Exit criterion: a human and an agent can inspect the same record, review changes, and understand what was delivered or executed.
4 — Reusable development workflows

    Add Teach Once, reviewed recipes, and local regression checks.
    Add passive review rules with labelled fixture evaluations.
    Add optional stream, MCP, and AI-application inspectors.
    Publish extension contracts and a truthful adapter capability matrix.

Research backlog

BranchLab, cross-agent shared evidence, adaptive visual observations, semantic page-state identity, privacy-preserving artifact references, and bounded programmatic analysis need independent experiments.

Do not delay the first useful release for agent graphs, an elaborate dashboard, every proxy backend, or a huge scanner catalogue. The initial product is the installable evidence workbench and its efficient agent interface.
Project boundaries

AgentBurp is not a replacement coding agent, a model-provider router, or primarily a scanner for attacking LLMs. Its scope is web debugging, inspection, controlled development verification, and evidence-led security review.

It does not promise universal traffic capture, perfect prompt-injection prevention, automatic vulnerability confirmation, or guaranteed savings. Autonomous exploitation and unattended third-party attack workflows are not part of this design.

AgentBurp is a working name. No affiliation with Burp Suite, PortSwigger, ZAP, OpenAI, or Anthropic is implied. Licensing, supported platforms, packaging, and release policy remain undecided.

The long-term goal: let an existing coding agent use a real web workbench, while spending its context on relevant evidence rather than transport noise.
Technical references

Official documentation consulted on 26 September 2026. Integration claims should be rechecked against the specific versions selected for implementation. These references describe external capabilities, not implemented AgentBurp features.

[^codex-mcp]: OpenAI, Model Context Protocol: local server connections and project-scoped MCP configuration.
[^codex-skills]: OpenAI, Build skills: repository skills, supporting files, and progressive disclosure.
[^claude-mcp]: Anthropic, Connect Claude Code to tools via MCP: project configuration, plugin-provided servers, and tool search.
[^claude-skills]: Anthropic, Extend Claude with skills: project skill locations and plugin packaging.
[^efficient-mcp]: Anthropic, Code execution with MCP: filtering tool results outside model context and on-demand capability access.
[^context]: Anthropic, Effective context engineering for AI agents: just-in-time context retrieval using lightweight references.
[^tools]: Anthropic, Writing effective tools for agents: tool design and evaluation considerations.
[^playwright]: Microsoft / Playwright, Network: browser network observation, routing, proxies, and service-worker caveats.
[^mitmproxy]: mitmproxy, Features: interception-related functionality and replay.
[^zap]: ZAP,
API: external programmatic integration.
