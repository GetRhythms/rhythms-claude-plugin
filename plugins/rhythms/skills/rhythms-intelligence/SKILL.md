---
name: rhythms-intelligence
description: Recall and record Rhythms company memory. Use whenever the user asks about
  prior decisions, ongoing risks, team/goal context, meeting outcomes, or wants to capture
  something for future reference.
---

# Rhythms Intelligence

Rhythms maintains a private company knowledge graph of people, teams, goals, documents,
meetings, decisions, risks, accounts, and the relationships between them. **Reading and
writing this graph is core to your job, not housekeeping.**

## The memory contract (read this every turn)

You have three obligations to the graph on every substantive turn. They go out
**together with your reply, in the same final tool batch**:

1. **Record what's new.** If the turn surfaced reusable information that isn't already
   in the graph, write it with `intelligence_ingest`.
2. **Record what changed.** If the turn surfaced information that contradicts,
   supersedes, or refines something the graph already knows (priorities re-ranked,
   owners reassigned, status flipped, dates moved), write the new state with
   `intelligence_ingest`. Graphiti's temporal extraction invalidates the prior edge
   automatically.
3. **Record what no longer holds.** If the turn surfaced that something the graph
   believes is no longer true (a person left, a ticket closed, an account churned, a
   decision reversed), write that explicitly — phrase the episode as the cessation
   event ("X is no longer the owner of Y", "Account A churned on 2026-04-12").
   Graphiti will set `invalid_at` from it.

These obligations are **not phases** appended after the reply. There is no "first I
reply, then I sweep." Your final tool batch contains your user-facing message **and**
the `intelligence_ingest` calls in parallel — the same way you'd batch parallel reads.
If you find yourself about to send a substantive reply without any
`intelligence_ingest` calls in the same batch, **stop and walk the pre-send checklist
below before sending.** A substantive turn that ships without `intelligence_ingest`
calls is a bug.

The only turns that legitimately ship without `intelligence_ingest` calls are pure
navigation, trivial confirmations, and questions whose answer surfaced no new,
changed, or invalidated knowledge.

## Pre-send checklist (run this BEFORE every reply)

Right before you produce a substantive reply, walk this list. For every "yes", queue an
`intelligence_ingest` call into the same batch as the reply:

1. **Decision?** Did the user (or you, on their behalf) decide something — a priority,
   an owner, a deadline, a trade-off?
2. **Risk?** Did you flag something new at risk — a breached SLA, a missing ticket,
   a concentration, a customer signal, a load issue?
3. **Commitment?** Did the user (or someone in their org) take on an action item,
   deadline, or follow-up?
4. **Non-obvious fact?** Did you discover something via a live read or analysis that
   the graph doesn't already know?
5. **Update?** Did the conversation reveal that something the graph believes has
   changed — a new owner, a different priority, a flipped status, a moved date?
6. **No-longer-true?** Did the conversation reveal that something the graph believes
   is no longer valid — a closed ticket, a departed person, a reversed decision, a
   churned account?

One `intelligence_ingest` call per item — **never** one mega-episode covering the
whole turn. If your reply lists five discrete insights, your batch contains five
`intelligence_ingest` calls plus the reply.

## Worked example

User: *"Analyse the KG sync meeting notes from yesterday, look at the Linear tickets
active for USENG team and summarise what isn't being focused properly."*

After you've done your lookups, your Linear reads, and your analysis, your **final** tool
batch contains, in parallel:

```text
intelligence_ingest(
  body="Venkat flagged knowledge-graph de-duplication as the #1 priority during the May 19 KG sync; his action item before the Devon demo.",
  source_id="chat:{thread_uuid}#kg-dedup-priority",
  source_label="chat_observation",
)
intelligence_ingest(
  body="USENG-689 (Amgen production issue) is urgent and unassigned — customer-facing.",
  source_id="chat:{thread_uuid}#useng-689-unassigned",
  source_label="chat_observation",
)
intelligence_ingest(
  body="Ashwin owns 4 of 5 in-progress USENG tickets plus 6+ high-priority backlog items — concentration risk.",
  source_id="chat:{thread_uuid}#ashwin-concentration",
  source_label="chat_observation",
)
# Update example -- the graph thought Priya owned USENG-699, but the meeting
# notes reassigned it. Record the new state; Graphiti invalidates the old edge.
intelligence_ingest(
  body="USENG-699 ownership transferred from Priya to Ashwin in the May 19 sync.",
  source_id="chat:{thread_uuid}#useng-699-owner-change",
  source_label="chat_observation",
)
# No-longer-true example -- the graph still believes the Devon demo is a live
# risk, but the user confirmed it shipped. Record the cessation.
intelligence_ingest(
  body="Devon demo shipped successfully on 2026-05-18; the demo-risk fact is no longer valid.",
  source_id="chat:{thread_uuid}#devon-demo-shipped",
  source_label="chat_observation",
)
# ... one ingest per discrete insight, update, or invalidation ...
# AND the assistant message with the user-facing analysis + follow-up question.
```

Same batch. Same turn. No second pass. No "I'll remember it next time."

## Source-id convention

`source_id` is the idempotency key — same id replaces a prior episode. Use stable,
source-system-derived ids whenever you have one (`"linear:USENG-689"`,
`"granola:note-xyz"`), and for conversational observations use
`"chat:{thread_uuid}#<short-slug>"`. Two `intelligence_ingest` calls with the same
`source_id` are deduped, so you can safely re-record the same insight on a later turn.

## Tools

You interact with the graph through six tools — four lookup primitives, one write,
one mint:

- `intelligence_lookup_entities` — entity-first. Resolves a name or phrase to graph
  entities (uuid + type + summary). Cheapest first move; use it to turn a name into an
  anchor uuid.
- `intelligence_lookup_facts` — edge-first. Returns relationships involving a topic as
  `entities + facts` triplets. Anchor with `focal_entity_uuids` when you have a uuid.
- `intelligence_lookup_evidence` — raw episodes. Pass exactly one of `entity_uuid` or
  `episode_uuids`. Use to quote primary sources.
- `intelligence_lookup_related` — neighborhood traversal. Hand it a uuid and get back
  the edges touching it plus the nodes on the other end.
- `intelligence_ingest` — write memory (see the contract above). Also the right
  primitive for **correcting** memory: ingest a contradicting episode ("X is no
  longer the owner of Y") and Graphiti's temporal extractor invalidates the prior
  edge automatically — you never need a separate "forget" call.
- `intelligence_mint_document` — synthesise a shareable markdown document grounded in
  the graph.

## Retrieving well — the graph is memory, not the source of truth

The graph is a fast, tenant-wide **index of what we've learned** — decisions, owners,
risks, history, provenance. It is not guaranteed to be current, complete, or
unambiguous. A great answer treats a lookup as the *start* of an investigation, not the
end:

1. **Orient** — resolve the anchors and read the immediate relationships.
2. **Drill** — follow the references the first results expose; a fact almost always
   points at another entity worth opening.
3. **Verify** — for anything whose truth changes over time, confirm the live state at
   the source before you commit to it.
4. **Shape** — lead with the durable answer, mark how fresh it is, and cite where it
   came from.

### Drill down — follow the chain, don't stop at hop 1

The value of the graph is its edges. A single lookup rarely answers a real question; the
first result names the *next* thing to open. Keep going until the question is answered
or the neighbourhood is exhausted — a few hops is normal; stop when new lookups stop
adding relevant entities.

- A decision often references an artifact ("we agreed to open a Jira RCA") → resolve and
  open that work item; don't report the decision without its follow-through.
- A risk points at the initiative that mitigates it → open the initiative → its owner →
  its latest status.
- "Who's accountable for X end to end" walks ownership up the chain: initiative → parent
  goal → owner → manager.
- An action item names an owner → open the owner to see their other commitments when the
  question is about load or follow-through.

Chain uuids forward at every hop (resolve a name once, then anchor `*_facts` /
`*_related` / `*_evidence` on its uuid). Issue independent drills in parallel; chain only
when a later call depends on an earlier result.

### Verify against the source when the answer can go stale

The graph lags reality. Before you state a fact whose truth **changes over time**, decide
whether to confirm it live:

- **Volatile — verify at the source when you can:** open/closed status, current
  owner/assignee, progress numbers / metric values / KR attainment, due dates and
  timelines, "is it done yet", "what's the latest".
- **Durable — trust the graph:** who built or decided something, history, what happened
  in a past meeting, prior context and rationale, relationships that don't expire.

How to decide and act:

- Read the freshness signal on the result — an entity's `last_seen`, a fact's `valid_at`.
  If it's old relative to the question and the field is volatile, treat the graph value
  as a lead, not the answer.
- If the entity carries a source pointer (`source_system` + `external_id`, e.g. a
  Linear/Jira work item) **and you have a connected tool that can look it up** (by its
  `external_id`, or by the source `url` you can read from `intelligence_lookup_evidence`),
  fetch the current state from that source, then answer with both ("graph had it open as
  of May 3; confirmed in Jira just now — closed").
- If you can't verify (no connection, no access), answer from the graph but **timestamp
  it explicitly** ("as of <date>, per our memory") and offer to check the source. Never
  present a stale volatile fact as present-tense truth.
- `intelligence_lookup_evidence` returns the underlying episode; its `content` carries
  the source `url` / `external_id`, which is how you locate the live record to verify.

### Disambiguate before you answer

- **Same name, different people.** If a name resolves to more than one Person (two
  "Hasan"s), don't default to the most recently-active one. Separate them by team / role
  / what they actually work on, and either answer per-person or ask which one. Surfacing
  the wrong same-named person is worse than asking.
- **Same word, different things.** A topic like "email notifications" can span distinct
  surfaces (product notifications vs marketing/webinar email vs growth nudges vs an
  inbound mail connector). When the results split across surfaces, separate them rather
  than blending them into one cloud — or ask which surface the user means.
- **Aliases / acronyms** (MBR, ELR, QBR, RCA) → resolve to the specific entity or series;
  don't guess.
- **First person** ("my", "our", "who do I report to") → resolve to the caller's own
  Person / Team first, then anchor on it.

### Be complete on "all of X" questions

"Who owns X", "list everyone who touched Y", "all the risks to Z" are *completeness*
questions, not best-match questions. Don't stop at the first page of
`intelligence_lookup_facts` — anchor on the entity and use `intelligence_lookup_related`
to enumerate the full neighbourhood, raise the `limit`, and if results are still capped,
say the list may be partial rather than presenting a truncated set as the whole.
Distinguish **who built / owns it** (durable) from **who's actively touching it now**
(recent) and give both — leading with recent activity alone is the classic wrong answer.
A recent contributor is still a contributor: recency orders an enumeration, it never
filters it — never drop someone from a "who all / list everyone" answer just because
their work is newer.

### Ground every non-trivial claim

Prefer structured facts and quoted episodes over paraphrase. When accuracy matters, pull
`intelligence_lookup_evidence` and cite the source + date. Treat extracted/inferred edges
as such when you lean on them. If two memories conflict, surface both with their dates and
provenance rather than silently choosing one.

### Shape the answer

Lead with the durable, authoritative answer; then recent activity as separate context;
then the source and as-of date; then your confidence and any gap ("the graph has nothing
on the marketing side — want me to check the campaign tool?"). "Nothing in the graph" is
not "doesn't exist" — say which it is and where to look next.

## Looking up before acting

### Graph context sweep

For every substantive task, query the graph as part of your context gathering. This
applies broadly: analysis, summaries, planning, OKR work, document work, status checks,
prioritisation, risk reviews, connector/source investigations, and "what should we do"
questions. The graph is not only for explicit memory questions — it is the tenant's
accumulated operating context.

External/source reads complement graph lookups; they do not replace them. When the task
calls for Linear, Slack, Granola, CRM, Google Workspace, web search, Rhythms APIs,
document reads, or OKR search, run the relevant graph lookup(s) in the same
context-gathering batch whenever possible. Use external/source tools to verify freshness,
fill gaps, and fetch live fields; use the graph to recover prior decisions, risks,
ownership, context, and provenance.

Default sweep pattern:

1. **Resolve anchors.** Use `intelligence_lookup_entities` for every named account,
   person, team, goal, document, meeting, ticket/work item, risk, initiative, or topic
   unless you already have a uuid from page context or an explicit entity mention.
2. **Read relationships.** Use `intelligence_lookup_facts` for the resolved anchor(s),
   or for the natural-language topic when no clean anchor exists.
3. **Explore neighborhood.** Use `intelligence_lookup_related` for important anchors
   when the user asks for status, context, planning, ownership, risk, dependencies,
   "what's connected", "what's going on", or any answer that benefits from adjacent
   entities.
4. **Check evidence.** Use `intelligence_lookup_evidence` when the answer needs
   citations, when graph facts look surprising, when reconciling graph context with
   live source data, or before making a high-impact claim.

Skip the graph context sweep only for trivial confirmations, pure navigation actions,
mechanical formatting with no business content, or when the graph tools return
`intelligence_not_enabled`. When in doubt, look it up.

## How to pick a lookup tool

Compose them — each call is cheap, structured, and tenant-scoped.

| Question shape | Tool |
| --- | --- |
| "Do we know about X?" / "Is there a record for X?" | `intelligence_lookup_entities` |
| "What decisions involve X?" / "Who owns Y?" | `intelligence_lookup_facts` (anchor with uuid when you have one) |
| "Where did we hear that?" / "Show me the source" | `intelligence_lookup_evidence` |
| "What's connected to X?" / "Who's around this account?" | `intelligence_lookup_related` |
| "How is X going?" / "What's going on with X?" / "Where do we stand on X?" (multi-section answer) | `intelligence_mint_document` (after using the lookups to gather context) |
| "Write me a brief / recap / summary / register built from what we know" | `intelligence_mint_document` |

## Research strategy: fan out vs drill down

Match the shape of your lookups to the shape of the question. The lookups compose
cheaply, so the cost of getting this wrong is a slow, padded answer — not a failure —
but matching well gets you to a grounded answer in the fewest calls.

**Fan out** when the question is broad or open-ended — a status rollup, a risk
register, "what's going on with X", "what should we focus on", anything spanning
multiple entities or whose scope you don't yet know:

1. Resolve the main anchors with `intelligence_lookup_entities` (often several names
   at once).
2. Gather breadth across them **in parallel** — `intelligence_lookup_facts` and
   `intelligence_lookup_related` on each anchor in the same batch — before you narrow.
3. Let the breadth surface the threads that matter, then drill into those.

**Drill down** when the question is specific and points at one thing — "who owns Y",
"why did we decide Z", "what's the status of ticket Q":

1. Resolve the single anchor with `intelligence_lookup_entities`.
2. Go straight to the relevant edges with `intelligence_lookup_facts`
   (`focal_entity_uuids` set to the anchor).
3. Pull `intelligence_lookup_evidence` only when the user needs the "why" or a
   quotable primary source.

Always **chain uuids forward**: feed the uuid from `intelligence_lookup_entities` into
the `*_facts` / `*_related` / `*_evidence` calls — never guess a uuid. Stop as soon as
you can answer; redundant lookups slow the turn without improving the answer. If the
graph genuinely has little on the topic, say so plainly rather than padding with more
speculative lookups.

## Mint proactively, not just on request

Mint whenever your reply would itself be a synthesised, multi-section artifact —
a status brief, an account rollup, a risk register, a recap of a meeting or
quarter, a decision log, an ownership map, a QBR section, a "what's going on with
X" answer that pulls together decisions + risks + actions + open issues. Do not
wait for the user to literally ask for a "document".

Concrete signals you should mint:

- Your reply would have **three or more sub-sections** ("Open Issues", "Decisions
  Made", "Action Items").
- Your reply pulls evidence from **multiple distinct sources** (lookups, tickets,
  meeting notes) into one synthesised view.
- The user asked a **scoped status question** about an account, team, initiative,
  or goal whose answer cuts across many memories.
- The user explicitly asked for a brief, recap, summary, report, register, or
  rollup.

Minting produces a real Rhythms Document the user can open, share, and edit, with
provenance edges back to the source episodes. Re-minting the same artifact (same
topic + audience + focals) returns the same document — it does not duplicate. When
you mint, your chat reply becomes short: name what you minted, render the returned
document URL as a markdown link, and call out 2-3 headline insights — do not paste
the document body back into chat.

A mint turn still runs the pre-send checklist: queue `intelligence_ingest` calls
for any new insights the conversation surfaced *around* the mint request.

## Thorough ingestion standard

Aim to record as much reusable business information as possible. A good memory sweep
captures the durable operating facts someone would expect Rhythms to know later:
decisions, owners, responsibilities, commitments, risks, blockers, dependencies,
deadlines, priorities, status changes, meeting outcomes, customer/account signals,
process rules, document substance, source-system observations used in your analysis,
and explicit invalidations or reversals.

Split compound findings into one `intelligence_ingest` call per discrete durable
insight. If an analysis finds three risks, two owner changes, and one decision, queue
six ingests, not one summary. Prefer stable source IDs from source systems
(`linear:...`, `granola:...`, `rhythms:...`) when available; otherwise use
`chat:{thread_uuid}#<specific-slug>` or `chat:document:{document_uuid}#<insight-slug>`
for document substance. Use specific, factual bodies that preserve names, dates, IDs,
owners, and the relationship predicate in natural language.

Thorough does **not** mean indiscriminate. Before writing, skip duplicates already
returned by graph lookups unless the fact changed; skip trivia, pure navigation,
unsupported guesses, and unclear/private PII. When live source data corrects or
supersedes graph context, ingest the new state explicitly so Graphiti can invalidate
the old one.

## What NOT to record

- **Things the graph already knows and that haven't changed.** If a `intelligence_lookup_*`
  call already returned the fact, there's no value in re-recording it. Only write when
  the fact is new, has changed, or has been invalidated.
- **One-off facts the user doesn't want stored.** "What's 2+2" is not memory.
- **PII you don't have a clear reason to retain.** Personal phone numbers, home
  addresses, etc. belong outside the graph.

## Render links

When citing returned entities, format them as markdown links using the URL patterns in
the Rhythms MCP server instructions. Use the entity's own name as the link text — never
paste a raw URL inline.

## Correcting and invalidating memory

You never call a separate "forget" tool to fix a wrong fact. The graph is
evidence-based: to invalidate a memory, ingest the *new* state via
`intelligence_ingest` and Graphiti's bi-temporal extractor will set `invalid_at`
on the superseded edge automatically. Examples:

- Wrong owner: ingest `"USENG-699 ownership transferred from Priya to Ashwin on
  2026-05-19."`
- Reversed decision: ingest `"The proposal to ship the Devon demo on May 18 was
  reversed; the demo will now ship on May 25."`
- Departed person: ingest `"Priya left the company on 2026-04-12 and is no longer
  the owner of USENG-699."`

These are normal `intelligence_ingest` calls, not a special "forget" workflow.

## Final check before you send

If your reply contains analysis, recommendations, prioritisation, ownership claims,
dates, risk language, or discovered facts — and your final tool batch contains zero
`intelligence_ingest` calls — **you missed the sweep**. Add the `intelligence_ingest`
calls to the same batch before sending. Do not send "I'll remember that next turn" or
apologise after the fact; the value is in capturing the memory, not in promising to.
