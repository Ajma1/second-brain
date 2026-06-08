---
type: reference
source: github-gist
url: https://gist.github.com/kimprobably/24ed1a33700bac09e19a2372ae1289c1
tags: [github, gist]
---

Brain v1 Implementation Spec — ghostwriter knowledge layer (Convex, OpenRouter)

# Brain v1 — Implementation Spec

**Date:** 2026-06-07
**Status:** Ready for implementation
**Author:** Tim + Claude (synthesis of 8 prior specs, 7 repo teardowns, 3 adversarial reviews, 3 Convex prototype schemas)
**Builder:** Ajmal
**Runtime:** Convex (new project, greenfield)
**LLM routing:** All calls through OpenRouter (`anthropic/claude-haiku-4-5` for extraction, `anthropic/claude-sonnet-4-6` for synthesis)

---

**Reading guide for Ajmal:** Start with **§10 (Build Order)** — it tells you what to build and when. Reference **§4 (Schema)**, **§5 (Pipeline)**, **§6 (Retrieval)**, **§11 (API Surface)**, and **§12 (Convex Notes)** while building. §§1-3 are design rationale and locked architectural decisions — read for context but don't build from them. §13 (Research Appendix) is reference material you don't need unless you're questioning a design decision.

---

## 1. What This Is

A per-client knowledge layer for a multi-client LinkedIn ghostwriting platform. The ghostwriter (persona: **Aidan**, ~5 clients) uploads source material about each client — transcripts, websites, docs, past posts — and the Brain extracts cited claims, organizes them into topics, and feeds them into content generation so every post is grounded in real client context.

**The compounding thesis:** Human edits → lints / examples / brain updates → better next batch → fewer edits. The Brain is the citation-backed truth store that makes this flywheel work.

**What this spec covers:** The Brain foundation — ingestion, extraction, storage, retrieval, knowledge files, position synthesis, and eval. It does NOT cover the content generation pipeline, the lint/replacer engine, the edit-learning system, agent memory, or the ops cockpit. Those are separate specs that consume the Brain as a dependency.

**Storage philosophy: .md-first, RAG-second.** Every claim, position, and knowledge file is stored as markdown with structured frontmatter. The markdown representation IS the primary interface — models consume it directly. For a client with 200-500 claims, you don't need vector search; you read the relevant claims as markdown and pass them in context. RAG (vector indexes, embeddings) is an optimization layer that activates when a client's corpus exceeds what fits in a context window. Build the .md layer first; add RAG when you need it.

---

## 2. Architectural Principles (Locked)

These 12 decisions are settled. They come from 3 months of spikes, prototypes, and a live methodology-spike that achieved recall 1.0 / over-edit 0.0 / preservation 1.0. Don't revisit them — build on them.

### 2.1 Grounded-node primitive
Every piece of knowledge traces to a cited source with an exact quote and locator. No uncited claims in the Brain. If it can't be grounded, it goes in agent memory (a separate system).

### 2.2 Three provenances
- **client** — their own words (transcripts, website, docs they wrote)
- **market** — external context (competitor analysis, industry research, audience language)
- **internal** — learned from results (performance data, edit patterns, what worked)

Provenance is set at ingestion time based on source origin. It never changes.

### 2.3 Two-memory boundary
- **Brain** = cited client truth. Deterministic. Survives across sessions. Authority for content generation.
- **Agent memory** = adaptive preferences, session context, agent hunches. NOT citation authority. Separate tables, separate retrieval.

This boundary is load-bearing. Don't blur it.

### 2.4 Claim extraction pipeline
`Source → chunk → cheap model (Haiku via OpenRouter) → grounding validator (deterministic substring check) → fingerprint dedup → store`

The cheap model does extraction. The grounding validator is deterministic — it checks that the cited quote actually appears in the source text (substring match). If grounding fails, the claim is rejected. No exceptions.

### 2.5 Knowledge file trinity
Three curated markdown files per client:
- **voice.md** — how they talk, banned phrases, stylistic preferences
- **icp.md** — who they sell to, pain points, objections, customer language
- **design.md** — visual identity, brand constraints, formatting preferences

These are **curated, not computed**. The Brain proposes updates; a human approves them. They go into every generation prompt as system context.

### 2.6 Compounding thesis
Edits are learning signals. Each edit routes to one of: lint (deterministic rule), example (taste corpus), brain update (new fact), or nothing (one-off preference). The routing is a small ZFC classifier + human confirm.

### 2.7 Markdown-first, RAG-second
Every piece of knowledge has a canonical markdown representation with YAML frontmatter. This is the primary interface — models read markdown natively and it's the most debuggable format. Convex tables store the structured data AND the rendered markdown. RAG (vector indexes, embeddings, search) is an optimization layer for when a client's claim count exceeds what fits in context (~500+ claims). Don't build RAG first and markdown on top — build markdown first and add RAG when the corpus demands it.

The original design doc states: *"The files are the source of truth; `.index.json` is a derived cache. The brain needs no database."* In Convex, the "files" are records with markdown content fields. The principle holds: the markdown IS the knowledge; the database is infrastructure.

### 2.8 Headless-first
Every Brain operation is a Convex function callable from CLI, MCP, or UI. The UI is a view layer over headless ops. No UI-only features.

### 2.9 Deterministic lints at 100%
Quality rules are deterministic (regex, forbidden phrase, required slot, citation check). They run at 100% — every draft, every time. Three enforcement levels: `hard_generation` (blocks generation), `hard_display` (blocks display/publish), `soft_style` (warning only). Four scopes: `system`, `workspace`, `customer`, `individual`.

### 2.10 Self-modifiable methodology substrate
The methodology (prompts, rules, examples, voice constraints) is versioned data, not code. Power users edit it through the cockpit or CLI. The system is homoiconic — methodology shapes generation, and generation results shape methodology.

### 2.11 Protected-voice invariant
Every generative step (draft, edit, repair, client-edit-application) runs through the voice model. The replacer enforces a protected-content invariant: named entities, quotes, numbers, and client-specific terminology are never altered by AI edits. Validated in methodology-spike at preservation 1.0.

### 2.12 Brain portability — the client's data, not the ghostwriter's
The Brain belongs to the client. The ghostwriter has stewardship, not ownership. If a client parts ways with their ghostwriter, they take their Brain with them — onto their own Maestro account or to a new ghostwriter's workspace. If the ghostwriter leaves Maestro, each client's Brain stays on the platform under the client's own account.

This is a platform retention play: the Brain is a compounding asset. The more a client invests, the more valuable it becomes, and the more reason they have to stay on Maestro regardless of which ghostwriter (or no ghostwriter) they're working with. The ghostwriter is a service layer on top of the client's knowledge — not the owner of it.

**v1 constraint:** Don't build transfer yet, but don't prevent it. No workspace-specific data baked into claim content, markdown, or deterministic IDs. All deterministic IDs (`claimId`, `sourceId`) use `clientId` as the namespace, not `workspaceId`. Fingerprints are workspace-independent. The `workspaceId` on every table is an access-scoping key, not an ownership claim. Transfer is a `workspaceId` reassignment across table rows — no content migration, no ID regeneration, no markdown rewriting.

---

## 3. Research-Backed Design Decisions

Each decision below is grounded in findings from repo teardowns. The source repo is cited so you can dig deeper if needed.

### 3.1 ADD-only extraction (from mem0)
The extraction pipeline only ADDs claims. It never UPDATEs or DELETEs existing claims based on new source material. mem0 found that removing UPDATE/DELETE from LLM extraction calls improved their LoCoMo benchmark by 20 points. Stale claims are handled by lifecycle management (a separate, human-triggered process), not by the extraction pipeline.

**Implementation:** The extraction prompt says "extract new claims" — it never sees existing claims. Dedup happens post-extraction via fingerprinting, not by asking the LLM to merge.

### 3.2 Delimiter-based extraction output (from nano-graphrag, Microsoft GraphRAG)
Both nano-graphrag and Microsoft GraphRAG use delimiter-based formats (`<|>` for fields, `##` for records) instead of JSON for extraction output. JSON parsing failures cause silent data loss. Delimiter parsing is more robust with cheap models.

**Implementation:** Extraction output format:
```
claim<|>TITLE<|>SUBTYPE<|>DESCRIPTION<|>QUOTE<|>LOCATOR
##
claim<|>TITLE<|>SUBTYPE<|>DESCRIPTION<|>QUOTE<|>LOCATOR
```
Parse with `split("##")` then `split("<|>")`. Malformed records are logged and skipped, not silently dropped.

### 3.3 Gleaning loop for extraction completeness (from Microsoft GraphRAG)
After initial extraction, send a follow-up prompt: "MANY entities and claims were missed. Add them below using the same format." Then ask: "Are there still claims to extract? Y/N." If Y, loop again (max 2 gleanings). GraphRAG shows this materially improves extraction completeness at minimal cost (1-2 extra Haiku calls per chunk).

**Implementation:** Extract → Glean 1 → Check → (optional Glean 2) → Done. All claims from all passes are merged before dedup.

### 3.4 Two-tier dedup (from Graphiti, simplified)
1. **Exact:** SHA-256 fingerprint (see §5.6) — catches identical re-extractions. Free.
2. **LLM dedup:** For new claims, compare title against existing claim titles in the same topic using Convex's full-text `searchIndex`. For near-matches, ask Haiku: "Are these two claims saying the same thing? YES or NO." Cost: ~$0.0001 per comparison, fires for ~5-10% of claims.

MinHash/LSH is dropped. At 200-500 claims per client, the corpus is small enough that full-text search + cheap LLM comparison handles near-duplicates without building a separate fingerprinting pipeline. The added complexity of MinHash/LSH buys nothing at this scale.

**Implementation:** Tier 1 catches re-extractions. Tier 2 uses the searchIndex that already exists for retrieval. No new infrastructure.

### 3.5 Obsidian-style linked knowledge (markdown-first, RAG-deferred)
The Brain is Obsidian, not Pinecone. Claims are markdown notes. Topics are folders/tags. ClaimEdges are `[[wikilinks]]`. Positions are Maps of Content. Knowledge files are pinned notes. `brain.dump` is "give me everything in this folder."

An AI agent given a directory of markdown files with links would: read the files, follow links to related files, use the link graph for discovery, use full-text search when needed. That's the retrieval model for v1.

**Why this is enough for v1:** A client with 300 claims across 10 topics has ~30 claims per topic. At ~150 tokens per claim markdown, that's ~4,500 tokens per topic. Add 3 positions and 3 knowledge files = ~9,000 tokens total. The entire relevant context fits in a single API call. The model is a better ranker than any pre-filtering pipeline — it sees the full picture and selects what matters.

**Why claimEdges matter more than embeddings for "why?" questions:** The Right Reasons project (github.com/Right-Reasons/right-reasons) benchmarked structured ontology vs markdown+RAG across 48 test sessions. For "why did we make this decision?" questions: RAG scored **0% recall** (6/6 runs failed). The structured ontology scored **100% recall** (6/6 runs succeeded). The reason is structural — vector search can't recover reasoning chains that span multiple documents; it retrieves similar-sounding chunks, not connected reasoning. ClaimEdges (`supports`, `contradicts`, `refines`, `depends_on`) are the Brain's reasoning chain layer. Traversing from a position through its supporting claims to source citations answers "why does Tim believe X?" deterministically. Invest in edge density before investing in retrieval sophistication.

**Embeddings: compute async, index from day 1.** Embeddings cost ~$0.0001 per claim (basically free). Compute them asynchronously post-extraction via scheduler (§12.6) — never inline, so embedding API failures don't block claim storage. The `vectorIndex` is one line on the Convex schema, managed automatically. Claims without embeddings (pending backfill or API failure) are excluded from vector search but work fine for .md-first retrieval.

**The default retrieval path is still .md-first** (topic filter → dump all as markdown → model ranks). Vector search is an additional retrieval mode, not a replacement. Use it for cross-topic discovery, "find claims similar to X," or when a topic dump exceeds the context budget. The .md path is primary because the model is a better ranker than any pre-filtering pipeline at this scale.

### 3.6 Source backreference from claims to chunks (from LightRAG, Microsoft GraphRAG)
Every claim stores `sourceChunkIds[]` — which chunks it was extracted from. At query time, when a claim is retrieved, its source chunks are available for citation. This is the bridge between structured knowledge (claims) and raw evidence (quotes in context).

**Implementation:** Many-to-many relationship between claims and chunks via a `claimSources` table. When the context assembler retrieves a claim, it joins to get source quotes.

### 3.7 Description summarization with token batching (from Microsoft GraphRAG)
When the same concept appears across many sources, descriptions accumulate. Below 5 descriptions, concatenate with separator. Above 5, batch them into groups fitting a token window, summarize each group with LLM, then recursively summarize summaries until under 500 tokens.

**Implementation:** Used when building topic summaries from constituent claims. Not needed for individual claims (they should be atomic — one idea per claim, enforced by the extraction prompt).

### 3.8 Content-addressable chunk IDs (from Cognee)
`chunkId = uuid5(NAMESPACE_OID, chunk_text)` — deterministic from content. Re-ingesting the same source produces the same chunk IDs, so existing claims that reference those chunks remain valid. Enables idempotent re-ingestion without orphaning citations.

**Implementation:** Chunk IDs are derived, not random. If the source text hasn't changed, the chunks haven't changed, and existing claims still point to valid chunks.

### 3.9 Bi-temporal lifecycle (from Graphiti)
Claims are never deleted. They transition through lifecycle states: `candidate → accepted → stale → archived`. Claims that fail grounding validation go to `rejected` (non-promotable — must be revalidated before they can become `candidate` again). Each transition records `validAt` (when the claim became true in the real world) and `invalidAt` (when it stopped being true). Old claims remain queryable for historical context ("what did we believe about pricing in March?").

**Implementation:** Lifecycle is a state machine on the claim record. Default retrieval filters to `accepted` only. Historical queries can opt in to `stale` and `archived`. `rejected` claims are only visible in admin/debug views.

---

## 4. Convex Schema

### 4.1 Tenancy Model

```
workspace (ghostwriter account)
  └── client (each of Aidan's ~5 clients)
       └── all brain data is scoped to (workspaceId, clientId)
```

Every table carries `workspaceId` and `clientId`. Every query asserts access. No cross-client data leakage — this is the privacy invariant.

**Auth tables (not part of this spec):** The `workspaces` and `clients` tables are defined in the auth/accounts module — not in the Brain schema. The Brain references them via `v.id("workspaces")` and `v.id("clients")`. For v1 development, stub these with simple tables: `workspaces: defineTable({ name: v.string() })` and `clients: defineTable({ workspaceId: v.id("workspaces"), name: v.string(), status: v.union(v.literal("active"), v.literal("inactive")) })`. The real auth module will be designed separately.

**Ownership vs access (§2.12):** `workspaceId` is an access-scoping key, not an ownership claim. The Brain belongs to the client. Transfer between workspaces is a deferred feature but must not be prevented by v1 design choices. All deterministic IDs (`claimId`, `sourceId`) are workspace-independent — they use `clientId` as the namespace, not `workspaceId`. Transfer is a workspace-reassignment on every table row, not an ID rewrite.

### 4.2 Tables

#### `sources`
The raw material uploaded by the ghostwriter.

```typescript
sources: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  sourceId: v.string(),          // deterministic: uuid5(client+origin+url_or_hash) — workspace-independent for portability (§2.12)
  title: v.string(),
  origin: v.union(
    v.literal("upload"),         // file upload
    v.literal("url"),            // scraped website
    v.literal("transcript"),     // call transcript
    v.literal("paste"),          // pasted text
    v.literal("linkedin_posts"), // imported post history
    v.literal("performance"),    // performance data
  ),
  provenance: v.union(v.literal("client"), v.literal("market"), v.literal("internal")),
  url: v.optional(v.string()),
  sourceHash: v.string(),        // SHA-256 of raw content — for change detection
  status: v.union(
    v.literal("ingesting"),
    v.literal("chunked"),
    v.literal("extracted"),
    v.literal("failed"),
  ),
  errorMessage: v.optional(v.string()),
  metadata: v.optional(v.any()), // source-type-specific metadata (speaker names, etc.)
  createdAt: v.number(),
  updatedAt: v.number(),
})
.index("by_workspace_client", ["workspaceId", "clientId"])
.index("by_workspace_client_sourceId", ["workspaceId", "clientId", "sourceId"])
.index("by_workspace_client_sourceHash", ["workspaceId", "clientId", "sourceHash"])
.index("by_workspace_client_status", ["workspaceId", "clientId", "status"])
```

#### `sourceChunks`
Source material split into chunks for extraction.

```typescript
sourceChunks: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  sourceId: v.id("sources"),
  chunkId: v.string(),            // content-addressable: uuid5(NAMESPACE, chunk_text)
  chunkIndex: v.number(),         // position within source
  text: v.string(),
  tokenCount: v.number(),
  locator: v.object({             // where in the source this chunk lives
    type: v.union(v.literal("line_range"), v.literal("heading"), v.literal("speaker_turn"), v.literal("time_range")),
    start: v.string(),
    end: v.optional(v.string()),
    speaker: v.optional(v.string()),
  }),
  createdAt: v.number(),
})
.index("by_source", ["sourceId"])
.index("by_workspace_client_chunkId", ["workspaceId", "clientId", "chunkId"])
```

#### `claims`
The core knowledge primitive. Every claim is grounded in a source with an exact quote. The `markdown` field is the claim's canonical representation — what models consume directly.

```typescript
claims: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  claimId: v.string(),            // deterministic: uuid5(client+fingerprint) — workspace-independent for portability (§2.12)
  title: v.string(),              // concise label: "Agency pricing is value-based, not hourly"
  summary: v.string(),            // 1-3 sentence expansion
  markdown: v.string(),           // canonical .md representation (frontmatter + content) — see §4.4
  subtype: v.union(
    v.literal("opinion"),         // what the client believes
    v.literal("fact"),            // verifiable business fact
    v.literal("story"),           // anecdote or case study
    v.literal("framework"),       // mental model or process
    v.literal("proof"),           // data point, metric, result
    v.literal("preference"),      // how they want things done
    v.literal("icp"),             // ideal customer profile detail
    v.literal("offer"),           // what they sell, pricing, packaging
    v.literal("objection"),       // common pushback they handle
    v.literal("positioning"),     // how they differentiate
    v.literal("constraint"),      // what they won't do or say
    v.literal("voice"),           // stylistic preference, phrase, tone
  ),
  provenance: v.union(v.literal("client"), v.literal("market"), v.literal("internal")),
  lifecycle: v.union(
    v.literal("candidate"),       // freshly extracted, awaiting review
    v.literal("accepted"),        // reviewed and accepted as truth
    v.literal("rejected"),        // failed grounding validation — non-promotable
    v.literal("stale"),           // marked outdated but preserved
    v.literal("archived"),        // removed from active use
  ),
  confidence: v.number(),         // 0-1, set by extraction model
  fingerprint: v.string(),        // SHA-256(subtype + normalized_title + normalized_quote[:200]) — title included to distinguish distinct claims citing the same passage
  humanEdited: v.boolean(),       // true if a human has modified this claim
  originalTitle: v.optional(v.string()),   // snapshot of extraction-time title (preserved for eval comparison when humanEdited)
  originalSummary: v.optional(v.string()), // snapshot of extraction-time summary
  validAt: v.optional(v.number()),    // when this became true (bi-temporal)
  invalidAt: v.optional(v.number()),  // when this stopped being true
  embedding: v.optional(v.array(v.float64())),  // 1536-dim, computed async post-extraction. Optional so claim storage never blocks on embedding API failures.
  embeddingModel: v.optional(v.string()),       // model ID that produced the embedding (for drift detection / re-embed on model change)
  extractionRunId: v.optional(v.string()),
  createdAt: v.number(),
  updatedAt: v.number(),
})
.index("by_workspace_client", ["workspaceId", "clientId"])
.index("by_workspace_client_claimId", ["workspaceId", "clientId", "claimId"])
.index("by_workspace_client_lifecycle", ["workspaceId", "clientId", "lifecycle"])
.index("by_workspace_client_subtype", ["workspaceId", "clientId", "subtype"])
.index("by_workspace_client_provenance", ["workspaceId", "clientId", "provenance"])
.index("by_fingerprint", ["fingerprint"])
.searchIndex("search_claims", {
  searchField: "title",
  filterFields: ["workspaceId", "clientId", "lifecycle", "subtype", "provenance"],
})
.vectorIndex("by_embedding", {
  vectorField: "embedding",
  dimensions: 1536,
  filterFields: ["workspaceId", "clientId", "lifecycle"],
})
```

#### `claimCitations`
The grounding evidence. Every claim has at least one citation with an exact quote from the source.

```typescript
claimCitations: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  claimId: v.id("claims"),
  citationId: v.string(),
  sourceId: v.id("sources"),
  sourceChunkId: v.id("sourceChunks"),
  quote: v.string(),              // exact substring from source — validated by grounding check
  locator: v.object({
    type: v.union(v.literal("line_range"), v.literal("heading"), v.literal("speaker_turn"), v.literal("time_range")),
    start: v.string(),
    end: v.optional(v.string()),
    speaker: v.optional(v.string()),
  }),
  status: v.union(
    v.literal("valid"),
    v.literal("stale"),           // source has been re-ingested and quote no longer matches
    v.literal("rejected"),        // grounding check failed
  ),
  createdAt: v.number(),
})
.index("by_claim", ["claimId"])
.index("by_source", ["sourceId"])
.index("by_workspace_client", ["workspaceId", "clientId"])
```

#### `claimEdges`
Relationships between claims. Lightweight — no edge-level facts (per Graphiti's insight, facts live on the claims themselves, not on the edges). Edges are created by a post-extraction batch pass: after new claims are stored, a Haiku call receives the new claims + nearby existing claims (same topic or high embedding similarity) and proposes edges. Manual edge creation is also supported via `brain.addEdge`.

**Edge lifecycle:** Model-generated edges start as `proposed`. The `related_to` type is auto-accepted (low-stakes discovery aid). Reasoning-chain types (`supports`, `contradicts`, `refines`, `depends_on`) stay `proposed` until human review or deterministic evidence confirms them. Only `accepted` edges influence position synthesis and "why?" traversal (§3.5). `proposed` edges are still followed in 1-hop retrieval link-following (§6.2) — they help discovery but don't make truth claims.

```typescript
claimEdges: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  fromClaimId: v.id("claims"),
  toClaimId: v.id("claims"),
  edgeType: v.union(
    v.literal("supports"),
    v.literal("contradicts"),
    v.literal("refines"),
    v.literal("depends_on"),
    v.literal("related_to"),
  ),
  edgeLifecycle: v.union(
    v.literal("proposed"),          // model-generated, not yet reviewed
    v.literal("accepted"),          // human-reviewed or auto-accepted (related_to)
    v.literal("rejected"),          // human rejected
  ),
  provenance: v.union(
    v.literal("model"),             // auto-generated by Haiku batch pass
    v.literal("human"),             // manually created via brain.addEdge
  ),
  confidence: v.optional(v.number()), // 0-1, set by model for proposed edges
  createdAt: v.number(),
})
.index("by_from", ["fromClaimId"])
.index("by_to", ["toClaimId"])
.index("by_from_lifecycle", ["fromClaimId", "edgeLifecycle"])
.index("by_workspace_client", ["workspaceId", "clientId"])
```

#### `topics`
Canonical topic vocabulary per client. Curated, not auto-generated.

```typescript
topics: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  topicId: v.string(),
  slug: v.string(),
  displayName: v.string(),
  description: v.optional(v.string()),
  aliases: v.array(v.string()),    // alternative names that map to this topic
  lifecycle: v.union(
    v.literal("emergent"),         // auto-discovered, not yet reviewed
    v.literal("canonical"),        // reviewed and accepted
    v.literal("stale"),
    v.literal("archived"),
  ),
  hidden: v.boolean(),
  mergedIntoTopicId: v.optional(v.string()),
  claimCount: v.number(),          // denormalized count for quick display
  createdAt: v.number(),
  updatedAt: v.number(),
})
.index("by_workspace_client", ["workspaceId", "clientId"])
.index("by_workspace_client_slug", ["workspaceId", "clientId", "slug"])
.index("by_workspace_client_lifecycle", ["workspaceId", "clientId", "lifecycle"])
```

#### `topicMemberships`
Links claims to topics. Separate table (not a string array on claims) so memberships have their own lifecycle.

```typescript
topicMemberships: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  topicId: v.id("topics"),
  claimId: v.id("claims"),
  method: v.union(
    v.literal("model_suggested"),
    v.literal("human"),
    v.literal("rule"),
  ),
  status: v.union(
    v.literal("proposed"),
    v.literal("accepted"),
    v.literal("rejected"),
  ),
  confidence: v.optional(v.number()),
  createdAt: v.number(),
})
.index("by_topic", ["topicId"])
.index("by_claim", ["claimId"])
.index("by_workspace_client_topic_status", ["workspaceId", "clientId", "topicId", "status"])
```

#### `topicSummaries`
Derived summaries of what the Brain knows about each topic. Generated from constituent claims. Not citation authority — the claims are.

```typescript
topicSummaries: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  topicId: v.id("topics"),
  summary: v.string(),
  supportClaimIds: v.array(v.id("claims")),
  supportCitationIds: v.array(v.id("claimCitations")),
  sourcePackHash: v.string(),      // hash of input claims — for staleness detection
  modelSlot: v.string(),
  promptVersion: v.string(),
  status: v.union(v.literal("current"), v.literal("stale")),
  createdAt: v.number(),
})
.index("by_topic", ["topicId"])
.index("by_workspace_client", ["workspaceId", "clientId"])
```

**Staleness trigger:** Topic summaries are checked eagerly — after each extraction run completes, recompute `sourcePackHash` for affected topics (topics that received new claims). If the hash no longer matches, mark the summary `stale`. Stale summaries are still served (better stale than missing) but flagged in the UI. Re-generation is triggered manually or as a background workflow step after extraction. This prevents stale summaries from feeding into position synthesis without the ghostwriter knowing.

#### `knowledgeFiles`
The curated knowledge files per client. Three are seeded by default (voice, icp, design) but ghostwriters can create custom files for any domain (competitor analysis, hiring philosophy, content strategy, etc.).

```typescript
knowledgeFiles: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  fileType: v.string(),              // "voice", "icp", "design" seeded by default; custom types allowed (e.g. "competitors", "hiring", "content-strategy")
  content: v.string(),             // markdown content
  version: v.number(),
  proposedUpdates: v.optional(v.array(v.object({
    section: v.string(),
    currentText: v.string(),
    proposedText: v.string(),
    reason: v.string(),
    sourceClaimIds: v.array(v.id("claims")),
    status: v.union(v.literal("pending"), v.literal("accepted"), v.literal("rejected")),
  }))),
  updatedAt: v.number(),
  updatedBy: v.union(v.literal("human"), v.literal("system")),
})
.index("by_workspace_client_type", ["workspaceId", "clientId", "fileType"])
```

#### `extractionRuns`
Audit trail for extraction pipeline runs.

```typescript
extractionRuns: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  runId: v.string(),
  sourceId: v.id("sources"),
  status: v.union(
    v.literal("chunking"),
    v.literal("extracting"),
    v.literal("gleaning"),
    v.literal("validating"),
    v.literal("deduplicating"),
    v.literal("completed"),
    v.literal("failed"),
  ),
  stats: v.optional(v.object({
    chunksTotal: v.number(),
    chunksProcessed: v.number(),
    chunksSkipped: v.number(),      // chunks that failed after 3 retries (§5.9)
    claimsExtracted: v.number(),
    claimsPassedGrounding: v.number(),
    claimsFailedGrounding: v.number(),
    claimsDeduplicated: v.number(),
    claimsStored: v.number(),
    gleaningPasses: v.number(),
    tokenCost: v.number(),
  })),
  errorMessage: v.optional(v.string()),
  modelSlot: v.string(),
  promptVersion: v.string(),
  createdAt: v.number(),
  completedAt: v.optional(v.number()),
})
.index("by_workspace_client", ["workspaceId", "clientId"])
.index("by_source", ["sourceId"])
```

#### `contextPackRuns`
Audit trail for retrieval operations. Every time the Brain assembles context for generation, it logs what was retrieved and why.

```typescript
contextPackRuns: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  runId: v.string(),
  query: v.string(),
  strategy: v.union(
    v.literal("topic_dump"),       // all claims in a topic (default)
    v.literal("full_dump"),        // all claims for a client
    v.literal("subtype_filter"),   // all claims of a subtype
    v.literal("source_trace"),     // all claims from a source
    v.literal("text_search"),      // full-text searchIndex + link-following
    v.literal("vector_search"),    // embedding-based similarity search
  ),
  retrievedClaimIds: v.array(v.id("claims")),
  retrievedCitationIds: v.array(v.id("claimCitations")),
  tokenEstimate: v.number(),
  createdAt: v.number(),
})
.index("by_workspace_client", ["workspaceId", "clientId"])
```

#### `positions`
Higher-order theses synthesized from multiple claims. This is what the writer actually uses — not "here are 47 facts about pricing" but "Tim believes value-based pricing beats hourly for agencies, and here's why." Positions are the bridge between raw evidence and content.

```typescript
positions: defineTable({
  workspaceId: v.id("workspaces"),
  clientId: v.id("clients"),
  positionId: v.string(),
  title: v.string(),              // "Value-based pricing beats hourly for agencies"
  thesis: v.string(),             // 2-4 sentence articulation of the position
  markdown: v.string(),           // canonical .md representation (see §4.4)
  stanceType: v.union(
    v.literal("contrarian"),      // goes against conventional wisdom
    v.literal("conventional"),    // aligns with industry consensus but has unique angle
    v.literal("nuanced"),         // "it depends" with specific conditions
    v.literal("experiential"),    // "I learned this the hard way" — story-backed
  ),
  confidence: v.number(),         // 0-1, based on support strength
  readiness: v.union(
    v.literal("draft"),           // just synthesized, needs review
    v.literal("usable"),          // has sufficient support for content
    v.literal("strong"),          // well-supported, multiple evidence types
    v.literal("stale"),           // support claims are stale or contradicted
  ),
  topicIds: v.array(v.id("topics")),
  supportClaimIds: v.array(v.id("claims")),    // the claims this position is derived from
  supportCitationIds: v.array(v.id("claimCitations")),
  tensions: v.optional(v.array(v.string())),    // contradictions or caveats
  gaps: v.optional(v.array(v.string())),        // what evidence is missing to make this stronger
  sourcePackHash: v.string(),      // hash of input claims — for staleness detection
  createdAt: v.number(),
  updatedAt: v.number(),
})
.index("by_workspace_client", ["workspaceId", "clientId"])
.index("by_workspace_client_readiness", ["workspaceId", "clientId", "readiness"])
.index("by_workspace_client_stanceType", ["workspaceId", "clientId", "stanceType"])
```

### 4.3 The Markdown Representation

Every claim, position, and knowledge file has a canonical markdown representation. This is NOT a secondary format — it's the primary interface models consume. The Convex record stores both structured fields (for indexing/filtering) AND the rendered markdown (for direct model consumption).

**Claim markdown format:**
```markdown
---
id: cl-2026-06-07-7f3a9c
type: claim
subtype: opinion
provenance: client
confidence: 0.92
lifecycle: accepted
topics: [pricing, agency-model]
---
# Agency pricing is value-based, not hourly

Tim charges based on the value delivered to the client's business, not hours worked.
He frames this as "what is solving this problem worth to you?" rather than billing
for time spent. This applies to both retainer and project-based engagements.

> "We stopped billing hourly three years ago. The conversation changed completely —
> instead of 'how many hours will this take,' clients ask 'what result will I get.'
> That's the right conversation."
> — Client onboarding transcript, 2026-03-15, turn 23
```

**Position markdown format:**
```markdown
---
id: pos-2026-06-07-a1b2c3
type: position
stance: contrarian
readiness: strong
topics: [pricing, agency-model]
support_claims: [cl-7f3a9c, cl-8d4b2e, cl-9e5c3f]
gaps: ["No data on client retention rates under value-based model"]
---
# Value-based pricing beats hourly for agencies

Tim believes agencies that charge hourly are leaving money on the table and
training clients to micromanage their time. His position: price the outcome,
not the input.

## Supporting evidence

- **Opinion:** "We stopped billing hourly three years ago" (onboarding transcript)
- **Proof:** 40% revenue increase after switching to value-based (Q3 review)
- **Story:** Lost a client who wanted hourly, won them back at 2x on value (sales call)
- **Framework:** The "what is this worth to you?" discovery question (methodology doc)

## Tensions
- Some clients with tight budgets still push for hourly as a way to control scope

## Gaps
- No data on client retention rates under value-based model
```

**Sync direction: structured fields are authoritative, markdown is derived.** When a human edits a claim via `brain.editClaim` (updating title or summary), the markdown is re-rendered from the structured fields. Editing the raw markdown string directly (via CLI or MCP) is not supported — always edit the structured fields and let the system re-render. This prevents structured fields and markdown from drifting apart.

**Rendering function (pseudocode):**
```typescript
function renderClaimMarkdown(claim, citations, topicSlugs) {
  const frontmatter = [
    `id: ${claim.claimId}`, `type: claim`, `subtype: ${claim.subtype}`,
    `provenance: ${claim.provenance}`, `confidence: ${claim.confidence}`,
    `lifecycle: ${claim.lifecycle}`, `topics: [${topicSlugs.join(", ")}]`,
  ].join("\n");
  const body = `# ${claim.title}\n\n${claim.summary}`;
  const citationBlock = citations.map(c =>
    `> "${c.quote}"\n> — ${c.locator.type}: ${c.locator.start}`
  ).join("\n\n");
  return `---\n${frontmatter}\n---\n${body}\n\n${citationBlock}`;
}
```
Each `brain.editClaim` call re-runs this and patches the `markdown` field.

**Why this matters:** When a writer asks "what does Tim think about pricing?", the system can return 3 position markdown files and 12 supporting claim markdown files. The model reads them directly — no parsing, no assembly, no retrieval pipeline. Just markdown.

**When RAG activates:** When a client has 500+ claims and you can't fit all relevant ones in context, use vector search to select WHICH markdown files to include. The output is still markdown — RAG just filters the set.

### 4.4 Tables Deferred to Later Phases

These exist in the Convex prototype schemas but are deferred:

- `assetOpportunities` — "what can I make from this knowledge." Depends on positions.
- `researchGaps` — "what's missing." Depends on topic summaries. (Note: `positions.gaps` handles the basic case.)
- `qualityRules`, `qualityGateRuns` — the deterministic lint engine. Separate spec.
- `editEvents`, `ruleCandidates` — edit-learning system. Separate spec.
- `memorySessions`, `memoryObservations` — agent memory. Separate system.
- `topicDiscoveryClusters` — emergent clustering. v1 uses manual topic assignment.
- `topicPerformanceAttributions` — performance loop. Needs live post data.
- `sourceFacets` — segment slicing. v2.
- `scoutCreators`, `scoutPosts`, `scoutFormats` — the Scout/Joni research agent. Separate spec.

---

## 5. Extraction Pipeline

### 5.1 Source Ingestion

```
User uploads/pastes/links source → store raw content → compute sourceHash → check for existing source with same hash (idempotent) → store source record → trigger chunking
```

### 5.2 Chunking Strategy

Chunk strategy varies by source type. Goal: 700-1200 tokens per chunk, preserving semantic boundaries.

| Source Type | Strategy | Boundaries |
|---|---|---|
| Website | Split by `<h2>` / `<h3>` headings | Each section = 1 chunk. Merge small adjacent sections. Split sections > 1200 tokens at paragraph boundaries. |
| Transcript | Speaker micro-turns | Group 3-10 consecutive turns by same speaker. Split at speaker changes. Carry speaker name as metadata. |
| Document (PDF, doc) | Heading hierarchy | Split at heading boundaries. Merge short sections. |
| Pasted text | Paragraph boundaries | Split at double newlines. Merge short paragraphs. |
| LinkedIn posts | One post per chunk | Each post is its own chunk (typically 200-800 tokens). |
| Performance data | One metric period per chunk | Each reporting period = 1 chunk. |

**Chunk overlap:** Use 100-token overlap between adjacent chunks. Claims that span a chunk boundary will otherwise be split or missed entirely — this is a known extraction recall killer. The overlap ensures boundary-spanning claims appear fully in at least one chunk. Exception: LinkedIn posts and performance data chunks are self-contained and don't need overlap.

**Chunk ID:** `uuid5(NAMESPACE_OID, chunk_text)` — content-addressable, deterministic. Re-ingesting the same source produces the same chunk IDs.

### 5.3 Extraction Prompt

```
-Goal-
Given a text chunk from {source_type} about {client_name}'s business, extract all distinct claims that could be useful for writing LinkedIn content on their behalf.

-Claim Types-
OPINION: What the client believes or advocates for
FACT: Verifiable business facts (revenue, team size, years in business, tools used)
STORY: Anecdotes, case studies, client success stories, personal experiences
FRAMEWORK: Mental models, processes, methodologies they teach or follow
PROOF: Data points, metrics, results, testimonials
PREFERENCE: How they want things done, communicated, or presented
ICP: Details about their ideal customer (role, company size, pain points, language)
OFFER: What they sell, pricing, packaging, delivery model
OBJECTION: Common pushback and how they handle it
POSITIONING: How they differentiate from alternatives
CONSTRAINT: What they won't do, say, or endorse
VOICE: Stylistic preferences, signature phrases, tone markers

-Output Format-
For each claim, output one record:
claim<|>TITLE<|>SUBTYPE<|>SUMMARY<|>EXACT_QUOTE<|>LOCATOR
Use ## to separate records.

-Rules-
1. TITLE: A concise, specific label (5-15 words). Not generic ("Business Strategy") but specific ("Value-based pricing beats hourly for agencies").
2. SUMMARY: 1-3 sentences expanding the claim. Include specific details from the source.
3. EXACT_QUOTE: Copy-paste the most relevant sentence(s) from the source text. This MUST be a verbatim substring of the input. Do not paraphrase, summarize, or invent.
4. LOCATOR: The heading, speaker turn, or line range where the quote appears.
5. One claim per distinct idea. If a paragraph contains 3 ideas, extract 3 claims.
6. Do NOT extract claims about things the client didn't say. Only extract what is explicitly stated or directly implied.
7. Do NOT merge multiple ideas into one claim. Atomic claims only.
8. When finished, output <|COMPLETE|>

-Source Text-
{chunk_text}
```

**Subtype accuracy note:** 12 subtypes in one extraction pass is a lot for a cheap model. If the golden dataset eval shows subtype accuracy below the 85% gate, switch to two-pass extraction: Pass 1 extracts claims without subtype (just title, summary, quote, locator). Pass 2 is a cheap batch classification call that assigns subtypes to extracted claims. The two-pass approach is more ZFC-aligned (extraction ≠ classification) and will likely improve subtype precision. Start with single-pass; measure; split if needed.

### 5.4 Gleaning Prompt (runs 1-2x after initial extraction)

```
MANY claims were missed in the last extraction. Look for:
- Specific numbers, dates, metrics
- Stories or anecdotes mentioned in passing
- Opinions expressed indirectly ("I always tell my clients...")
- Constraints or things they avoid ("We never...")
- Customer language or pain points
- Process details or frameworks

Add missed claims below using the same format (claim<|>TITLE<|>...).
When no more claims can be found, output <|COMPLETE|>
```

### 5.5 Grounding Validation (Deterministic)

For each extracted claim, validate that `EXACT_QUOTE` is a substring of the source chunk text:

```python
def validate_grounding(claim, chunk_text):
    quote = claim.exact_quote.strip()
    # Normalize whitespace for comparison
    normalized_quote = re.sub(r'\s+', ' ', quote)
    normalized_text = re.sub(r'\s+', ' ', chunk_text)
    return normalized_quote in normalized_text
```

**Hard rule:** If `>30%` of claims in a single extraction run fail grounding, the entire run is flagged for review. This catches prompt failures and model hallucination spikes early.

Claims that fail grounding are stored with `lifecycle: "rejected"` and `confidence: 0`. The `rejected` state is non-promotable — `brain.acceptClaim` refuses to transition `rejected` claims. They're visible in admin views for debugging but never enter active retrieval. To recover a rejected claim, the human must edit the citation to a valid quote and call `brain.revalidateClaim`, which re-runs grounding and transitions to `candidate` if it passes.

### 5.6 Fingerprint Dedup

```python
def compute_fingerprint(claim):
    # Includes subtype + title + quote to distinguish distinct claims
    # from the same passage. Title is LLM-generated so re-extractions of the
    # same claim may produce different titles — the fuzzy dedup (Tier 2) catches
    # those near-matches.
    normalized_title = re.sub(r'\s+', ' ', claim.title.lower().strip())
    normalized_quote = re.sub(r'\s+', ' ', claim.exact_quote[:200].lower().strip())
    fingerprint_input = f"{claim.subtype}|{normalized_title}|{normalized_quote}"
    return hashlib.sha256(fingerprint_input.encode()).hexdigest()
```

**Tier 1 (exact):** Check fingerprint against existing claims. If match, skip (idempotent re-extraction). Because the fingerprint includes the LLM-generated title, two distinct claims from the same passage will have different fingerprints and both survive. Re-extractions that produce slightly different titles for the same claim fall through to Tier 2.

**Tier 2 (fuzzy — full-text search + LLM confirmation):** For new claims, compare title against existing claim titles in the same topic using Convex's full-text `searchIndex`. For near-matches (top 3 results), ask Haiku: "Are these two claims saying the same thing? Respond YES or NO." Cost: ~$0.0001 per comparison, fires for ~5-10% of claims. No MinHash/LSH — at 200-500 claims per client, full-text search is sufficient.

### 5.7 Topic Assignment

After extraction and dedup, assign topics to claims:

1. Check claim title + summary against existing topic vocabulary (exact match on aliases, then fuzzy match on display names)
2. If no match, propose a new topic (lifecycle: `emergent`)
3. Store as `topicMembership` with method `model_suggested`, status `proposed`

Topic assignment is advisory in v1. Claims are useful without topic assignment. Topics become important when building topic summaries and positions (v1.5).

### 5.8 Pipeline Summary

```
Source upload/paste/URL
  → Store source record (idempotent by sourceHash)
  → Chunk by source type (700-1200 tokens)
  → Store chunks (content-addressable IDs)
  → For each chunk:
      → Extract claims (Haiku via OpenRouter, delimiter format)
      → Glean 1-2x for missed claims
      → Validate grounding (deterministic substring check)
      → Compute fingerprints
      → Dedup (Tier 1 exact fingerprint → Tier 2 full-text search + LLM confirmation)
      → Assign topics (advisory)
      → Store claims + citations
  → Update extraction run stats
  → Propose knowledge file updates (if relevant claims found)
```

**Cost estimate per source:**
- Average source: ~10 chunks × 2-3 Haiku calls (extract + 1 gleaning always + 1 conditional) = 20-30 Haiku calls
- At ~$0.0001/call via OpenRouter: **~$0.002-0.003 per source**
- Embedding: 1536-dim embedding per claim, ~$0.0001 per claim
- Total: **under $0.01 per source ingested**

**Source size guard:** Sources exceeding 50 chunks (~60K tokens) trigger a cost-estimate confirmation before extraction starts. This prevents a 200-page PDF from silently running 150+ API calls. The `brain.ingest` function returns the chunk count and estimated cost; the caller must explicitly confirm to proceed above the threshold.

### 5.9 Error Recovery

The extraction pipeline runs as a Convex durable workflow (`@convex-dev/workflow`). Each chunk is processed as a separate step, so the workflow survives action timeouts, OpenRouter outages, and Convex restarts — it resumes from the last completed step.

**Partial failure handling:**
- Each chunk extraction is an independent workflow step. If chunk 47 fails (OpenRouter timeout, malformed response), it retries with exponential backoff (3 attempts, 5s/30s/120s delays).
- After 3 retries, the chunk is marked failed and the workflow continues to chunk 48. Failed chunks are recorded in `extractionRuns.stats` as `chunksSkipped`.
- If >30% of chunks in a run fail, the entire run transitions to `failed` status with an error message. The ghostwriter can retry the run (idempotent — already-processed chunks are skipped via content-addressable chunk IDs and fingerprint dedup).

**Resume capability:** Because chunk IDs are content-addressable and claim fingerprints are deterministic, re-running `brain.ingest` on the same source is always safe. Already-stored chunks and claims are skipped. This means "retry the failed run" is just "ingest the source again."

**Prompt regression detection:** If grounding failure rate exceeds 30% within a single run (§5.5), the run is flagged and paused. This catches model regressions and prompt failures before they pollute the Brain with ungrounded claims.

---

## 6. Retrieval Strategy

### 6.1 .md-First Retrieval (Default)

The primary retrieval mode is **read markdown files, pass to model.** No vector search, no embedding, no keyword decomposition. For a client with 200-500 claims, this is sufficient and dramatically simpler.

```
"Write about Tim's pricing philosophy"
  → Identify relevant topic(s): "pricing" (by slug match or Haiku classification)
  → brain.dump({ clientId, topics: ["pricing"] })
      → Read all accepted claims in topic as markdown
      → Read positions for topic as markdown
      → Read knowledge files (voice.md, icp.md, design.md)
      → Concatenate into single markdown document
      → Check token count
          → If ≤ context budget: return full dump (done)
          → If > context budget: activate RAG path (§6.2)
  → Return markdown context pack
```

**Why this works:** A client with 300 claims across 10 topics has ~30 claims per topic. At ~150 tokens per claim markdown, that's ~4,500 tokens per topic — well within any context window. Add 3 positions (~500 tokens each) and 3 knowledge files (~1,000 tokens each) = **~9,000 tokens total.** No retrieval pipeline needed.

**The model is the ranker.** When you pass 30 claim markdown files about "pricing" to a generation model with the instruction "write a post about value-based pricing for agencies," the model handles relevance better than any pre-filtering pipeline could. It sees the full picture and selects what matters.

### 6.2 Search Path (Fallback for Large Corpora)

When `brain.dump` exceeds the token budget (topic has 100+ claims, or query spans multiple large topics), use Convex's full-text `searchIndex` to select which claims to include:

```
Query → Full-text search on claims.title via searchIndex (top 30)
  → Follow claimEdges from top results to pull in linked claims (1 hop)
  → Deduplicate by claimId
  → For each match: return the claim's `markdown` field (pre-rendered)
  → Prepend knowledge files (always included)
  → Prepend relevant positions (always included if topic matched)
  → Token budget truncation
  → Return markdown context pack
```

The output is identical — a markdown document. The only difference is HOW the claims were selected: topic filter (small corpus) vs text search + link-following (large corpus). This is the Obsidian model: search finds the entry point, links provide the neighborhood.

### 6.3 Context Pack Format

Regardless of retrieval mode, the context pack is always a markdown document:

```markdown
## Voice & Style
{voice.md content}

## Ideal Customer
{icp.md content}

## Design & Brand
{design.md content}

## {Custom Knowledge File Title}
{custom knowledge file content — e.g. competitors.md, hiring.md}

## Positions

{position-1.md content — full position with support evidence and gaps}

{position-2.md content}

## Supporting Claims

{claim-1.md content — with frontmatter and citation}

{claim-2.md content}

...
```

The positions go first because they're what the writer actually needs. Claims are supporting evidence. Knowledge files are always-on context.

### 6.4 Retrieval Modes

| Mode | When Used | How |
|---|---|---|
| **Topic dump** (default) | Content generation, "write about X" | Filter claims by topic → return all as markdown |
| **Full dump** | "Show me everything about this client" | All accepted claims + positions + knowledge files as markdown |
| **Subtype filter** | "Show me all proof points" | Filter by subtype → return as markdown |
| **Source trace** | "What did we extract from this transcript?" | Filter claims by sourceId → return as markdown |
| **Text search + links** | Large corpus or cross-topic query | Full-text searchIndex → follow claimEdges 1 hop → select markdown files |

### 6.5 Vector Search (Available, Not Primary)

Embeddings are computed at extraction time and indexed via Convex's built-in `vectorIndex`. This is available from day 1 — Convex manages it automatically, zero infrastructure.

**When to use vector search instead of topic dump:**
- Cross-topic discovery: "find anything this client has said about hiring" when no `hiring` topic exists yet
- Similarity queries: "find claims similar to this one" for dedup review or edge discovery
- Large corpus fallback: when a topic dump exceeds context budget, vector search pre-filters
- Semantic search from UI: user types a question, get ranked claims back

**The .md-first path (§6.1) remains the default for content generation.** When writing a post about a known topic, dump the topic as markdown and let the model rank. Vector search is a discovery tool, not the generation retrieval path.

---

## 7. Knowledge Files

### 7.1 Structure

Each client starts with three default knowledge files, but ghostwriters can create additional custom files for any domain (competitor analysis, hiring philosophy, content strategy, etc.) via `knowledge.create`. The three defaults are auto-seeded from extraction; custom files are created manually.

**voice.md** — seeded from extracted `voice` and `preference` claims:
```markdown
## Tone
Direct, conversational, occasionally contrarian. Never corporate-speak.

## Signature Phrases
- "Here's what nobody tells you about..."
- "The math is simple:"

## Banned Phrases
- "leverage" (says "use")
- "synergy" (banned entirely)
- "thought leader" (cringe)

## Formatting Preferences
- Short paragraphs (1-3 sentences)
- Line breaks between ideas
- Numbered lists over bullets
```

**icp.md** — seeded from `icp`, `objection`, `offer` claims:
```markdown
## Primary ICP
B2B agency owners, $500K-$5M revenue, 5-25 employees

## Pain Points
- Feast-or-famine pipeline
- Can't systematize sales
- Founder does all the selling

## Objections They Handle
- "We've tried outbound, it doesn't work" → ...
- "Our clients come from referrals" → ...

## Customer Language
(Exact phrases from transcripts they use to describe problems)
```

**design.md** — seeded from `constraint`, `preference` claims:
```markdown
## Brand Colors
Primary: #1a1a2e, Accent: #e94560

## Visual Preferences
- Clean, minimal
- No stock photos
- Data visualizations over decorative graphics

## Content Constraints
- No competitor bashing by name
- No political content
- No income claims
```

### 7.2 Seeding Process

On first source ingestion for a new client:
1. Extract claims as normal
2. Filter claims by subtype: `voice`, `preference`, `constraint`, `icp`, `offer`, `objection`
3. Group by knowledge file type
4. Generate initial markdown draft using Sonnet (via OpenRouter)
5. Store as `knowledgeFile` with `version: 1`, `updatedBy: "system"`
6. Flag for human review in the UI

### 7.3 Update Proposals

When new sources are ingested and relevant claims are extracted:
1. Compare new claims against existing knowledge file content
2. If new information found, generate a proposed update (section + current + proposed + reason + source claims)
3. Store as `proposedUpdates` on the knowledge file
4. Surface in the UI for human approval
5. On approval: apply update, increment version, set `updatedBy: "human"`

Knowledge files are NEVER auto-updated without human approval. They are the ghostwriter's curated truth, not a computed view.

**Custom knowledge files:** Created via `knowledge.create({ clientId, fileType: "competitors", content: "..." })`. Custom files are included in `brain.dump` context packs alongside the default three — all knowledge files for the client are always prepended. The seeding pipeline only auto-creates the default three; custom files are purely human-authored.

---

## 8. Eval Framework

### 8.1 Three Evaluation Layers

#### Layer 1: Extraction Quality

| Metric | What It Measures | Gate | Method |
|---|---|---|---|
| **Grounding rate** | % of claims where quote is valid substring | ≥ 95% | Deterministic |
| **Atomicity rate** | % of claims that contain exactly one idea | ≥ 90% | LLM judge (Haiku) |
| **Subtype accuracy** | % of claims assigned correct subtype | ≥ 85% | LLM judge vs golden labels |
| **Dedup precision** | % of dedup decisions that were correct | ≥ 95% | Human review of sample |
| **Coverage (recall)** | % of important facts in source that were extracted | ≥ 80% | Human annotation of golden sources |
| **Hallucination rate** | % of claims that assert something not in source | ≤ 2% | LLM judge + human spot-check |

#### Layer 2: Retrieval Quality

| Metric | What It Measures | Gate | Method |
|---|---|---|---|
| **Relevance@10** | % of top-10 retrieved claims relevant to query | ≥ 70% | LLM judge against golden queries |
| **Recall@20** | % of relevant claims found in top-20 | ≥ 80% | Human-annotated relevance sets |
| **Topic precision** | % of claims correctly assigned to topic | ≥ 85% | Human review |
| **Citation accuracy** | % of citations that still point to valid source text | 100% | Deterministic |
| **Cross-client isolation** | Zero claims from client A retrieved for client B | 100% | Deterministic test |

#### Layer 3: Content Quality (downstream — measures Brain's contribution to generation)

| Metric | What It Measures | Gate | Method |
|---|---|---|---|
| **Grounding rate** | % of generated claims that trace to Brain evidence | ≥ 80% | LLM judge |
| **Voice consistency** | Generated text matches voice.md style markers | ≥ 85% | Deterministic lint + LLM judge |
| **Factual accuracy** | No facts in generated content that contradict Brain | 100% | LLM judge |
| **Edit distance** | Average human edits per generated post (should decrease over time) | Trending ↓ | Automated tracking |
| **Client approval rate** | % of posts approved without revision | Trending ↑ | Automated tracking |

### 8.2 Golden Dataset

Start with 10 source documents (2 per source type: transcript, website, document, pasted text, LinkedIn posts). For each:

1. **Human-annotate all claims** in the source (the "answer key")
2. **Human-assign subtypes** to each claim
3. **Human-assign topics** to each claim
4. **Write 5 test queries** per source with expected relevant claims

Format:
```json
{
  "source_id": "golden-001",
  "source_type": "transcript",
  "source_text": "...",
  "expected_claims": [
    {
      "title": "...",
      "subtype": "opinion",
      "quote": "exact quote from source",
      "topics": ["pricing", "value"]
    }
  ],
  "test_queries": [
    {
      "query": "What does the client think about pricing?",
      "expected_claim_indices": [0, 3, 7]
    }
  ]
}
```

**Expand the golden dataset as bugs are found.** Every retrieval failure or extraction miss that a user reports becomes a new test case.

### 8.3 Eval Tooling

Use **Braintrust** for eval orchestration. The integration design (from the Braintrust × Convex Agent spike, 2026-06-05):

- Capture via Agent's post-call hooks (not Braintrust model wrappers — those are v1/v2 only and silently no-op on ai@6)
- Durable transactional outbox: workpool delivery, deterministic-id idempotent upsert, deletion-aware claim, atomic scorer lease
- No Braintrust component — router through OpenRouter

**Run evals:**
- On every prompt version change (extraction prompt, gleaning prompt)
- On every model change
- Weekly regression check against golden dataset
- On-demand when investigating quality issues

### 8.4 Production Monitoring

Track these metrics in production (not just eval):

- Grounding failure rate per extraction run (alert if > 10%)
- Average claims per source by type (detect extraction regression)
- Dedup collision rate (detect fingerprint hash collisions)
- Retrieval latency p50/p95
- Context pack token usage distribution
- Knowledge file update proposal acceptance rate

---

## 9. Position Synthesis Pipeline

Positions are higher-order theses synthesized from multiple claims. A writer doesn't need "47 facts about pricing" — they need "Tim believes value-based pricing beats hourly, and here's why."

### 9.1 When Positions Are Generated

After extraction, when a topic accumulates sufficient claims (≥3 accepted claims of mixed subtypes), trigger position synthesis:

```
Topic "pricing" has 12 accepted claims (3 opinion, 2 proof, 2 story, 2 framework, 1 icp, 1 offer, 1 positioning)
  → Collect all claim markdown files for topic
  → Send to Sonnet via OpenRouter with position synthesis prompt
  → Model returns 1-3 positions with:
      - title, thesis, stance type
      - which claims support each position (by claimId)
      - tensions (contradictions or caveats)
      - gaps (what evidence is missing)
  → Store positions with markdown rendered (§4.3 format)
  → Compute sourcePackHash for staleness detection
```

### 9.1.1 Cross-Topic Position Synthesis

The best positions often emerge from COMBINING insights across topics. "Tim's pricing philosophy" + "Tim's hiring approach" + "Tim's view on agency scale" = "Tim believes you should grow revenue before headcount." Single-topic synthesis won't surface these.

**Trigger:** When a client accumulates 50+ accepted claims across 3+ canonical topics, run a cross-topic synthesis pass. This pass receives ALL accepted claims (or the top claims per topic if the corpus is too large for context) and looks for positions that span multiple topics.

**How it differs from per-topic synthesis:**
- Input: claims from multiple topics (selected by diversity — at least 2 subtypes per topic)
- Output: positions with `topicIds` containing 2+ topics
- Prompt emphasizes: "Look for beliefs that CONNECT different areas of this person's expertise — positions that wouldn't emerge from any single topic alone"

Cross-topic synthesis runs less frequently than per-topic (triggered by claim threshold, not on every ingestion) and uses Sonnet (the synthesis quality matters more here).

### 9.2 Position Synthesis Prompt

```
Given these claims about {topic_name} for {client_name}, synthesize the
client's distinct positions — what they BELIEVE, not just what they know.

A position is a stance the client takes that could anchor a LinkedIn post.
Good positions are specific ("value-based pricing beats hourly for agencies")
not generic ("pricing is important").

For each position, identify:
- TITLE: A specific, opinionated statement (not a topic label)
- THESIS: 2-4 sentences articulating the position
- STANCE: contrarian (against conventional wisdom), conventional (consensus but unique angle),
  nuanced ("it depends" with conditions), or experiential (learned the hard way)
- SUPPORT: Which claims back this up (list claim IDs)
- TENSIONS: Any contradictions or caveats in the evidence
- GAPS: What evidence is missing to make this position stronger

{claim_markdown_files}
```

### 9.3 Position Lifecycle

- Positions start as `draft` (just synthesized)
- Human reviews and promotes to `usable` or `strong`
- When support claims become `stale` or new claims enter the topic, the position's `sourcePackHash` no longer matches → auto-transitions to `stale` → re-synthesis proposed
- Positions are never auto-updated. Re-synthesis proposes a new version; human accepts.

### 9.4 Positions Feed Content Generation

When the writer asks "write about pricing," the context pack (§6.3) includes:
1. **Positions first** — the synthesized stances with support evidence and gaps
2. **Claims second** — the raw evidence for deeper grounding
3. **Knowledge files always** — voice, icp, design

The model reads the position markdown and generates content grounded in a specific stance, not just assembled from scattered facts. This is the difference between "here are some things Tim said about pricing" and "Tim believes X because of Y, and here's how to write about it."

---

## 10. Build Order

### Phase 1: Foundation (Week 1-2)
**Goal:** Ingest a source, extract claims as markdown, retrieve them as a full client dump.

1. Convex schema: `sources`, `sourceChunks`, `claims`, `claimCitations`, `extractionRuns`
2. Source ingestion (paste text + URL scrape via Firecrawl) — use `@convex-dev/workflow` for the pipeline
3. Chunking with 100-token overlap (paragraph boundaries for v1, source-type-specific later)
4. Extraction pipeline (Haiku via OpenRouter, delimiter format) with error recovery (§5.9)
5. Grounding validation (deterministic substring check) — failed claims go to `rejected` lifecycle (§5.5)
6. Claim markdown rendering (each claim stored with its .md representation)
7. Fingerprint dedup (Tier 1 exact only — per §5.6)
8. Async embedding backfill via scheduler (post-extraction, ~$0.03 per client; vectorIndex in schema, Convex manages automatically; claim storage never blocks on embedding API)
9. `brain.dump` — full-client and subtype-filtered retrieval returning concatenated markdown
10. `brain.search` — vector search for cross-topic discovery (§6.5)
11. Headless API: `brain.ingest`, `brain.dump`, `brain.search`, `brain.listClaims`, `brain.acceptClaim`, `brain.revalidateClaim`

**Ship gate:** Ingest a real transcript, extract claims with markdown, `brain.dump({ clientId })` returns readable markdown with cited claims. `brain.search({ query: "pricing" })` returns relevant claims via vector search. All via Convex durable workflows.

### Phase 2: Knowledge Files + Topics (Week 2-3)
**Goal:** Knowledge files seeded from claims. Topic-filtered retrieval working.

12. `knowledgeFiles` table + seeding (3 defaults) + `knowledge.create` for custom files
13. `topics`, `topicMemberships` tables + model-suggested assignment
14. `brain.dump` enhanced: topic filter → knowledge files + topic claims as markdown
15. `contextPackRuns` for audit trail
16. Headless API: `brain.getContext`, `knowledge.get`, `knowledge.list`, `knowledge.create`, `knowledge.propose`, `topics.list`

**Ship gate:** Ingest 3 sources for a test client, get knowledge files seeded, `brain.dump({ topics: ["pricing"] })` returns claims grouped by topic with all knowledge files prepended.

### Phase 3: Positions + Eval (Week 3-4)
**Goal:** Position synthesis working. Golden dataset eval passing thresholds.

17. `positions` table + per-topic synthesis pipeline (Sonnet)
18. Cross-topic position synthesis (§9.1.1 — when 50+ claims across 3+ topics)
19. Position markdown rendering (§4.3 format)
20. Context pack now includes positions → claims → knowledge files
21. Gleaning loop (1-2 passes after initial extraction)
22. Golden dataset (10 sources, annotated)
23. Eval pipeline (extraction + retrieval metrics) — if subtype accuracy < 85%, switch to two-pass extraction (§5.3)

**Ship gate:** Positions synthesized from claims (per-topic + cross-topic), context packs include positions first. Eval: grounding ≥ 95%, extraction coverage ≥ 80%.

### Phase 4: Quality + Optimization (Week 4-5)
**Goal:** Fuzzy dedup, staleness detection, edge discovery.

24. LLM dedup (Tier 2 — full-text search for near-matches, Haiku confirmation)
25. `topicSummaries` table + generation + eager staleness detection after extraction
26. Position staleness detection (sourcePackHash mismatch → re-synthesis proposals)
27. Knowledge file update proposals from new claims
28. `claimEdges` + post-extraction edge discovery (Haiku batch pass) — the "why?" layer; invest in edge density (Right Reasons benchmarked 100% recall on reasoning questions via edge traversal vs 0% for RAG)
29. Search path fallback (§6.2 — text search + vector search + link-following for large corpora)
30. Production monitoring (grounding rate, extraction stats, source size guards)

**Ship gate:** Topic summaries generated, stale positions flagged, knowledge file proposals surfaced. Search fallback works for large topic dumps.

### Deferred to Later Specs
- `brain.transfer(fromWorkspace, toWorkspace, clientId)` — reassign all Brain data for a client to a new workspace (client leaves ghostwriter, ghostwriter leaves platform, or client goes solo). All deterministic IDs are workspace-independent, so transfer is a `workspaceId` field update across rows — no content migration, no ID regeneration. (§2.12)
- Asset opportunities ("what can I make from this knowledge")
- Research gaps (beyond position.gaps)
- Quality rules and gate runs (the lint engine)
- Edit-learning and promotion queue
- Agent memory (separate system)
- Performance attribution loop
- Cross-client de-identified patterns
- Community detection / hierarchical clustering
- Scout/Joni research agent integration
- Transcript landing with speaker resolution
- Segment comparison and faceting

---

## 11. API Surface (Headless-First)

Every function is a Convex mutation or query, callable from UI, CLI, and MCP.

### Mutations (writes)

| Function | Input | What It Does |
|---|---|---|
| `brain.ingestText` | `{ clientId, title, text, origin, provenance }` | Store source, chunk, extract, dedup, store claims with markdown |
| `brain.ingestUrl` | `{ clientId, url, provenance }` | Scrape URL, then same as ingestText |
| `brain.acceptClaim` | `{ claimId }` | Transition claim lifecycle: candidate → accepted. Refuses `rejected` claims (must revalidate first). Asserts at least one valid citation exists. |
| `brain.revalidateClaim` | `{ claimId }` | Re-run grounding validation on a `rejected` claim. If citation quote is found in source chunk, transitions to `candidate`. Otherwise stays `rejected`. |
| `brain.staleClaim` | `{ claimId, reason }` | Transition: accepted → stale |
| `brain.editClaim` | `{ claimId, title?, summary? }` | Human edit, snapshots original values (if first edit), sets humanEdited: true, re-renders markdown from structured fields |
| `brain.addEdge` | `{ fromClaimId, toClaimId, edgeType }` | Manual edge creation (provenance: human, auto-accepted) |
| `brain.acceptEdge` | `{ edgeId }` | Promote proposed edge to accepted (enables position synthesis influence) |
| `brain.rejectEdge` | `{ edgeId }` | Reject proposed edge |
| `positions.synthesize` | `{ topicId }` | Generate positions from topic's claims (Sonnet) |
| `positions.synthesizeCrossTopic` | `{ clientId }` | Generate cross-topic positions from all accepted claims (Sonnet) |
| `positions.accept` | `{ positionId }` | Promote position: draft → usable |
| `positions.edit` | `{ positionId, thesis?, title? }` | Human edit, re-renders markdown |
| `topics.create` | `{ clientId, displayName, slug }` | Create canonical topic |
| `topics.merge` | `{ fromTopicId, intoTopicId }` | Merge topics, update memberships, flag positions stale |
| `topics.assignClaim` | `{ topicId, claimId }` | Manual topic assignment |
| `knowledge.acceptProposal` | `{ fileId, proposalIndex }` | Apply proposed update to knowledge file |
| `knowledge.edit` | `{ fileId, content }` | Direct human edit of knowledge file |
| `knowledge.create` | `{ clientId, fileType, content }` | Create custom knowledge file (any fileType string) |

### Queries (reads)

| Function | Input | Returns |
|---|---|---|
| `brain.dump` | `{ clientId, topics?, subtypes?, tokenBudget? }` | **Primary retrieval.** Concatenated markdown: knowledge files → positions → claims. If `topics` specified, filters to claims in those topics. If omitted, returns ALL accepted claims for the client, ordered by subtype (opinions first, then stories, proofs, frameworks, then rest) then by `createdAt` desc. If result exceeds `tokenBudget`, truncate claims (keep all knowledge files and positions). Returns full markdown document ready for model consumption. |
| `brain.search` | `{ clientId, query, limit? }` | **Vector search.** Cross-topic discovery, similarity, and large-corpus fallback. Returns ranked claim markdown. Available from Phase 1. |
| `brain.listClaims` | `{ clientId, lifecycle?, subtype?, topicId? }` | Filtered claim list (structured, not markdown) |
| `brain.getClaim` | `{ claimId }` | Single claim with all citations + markdown |
| `brain.getStats` | `{ clientId }` | Claim counts by subtype, provenance, lifecycle |
| `positions.list` | `{ clientId, readiness?, topicId? }` | Positions with support claim counts |
| `positions.get` | `{ positionId }` | Full position markdown with support evidence |
| `topics.list` | `{ clientId }` | Topics with claim + position counts |
| `topics.getClaims` | `{ topicId }` | Claims in topic as markdown |
| `topics.getSummary` | `{ topicId }` | Topic summary with support claims |
| `knowledge.get` | `{ clientId, fileType }` | Knowledge file markdown content |
| `knowledge.list` | `{ clientId }` | All knowledge files for client (default + custom) |
| `knowledge.getPendingProposals` | `{ clientId }` | Pending knowledge file updates |
| `sources.list` | `{ clientId }` | Sources with extraction status |
| `sources.getClaims` | `{ sourceId }` | Claims extracted from source |
| `extractionRuns.get` | `{ runId }` | Run details with stats |

---

## 12. Convex Implementation Notes

These are Convex-specific gotchas that will save Ajmal time. Most come from the cockpit phase 1 build and the scout agent spike.

### 12.1 Durable Workflows for Extraction

The extraction pipeline (`brain.ingest`) MUST use `@convex-dev/workflow` — not a bare action. Bare actions have a 10-minute timeout; a large source with gleaning could exceed it. Durable workflows survive timeouts, Convex restarts, and OpenRouter outages — they resume from the last completed step automatically.

Each chunk extraction is a separate workflow step. The workflow looks like:
```
workflow("extractSource")
  → step: chunk source (mutation)
  → for each chunk:
      → step: extract claims (action — calls OpenRouter)
      → step: glean (action — calls OpenRouter)
      → step: validate + dedup + store (mutation)
  → step: finalize extraction run stats (mutation)
```

### 12.2 Actions vs Mutations vs Queries

- **Mutations:** All DB writes. Transactional, deterministic, no external calls. Chunking, claim storage, lifecycle transitions, topic assignment.
- **Actions:** External calls (OpenRouter for extraction/gleaning, embedding API). Can read DB via `ctx.runQuery` and write via `ctx.runMutation` — never `ctx.db` directly.
- **Queries:** All reads. `brain.dump`, `brain.listClaims`, `topics.list`, etc. Reactive — UI auto-updates when underlying data changes.

**Key rule:** Actions cannot access `ctx.db`. All DB reads/writes from actions go through `ctx.runQuery`/`ctx.runMutation` into internal queries/mutations.

### 12.3 File Uploads

Source files (PDFs, docs) go through Convex storage: `ctx.storage.store(blob)` returns a `storageId`. Store the `storageId` on the `sources` record. Read the file content via `ctx.storage.get(storageId)` in the chunking step.

### 12.4 Module Path Naming

No hyphens in Convex module paths. Use `camelCase` for file names in the `convex/` directory. `brain.ts` not `brain-v1.ts`. `knowledgeFiles.ts` not `knowledge-files.ts`.

### 12.5 Response Size Limits

Convex mutations and queries have a ~1MB response size limit. A full `brain.dump` for a client with 300+ claims could approach this. If the concatenated markdown exceeds the limit, `brain.dump` should be an action that assembles the result from multiple query calls, or it should paginate. In practice, topic-filtered dumps will be well under the limit; only `brain.dump` with no topic filter for a large client might hit it.

### 12.6 Embeddings and Vector Search

The `embedding` field on `claims` is `v.optional(v.array(v.float64()))`. Embeddings are computed **asynchronously** after claim storage — not inline during extraction. This means an embedding API outage never blocks claim ingestion or markdown retrieval.

**Flow:** Extraction pipeline stores claims → `ctx.scheduler.runAfter(0, internal.embeddings.backfill, { claimIds })` → embedding action batches claims, calls OpenRouter, patches each claim with the embedding vector and `embeddingModel`. Claims without embeddings are excluded from the `vectorIndex` automatically (Convex skips documents where the vector field is null/undefined).

The `vectorIndex("by_embedding", ...)` is defined in the schema and managed automatically by Convex — zero infrastructure overhead. It's available from day 1 for cross-topic discovery, similarity queries, and large-corpus fallback (§6.5). The default retrieval path for content generation is still .md-first (§6.1). The `embeddingModel` field enables drift detection — if the model changes, a one-time re-embed backfill (~$0.03/client) brings all claims to the same vector space.

### 12.7 Scheduler for Post-Extraction Steps

After the extraction workflow completes, use `ctx.scheduler.runAfter(0, ...)` to trigger downstream steps: embedding backfill, topic summary staleness check, knowledge file update proposals, edge discovery. Don't inline these in the extraction workflow — they're separate concerns and shouldn't block the extraction run from completing.

---

## 13. Research Appendix

Organized by source repo. Each pattern includes what we took, what we adapted, and what we left behind.

### From mem0 (25.4K stars)

| Pattern | Took | Adapted | Left Behind |
|---|---|---|---|
| ADD-only extraction | Yes — extraction never updates/deletes | — | UPDATE/DELETE operations entirely |
| 3-signal hybrid retrieval | Yes — semantic + text + entity | Entity boost → topic expansion | BM25 via Elasticsearch (use Convex searchIndex) |
| Anti-hallucination UUID mapping | Yes — fingerprint-based dedup | Adapted to SHA-256 fingerprint | UUID mapping table (overkill for our scale) |
| Graph layer | Concept only | Simplified to claim edges | Full Neo4j graph store |

### From Graphiti (3.2K stars)

| Pattern | Took | Adapted | Left Behind |
|---|---|---|---|
| Two-tier dedup | Adapted — exact fingerprint → full-text search + LLM confirmation | MinHash/LSH dropped (unnecessary at 200-500 claims/client; Convex searchIndex suffices) | LSH forest index, MinHash signatures |
| Bi-temporal lifecycle | Yes — validAt, invalidAt | Simplified to 5 lifecycle states (added `rejected` for failed grounding) | Full temporal query algebra |
| Facts-on-edges principle | Took the lesson | Facts stay on claims, edges are lightweight | Edge-level fact storage |

### From Cognee (2.1K stars)

| Pattern | Took | Adapted | Left Behind |
|---|---|---|---|
| Content-addressable chunk IDs | Yes — uuid5(NAMESPACE, text) | — | — |
| Cascade extraction (3-phase) | Concept — our gleaning loop | Single-phase + gleaning vs 3 separate phases | Relationship type pre-generation step |
| Triplet embedding | Concept — embed title+summary | Simplified to claim-level embedding | Triple-as-embeddable pattern |

### From nano-graphrag (1.8K stars)

| Pattern | Took | Adapted | Left Behind |
|---|---|---|---|
| Delimiter-based output | Yes — `<\|>` field sep, `##` record sep | Same format | — |
| Entity merging importance | Yes — same concept across chunks must merge | Via fingerprint dedup | NetworkX in-memory graph |
| Minimal GraphRAG (~500 lines) | Validation that graph+vector works without communities | — | Community detection (deferred) |

### From LightRAG (36K stars)

| Pattern | Took | Adapted | Left Behind |
|---|---|---|---|
| Dual-keyword decomposition | **Left behind** — markdown-first retrieval (§3.5) replaces keyword-based search at this scale | — | Separate entity vs theme keyword indexes |
| Embed descriptions not just names | Yes — `title + summary` as embedding text, computed at extraction time, indexed via Convex vectorIndex | — | — |
| Source_id backreference | Yes — claimSources many-to-many | Via claimCitations table | Delimiter-separated source_id string |
| Map-reduce description summarization | Concept — for topic summaries | Only when > 5 descriptions | Per-entity incremental (our entities are claims, already atomic) |
| Round-robin merge with dedup | **Deferred** — no hybrid retrieval in v1 (markdown-first) | — | Retrieval fusion ranking |
| Incremental-first | Yes — ADD-only, no global re-index | — | — |

### From Microsoft GraphRAG (33.5K stars)

| Pattern | Took | Adapted | Left Behind |
|---|---|---|---|
| Gleaning loop | Yes — CONTINUE_PROMPT + completeness check | Max 2 gleanings (not N) | LOOP_PROMPT Y/N (we just do 2 fixed passes) |
| Description summarization with token batching | Yes — for topic summaries | Threshold at 5 descriptions | Recursive summarization (overkill at our scale) |
| Provenance chain | Yes — claim → citation → chunk → source | Same architecture | Parquet-based storage |
| Mixed context builder | Concept — token-budgeted context assembly | Simplified to flat ranking + truncation | Community report substitution (no communities in v1) |
| Structured output for summaries | Yes — Pydantic/Zod schema for extraction | Delimiter format for extraction, structured for summaries | — |
| Community detection (hierarchical Leiden) | **Deferred** | — | Enterprise clustering infrastructure |
| Community reports (bottom-up summarization) | **Deferred** — will revisit for topic intelligence v2 | — | — |
| NLP fast path | **Left behind** — our cheap model path is cheap enough | — | — |

### From Letta/MemGPT (16.5K stars)

| Pattern | Took | Adapted | Left Behind |
|---|---|---|---|
| Sleeptime background consolidation | Concept — background claim processing | Extraction runs are async, not real-time | Full agent-driven memory management |
| Block sharing via junction tables | Yes — topicMemberships as junction | — | Agent-per-block architecture |
| memory_rethink | Concept — knowledge files can be fully rewritten | Via proposedUpdates + human approval | Agent-autonomous rewrite |
| XML compilation for context | Concept — structured context packs | Markdown format instead of XML | In-context memory blocks |

### From LangMem

| Pattern | Took | Adapted | Left Behind |
|---|---|---|---|
| Recency bias in retrieval | Yes — time decay factor on relevance scores | 180-day decay window | Dilated-window retrieval (our corpus is small enough for flat search) |

### From Right Reasons (structured ontology, 2026)

| Pattern | Took | Adapted | Left Behind |
|---|---|---|---|
| Explicit reasoning chains via typed edges | Yes — claimEdges with `supports/contradicts/refines/depends_on` | Lightweight edges (no assertion table, reasoning captured in positions) | Full four-layer LORE→VISION→RULES→OPS ontology |
| "Why?" questions need graph traversal, not vector search | Yes — position → supporting claims → citations is a traversal | Already in the Brain's natural structure | Dolt (Git-for-data) storage layer |
| Edge traversal beats RAG for reasoning (0% vs 100% recall) | Key insight — invest in edge density before retrieval sophistication | Edge discovery via Haiku batch pass rather than manual population | EPICAL intake pipeline (their ingest problem is unsolved too) |
| Deterministic SQL over probabilistic vector search | Concept — topic filter + searchIndex for v1 | Convex indexes instead of SQL, markdown dump instead of query results | Full SQL query exposure to agents |

### From Karpathy's LLM Wiki (2025-2026)

| Pattern | Took | Adapted | Left Behind |
|---|---|---|---|
| Markdown in context > RAG for small corpora | Yes — .md-first retrieval, model is the ranker | Claims as the atomic markdown unit (not raw docs) | Plain document dumping (Brain has structured claims with frontmatter) |
| 95% fewer tokens than naive document loading | Validated — 9K tokens per topic vs loading raw transcripts | Structured extraction (claims) is the compression step | Manual wiki curation (Brain auto-generates from extraction) |
| Start simple, add vector layer when you hit the wall | Yes — embeddings computed but not indexed in v1 | Clear activation criteria (§3.5) instead of vague "when it gets big" | — |

---

## 14. Known Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Extraction hallucination (LLM invents quotes) | Medium | High — erodes trust | Deterministic grounding validation rejects; >30% failure rate flags run |
| Cheap model quality varies | Medium | Medium — extraction quality dips | Golden dataset eval on every model change; pin specific model version |
| Topic assignment noise | High | Low — topics are advisory in v1 | Model-suggested topics start as `proposed`; human promotes to `accepted` |
| Knowledge file staleness | Medium | Medium — outdated voice/icp | Proposed updates surfaced on each ingestion; freshness card in UI |
| Cross-client data leakage | Low | Critical | Every query asserts (workspaceId, clientId); test in eval suite |
| Embedding drift on model change | Low | Medium — retrieval quality drops | Re-embed all claims on model change (one-time cost, ~$1-5 per client) |
| Dedup false positives (different claims marked as same) | Low | Medium — information loss | Tier 3 LLM escalation for edge cases; human review queue for near-matches |

---

## 15. What This Spec Does NOT Cover

These are explicitly out of scope. They have their own specs or will get them:

1. **Content generation pipeline** — how Brain context becomes a LinkedIn post
2. **Lint engine** — deterministic quality rules (hard_generation, hard_display, soft_style)
3. **Replacer** — deterministic lint + grounded semantic editor
4. **Edit-learning system** — edits → promotion queue → lint/example/brain
5. **Agent memory** — adaptive preferences, session context (the other side of the two-memory boundary)
6. **Scout/Joni** — research agent for outliers and market intelligence
7. **Client approval flow** — no-login approval page with AI-applies-edits
8. **Publishing pipeline** — LinkedIn OAuth + scheduling + analytics
9. **Ops cockpit** — the Today/Clients/Agents UI
10. **Cross-client patterns** — de-identified format/hook library that compounds across workspace

The Brain is a dependency for all of these. Build it solid, and they plug in cleanly.

---

## 16. Success Criteria

The Brain v1 is done when:

1. A ghostwriter can ingest 5 different source types for a client
2. Claims are extracted with ≥95% grounding rate
3. Every claim has a canonical markdown representation with frontmatter and citation
4. Knowledge files (voice.md, icp.md, design.md) are seeded from claims
5. `brain.dump({ topics: ["pricing"] })` returns a readable markdown document with positions, claims, and knowledge files — no RAG required
6. Positions are synthesized from topic claims with stance types, support packs, and gaps
7. A writer can read the `brain.dump` output and immediately understand what the client believes and why
8. Cross-client isolation holds (zero leakage in test suite)
9. Golden dataset eval passes all thresholds
10. Every operation works headlessly (no UI-only features)
11. Extraction cost stays under $0.01 per source
12. Retrieval latency stays under 500ms for topic dump, under 2 seconds for vector search path
