# Extensions to Event Modeling — parked

Ideas for extending Event Modeling beyond what Dymitruk and Dilger describe.

**Deliberately not applied to any model.** The orthodox model is built first, by the book, so that
any extension can be measured against it rather than blended into it — FnEmail's
[`event-model.md`](https://github.com/IvanTheGeek/FnEmail-Model/blob/main/docs/event-model.md)
is that measuring stick, and its repo's AGENTS rule 10 enforces the parking from the model's side.
Nothing here should leak into an orthodox model.

Status: **captured, not developed.** Moved here 2026-08-08 from FnEmail-Model, where this document
lived as `docs/event-model-extensions.md` (its drafting history is in that repo's git); Ivan ruled
that parked method-layer material belongs in the method repo now that it exists. Section numbers
are unchanged — three repos cite them.

---

## 1. Multiple models for different aspects

One event model per *aspect* of the system rather than one model for the whole thing.

The trigger for this was a concrete question in the SMTP model: is `DataPhaseEntered` a domain
fact or protocol bookkeeping? Under one model, that question has no good answer — protocol state
and domain state compete for the same timeline and the same completeness check. Split into two
models and each becomes coherent on its own terms.

Candidate aspects for FnEmail:

- **Protocol model** — session sequencing, verbs, reply codes, RFC conformance
- **Domain model** — mail as a business fact: accepted, delivered, bounced
- **Operational model** — queues, retries, capacity

Open questions this raises:

- What is the interface between models? Presumably events — one model's event is another's
  external (yellow) event, i.e. the Translation pattern doing real work between *our own* models.
- Does the completeness check run per model, or across the set? If per model, what stops a field
  being complete locally and orphaned globally?
- Does this multiply the ownership swimlanes, or replace them?

Prior art to check: whether Dilger's *Dynamic Consistency Boundary* chapter (p626) is addressing
a related problem from a different direction. *(Dated note, 2026-08-08: aggregates and DCB were
ruled out of scope on 2026-08-07 as a DDD import, not an Event Modeling question — see
FnEmail-Model's `docs/DECISIONS.md`. This check stays unrun unless that ruling is reopened under
rule 4.)*

⚠️ **Correction.** An earlier note held that FnEmail's H3 (`RecipientDirectory` unsourced) was the
strongest motivation for this extension. It is not. H3 resolved as a **separate context** —
different actors, timescales, normative source and change cadence — and separate contexts joined by
Translation are orthodox Event Modeling, needing no extension at all. See FnEmail-Model's
`event-model.md`, *H3 resolved*.

This extension is narrower than it looked: it is about splitting **one** context into layers
(protocol / domain / operational), where every layer shares a charter and an owner. That case is
still unsupported by the corpus, and the kit still has no mechanism for a second model. But it must
not be justified with examples that are really separate contexts.

## 2. GUI as its own model

A corollary of (1): the user interface gets its own event model rather than occupying the
storyboard row of the system model.

Motivation: the storyboard row conflates two things — *what triggers a state change* and *what a
human sees*. For a protocol server these are barely related. Dymitruk's actor swimlanes already
gesture at the separation by letting one lane be an admin and another a user; this takes it
further and gives the UI a timeline of its own.

Open question: if the GUI is its own model, is a screen still an element of the system model at
all, or does it become purely a consumer of the system model's read models?

## 3. Steps, Paths and Treks — example data as the test suite

The largest of the three, and the one with the clearest payoff.

> ✅ **Dated note, 2026-08-08: this section has graduated in part.** Steps and paths went from
> parked extension to working architecture — the generic form now lives in [`layering.md`](layering.md)
> and [`path-and-step-form.md`](path-and-step-form.md), and every walk in
> [FnEmail-Model's paths directory](https://github.com/IvanTheGeek/FnEmail-Model/tree/main/docs/paths)
> instantiates it. The parked status still governs the rest of this document. What remains live
> *in this section* is what has not been built or ruled: trek bounding and promotion (question 3),
> test-level mapping (question 4), per-actor outcomes (question 3's open tail), and the
> RFC-testbed reading below.

### The idea

- Every slice traversal is a **step**, and a step carries **specific sample data**.
- An ordered run of steps is a **path**.
- Multiple paths exist over the same slice inventory.
- Paths compose into larger paths, and ultimately into a **trek** through the whole system.
- Meaningful steps/paths/treks are **promoted** to become the actual tests that verify the
  system — at whatever test level each corresponds to.

The model stops being a document that tests are written *from* and becomes the artifact the
tests *are*.

### What it fixes

Event Modeling deliberately shows **one** non-branching way through. Dymitruk is explicit that
this is the point — branching timelines are what make flowcharts unreadable. The cost is that
alternate flows exist only as per-slice scenarios: they never compose into a narrative, so
nothing in the model describes *a whole session going wrong*.

This keeps the single timeline and moves multiplicity into a **second dimension** — paths over
the same slices — instead of branching the board. The property Dymitruk is protecting survives;
what it costs is recovered.

### Canonical anchors

Not as far from orthodox as it looks. Three existing pieces, never joined up:

- **"Step" is Dymitruk's own term.** *"Each workflow step is tied to either a command or a
  view/read-model"*, and *"a workflow step is considered to be repeated on the event model if it
  uses the same command or view."* He already separates the slice from the traversal of it.
- **Workflow Step Contracts already exist** in `eventmodeling-checking-completeness` — explicit
  pre/postconditions per step, defined so teams can work in parallel, and otherwise unused. They
  are exactly the path-joining rule: A+B composes iff A's postconditions satisfy B's
  preconditions.
  **A contract is a GWT with the example data removed** — the schema of which a step is one
  inhabitant. `GIVEN` is the precondition, `THEN` the postcondition, `WHEN` the step itself.
  Contracts are therefore not extra work on top of scenarios; they are the same artifact typed.
- **Example data is mandated**: *"Also put all relevant information in it. The more realistic the
  data the better."*

### Why RFC 5321 is an unusually good testbed

**The RFC ships the paths.** ✅ Verified 2026-08-05 against the archived text
(FnEmail-Model's `docs/rfc/rfc5321.txt`). Appendix D contains four worked scenarios — D.1 typical
transaction, D.2 aborted transaction, D.3 relayed mail (two steps), D.4 verifying and sending —
each a complete wire trace with concrete addresses, domains and reply codes.

Under this extension those are not documentation. They are **promoted acceptance tests authored
by the standards body**, and RFC conformance becomes "these paths pass." Most domains require
inventing example data; here the normative spec supplies it.

⚠️ **Two qualifications found on verification:**

1. **Every example uses `EHLO`** — five occurrences, zero of `HELO`. Under a HELO-only scope the
   paths are RFC-*derived*, not RFC-*quoted*. A promotion scheme needs to distinguish the two,
   because only one of them is evidence of conformance.
2. **The scenario labeled "typical" is not the clean one.** D.1 contains a `550` rejection
   mid-flow, as does D.2. The clean single-recipient paths are D.3 and D.4. Whatever picks the
   "golden path" cannot just take the one the source calls typical.

### The tension worth keeping

Dymitruk's flat-cost-curve argument rests on slice independence: *"implementing any other
workflow step does not cause the need to revisit this already complete workflow step."* Paths
explicitly exercise cross-slice composition — the thing that claim says needs no checking.

That is the valuable part, not a conflict. A path that fails while every constituent step passes
has found coupling the method says should not exist. No current artifact surfaces that.

### Open design questions

1. **Does example data belong to the step or the path?** **Settled: the step.** A step is a
   *(slice, data)* pair — an identified atom that participates in many paths. An earlier draft
   argued the opposite (slice as slot, path supplies data) on the grounds that data-in-step
   "collapses back into scenarios." That was wrong: a scenario is per-slice GWT, whereas a step
   is a reusable node. Data-in-step is also the only version that supports §8, since a real event
   log needs identified atoms to match against.
   Open sub-question: **when are two steps the same step?** Without an answer, every distinct data
   value spawns a node and the catalog stops being navigable.
2. **What makes composition legal?** Step contracts are the obvious answer, but they must
   actually exist first. Nothing in `event-model.md` v0.2 has them yet. *(Dated note: they landed
   in v0.3 — FnEmail-Model commit 4f6379a, 2026-08-05, every slice — so this precondition is
   met.)*
3. **What bounds the trek space?** Free composition explodes combinatorially. "Meaningful" needs
   a promotion criterion — coverage-driven, risk-driven, or explicit curation.
   **Terminology, settled:** *happy* is descriptive (the path reaches its intended successful
   outcome; many paths qualify); *golden* is a **designation** (the coverage baseline, the viewer
   default, what documentation leads with). Being clean does not make a path golden. So the
   question is not "find the golden path" but "**decide which path to designate golden, and by what
   criterion**" — a product decision, not something discoverable in the model.
   **Open:** outcome appears to be relative to an actor. In the first worked path the session is a
   complete success for the server, partial for the sender, and a failure for one recipient. Should
   a path carry an outcome per actor lane rather than a single verdict?
4. **Test-level mapping.** Step → slice test, path → integration, trek → acceptance is the
   intuitive reading. Stating it determines whether promotion is mechanical or a judgement call.
5. **On the board or beside it?** Drawing paths on the timeline risks re-creating the branching
   mess the single timeline exists to prevent.
6. **Relationship to Dilger's "chapters"** (blue arrow grouping slices) — coarse grouping that
   already exists. Is a chapter a degenerate path, or an orthogonal concept?
7. **Naming — avoid "workflow" for the business-level grouping.** Dymitruk already uses *workflow
   step* for one slice, i.e. the technical unit. Three distinct things are in play and two are
   already named:

   | Thing | Name | Altitude |
   |---|---|---|
   | One slice | **workflow step** (Dymitruk) | technical unit |
   | A named business grouping | **chapter** (Dilger) | business narrative |
   | A concrete traversal with data | **path** (this extension) | execution instance |

   Caveat: a chapter groups *adjacent* slices. If business groupings must span non-contiguous
   slices, chapters need extending from "contiguous group" to "named semantic group" — at which
   point it may be a fourth construct rather than a reuse.

   ⚠️ **Superseded 2026-08-07, the other way — questions 6 and 7 both.** The settled terminology
   is **workflow** for a named group of slices, never *chapter*: workflow is Dymitruk's word 241×
   to 10× in the corpus, *chapter* is Dilger's, and workflow nesting has no fixed depth — which
   absorbs the "three distinct things" concern without minting a new name. FnEmail-Model commit
   429be8e; register rows in its `docs/DECISIONS.md`. The table above keeps its original
   reasoning per rule 4.

### Prerequisite

Step contracts must be added to the orthodox model before any of this can be built. That is
already listed under *Deferred* in `event-model.md` — this extension is the reason to prioritize
it. ✅ *Met in v0.3 (FnEmail-Model commit 4f6379a): every slice carries a contract.*

## 4. Two classes of rule: normative vs. operator

Some rules are **hard-lined** — an external authority defines them and the system has no
discretion. RFC 5321 for a mail server; IRS regulations for tax software. Others are **operator
or business configuration** — chosen locally, within what the hard rules permit.

The clean example: RFC 5321 defines what a valid email address *can* be. A given server operator
may accept only a subset. Both are rules; only one is negotiable.

Implications to work out:

- Are these two kinds of scenario, two kinds of slice, or two models?
- Under §3, RFC-defined paths are **hard-lined** — failing one is a conformance defect. Operator
  paths are per-deployment and their expected outcomes vary by config. That means a path needs a
  *class*, and the promoted test suite is really two suites.
- Does an operator rule ever *loosen* a normative one? If never, that is a checkable invariant:
  the operator's accepted set must be a subset of the RFC's.

**The normative set is a versioned graph, not a document.** RFC 5321 is updated by RFC 7504, which
adds two reply codes. So "RFC-conformant" is a claim about a *set* of documents at a point in time,
and the set can grow without our model changing. A conformance claim has to name its set and its
date, or it silently rots.

**Normative rules can contain operator choices.** RFC 7504 §3 is a clean specimen: it hard-lines
*when* `521` must be used, then says that afterwards the server **MAY** continue replying `521` or
**MAY** close the connection. So the two classes are not cleanly separable per-rule — a single
normative rule can delegate part of itself. Any scheme that tags rules as hard or soft needs to
handle rules that are both.

## 5. Business errors vs. technical errors as separate models

Dymitruk is firm that the model shows **business/domain** errors, not technical ones.

The extension: model both, in **separate models**, and let a viewer merge them — with visibility
selectable by the reader. The domain model stays clean, and the technical detail exists without
polluting it.

This is where the SMTP work keeps landing: `DataPhaseEntered` and a `503` sequencing error are
protocol facts, not domain facts, and forcing them onto one timeline is what makes H1 and H2
awkward. Two models dissolves both questions. *(Dated note, 2026-08-08: FnEmail-Model's
`model-altitude.md` §3 has since classified `DataPhaseEntered` as product-tier, not protocol —
the awkwardness this section responds to is now framed as a contested G1 destination rather than
protocol-vs-domain. The `503` half of the motivation stands. Second dated note, 2026-08-09: H1
closed — the event is `DataRequested` now, renamed as a capture of the client's request under
[`event-kinds.md`](event-kinds.md); this section keeps the old name as the record of what
triggered it.)*

Related: Dilger appears to advocate multiple domain models directly, stacking a short HELO line
above or below a fuller EHLO model — which is the same move applied to protocol variants rather
than to error classes.

## 6. The live model viewer

Paths, models and layers are only useful if they can be selected. The intended artifact is a
**live viewer**, not a static board:

- Choose a path or trek, or step through interactively
- Choose which models to include (domain / protocol / GUI)
- Choose depth and layer

With **persona defaults**: a CEO view that is very high level; a technical-ops view; an accounting
view; a contributor view that follows exactly what happens on the technical side. One artifact,
many audiences — the same model serving as user documentation, onboarding, and technical
reference depending on which knobs are set.

This is also the answer to §3's question 5 (board or beside it): **neither** — paths are selected
in a viewer, so the static board never branches.

## 7. Graphs

If steps are nodes and paths are walks, the model is a graph and graph tooling applies directly:

- **Forensics** — an operator's event log is a walk. Which known walks reach this error node, and
  where does the log diverge? (see §8)
- **Coverage** — promotion becomes spanning-tree-plus-unique-edges rather than a judgement call
- **Reachability** — which slices can never be reached? which errors have no path to them?

## 8. Paths as a support and diagnosis artifact

A use for §3 that has nothing to do with testing.

An operator hits an error and hands over their event log. Because every event leading to the
error is on the path, the failure can be traced back through the exact sequence that produced it
— not reconstructed from guesswork or partial logs.

This is a strong argument for **data living in the step** (§3, question 1). If data lives on the
path, an incoming log has no atoms to match against. If steps are identified nodes, the log maps
onto a known sequence and the point of divergence is detectable mechanically.

**The mechanism is already canonical.** Dilger, *Handling Metadata* (p547):

> *"If a problem arises with a command, correlation and causation IDs enable us to see exactly
> what the user did before the problem and, if necessary, replay all actions to restore the system
> to the exact state at the time the command was issued."*

So correlation/causation already give you the trail and the replay. What this extension adds is
the **known path to diff against** — replay tells you what happened, the path catalog tells you
what was *supposed* to happen and where the two diverge. That is the part that does not exist yet.

Practical consequence: metadata design is a prerequisite alongside step contracts. A path catalog
is only matchable against real logs if those logs carry correlation and causation IDs from day one.

## 9. The altitude gate sequence and the four tiers

A five-gate test for what belongs in a model at all, and a four-tier taxonomy — **Domain**,
**Product**, **Protocol**, **Infrastructure** — for what does.

**Unlike everything else on this list, this section was applied before it was parked.** It ran
FnEmail's altitude decisions from 2026-08-05 and was unapplied on 2026-08-10, when Ivan ruled that
the apparatus was an agent's invention he had never ratified. This is a standing change, not a
correction: the scheme was accepted and is now unadopted, so it takes no ⚠️ (this repository's
AGENTS.md rule 10). It lived in FnEmail-Model's
[`docs/model-altitude.md`](https://github.com/IvanTheGeek/FnEmail-Model/blob/main/docs/model-altitude.md)
§2.2 and §2.3, with the nine classified events in its §3; that repo's git holds the removed text.

### What it is

Apply the gates in order. A fact that fails an earlier gate does not reach later ones.

| # | Gate | Question | Footing |
|---|---|---|---|
| **G1** | **Destination** | Does anything in this model consume it? | Dymitruk, `what-is-event-modeling/index.md` l. 167 — **direct** |
| **G2** | **State change** | Does it change what the system will accept or produce next? | Dymitruk, same article l. 120 — **direct** |
| **G3** | **Charter** | Is it a fact *about* the charter, or about how we happen to satisfy it? | ours |
| **G4** | **Substitution** | Does it survive replacing the implementation? | ours |
| **G5** | **Interpretation** | Could an outsider read it without our rules? | Dilger, Ch. 5 pp. 90–91 — **direct**, scoped to boundary crossing |

**G1 — Destination.** *"All information has to have an origin and a destination."* A fact with no
consumer is not low altitude; it is **not in the model**. Corollary: a fact can be promoted into
the model by *adding* a consumer, so an altitude argument must name the consumer rather than appeal
to taste.

**G2 — State change.** *"only state-changing events are to be specified"*, the article's example
being a guest who merely viewed a calendar. For a protocol server, "state" includes **which
commands are now legal**, which is what saves facts that would otherwise look like transcript
noise.

**G3 — Charter.** Separates **domain** from **product**. A fact the charter requires is domain; a
fact arising from a choice that could have been made differently while still satisfying the charter
is product. Test: find the normative clause. **MUST** → domain. **MAY**/**SHOULD**, or no clause at
all → product. Both are modeled; product facts are expected to churn.

**G4 — Substitution.** Separates **protocol** and **infrastructure** from the two above, in two
forms, and the right one must be chosen. The *wrong* form — "would this exist if we didn't use
SMTP?" — is meaningless for a charter that names the protocol. The right forms are: *"would this
exist under a different conformant implementation of the same charter?"* (different socket library,
concurrency model, storage — if no, it is infrastructure); and *"does this fact exist only because
of the wire's encoding, or does it exist in the conversation the wire is encoding?"* (a phase
transition, a line terminator, a three-digit reply code are encoding; an identity claim, a return
path, a refusal are conversation — if encoding only, it is protocol).

**G5 — Interpretation.** Only for facts crossing a model boundary. *"A system using these events
needs to know exactly what it means to 'add an item.'"* If a consumer would need our rules to make
sense of it, it stays inside. This is the gate that forbids exporting reply codes.

| Tier | Definition | In the model? |
|---|---|---|
| **Domain** | The charter is *about* this fact. Passes G1–G4. Survives any conformant reimplementation. | Yes — first-class event |
| **Product** | Produced by a decision we made and could remake while staying conformant. Passes G1, G2, G4; fails G3. | Yes — event, flagged as ours, expected to churn |
| **Protocol** | Exists only as an artifact of the wire encoding of the conversation. Fails G4's second form. | Only if the charter names the protocol *and* G1 passes — otherwise collapsed at translation |
| **Infrastructure** | Exists because of how we run the software. Fails G4's first form. | Never an event. May be metadata. |

The taxonomy's own headline claim was that a tier is not a property of a fact: *"Protocol is a tier
only relative to a charter … The facts did not move. The charter did."* For an e-commerce model
everything below `MessageAccepted` is protocol and disappears; for a charter naming RFC 5321, most
of it is domain.

### Whose it is

**Corpus: silent. Our stance: invent** (AGENTS.md rule 9).

Invented by an agent, in FnEmail-Model commit `d0f1913` (2026-08-05,
`Co-Authored-By: Claude Opus 5`), which says so in its own message: *"The rest is ours and marked as
such: a five-gate sequence, a four-tier taxonomy, and a classification of all nine events."* The
marking held: G3 and G4 carry *ours* in the gates table's own footing column, and §2.3 opens
*"Ours. The corpus has no such taxonomy."* What never happened is ratification. Asked about the
scheme on 2026-08-09, Ivan said he was *"honestly not sure what they are referring to"* — by which
time the tier vocabulary had spread to other documents in both public repositories and was being
cited as method.

**Where the search looked**, since rule 9 makes an unsourced absence claim worthless. The terms
*altitude, granularity, level of detail, abstraction, zoom, protocol, infrastructure, subdomain,
core domain* and *context map* appear **zero times** in Dilger's Ch. 8 (Domain-Driven Design),
Ch. 5 (Internal vs External Data), Ch. 7 (Stream Design) and Ch. 44 (DCB); Dymitruk's canonical
article contains no discussion of choosing a level of detail; and the `agent-modeling-kit` skills
have no vocabulary for it and no mechanism for a second model at all — every skill resolves one
board ID. Recorded in the private research repo's `research/model-altitude-theory.md` §0.

**That absence is narrower than it reads, and the qualification matters here.** It is a term search
over four chapters and one article, not a concept sweep, and one counter-instance is already
recorded against it: explaining the method to Vaughn Vernon, Dymitruk describes lane position plus
visibility doing exactly this job — *"hide these two lower swim lanes when we're just talking about
the business"* (machine transcript, hence the lowercase and the missing punctuation;
`ufKgwjsD1l8` [01:17:15]). So the corpus is silent **in words** while enacting something in
practice, which rule 9 names as the common case — and the thing it enacts is not this apparatus.

### Which parts are not inventions

Three of the five gates are the authors' own, and **they are not parked.** They survive as
individual tests.

| Piece | Whose | Parked |
|---|---|---|
| G1 Destination | Dymitruk, l. 167 | No — survives as an individual test |
| G2 State change | Dymitruk, l. 120 | No — survives as an individual test |
| G5 Interpretation | Dilger, Ch. 5, scoped by him to what crosses a boundary | No — survives, within that scope |
| G3 Charter | ours | **Yes** |
| G4 Substitution | ours | **Yes** |
| The ordered sequence | ours | **Yes** |
| The four tiers | ours | **Yes** |

G1 in particular is the corpus's one mechanical test for what belongs in a model, and it does real
work: it deleted FnEmail's `ServiceGreetingSent` outright, and it is what admitted
`ConnectionAccepted` against the taxonomy's own verdict. Parking the apparatus does not retire it.

What is parked is the claim that these tests form an **ordered sequence** with early failures
short-circuiting the rest, plus G3 and G4, which have no corpus footing at all, plus the four tiers
those two feed.

### Why it was unapplied

The reading that replaced it is **two axes rather than one ranked ladder**.

- **A lane axis** — business up, technical and infrastructure down, hideable. This is the authors'
  own device: the swimlane is the decomposition unit for both of them (see
  [`altitude.md`](altitude.md), *When to split into a separate model*), and the Vernon remark above
  is a lane axis being used for precisely the altitude job.
- **A provenance axis** — did the specification force this, or did we choose it. The tier word was
  a lossy summary of a citation that should simply be kept. Instead of *Domain*, the clause.
  Instead of *Product*, the MAY or the SHOULD. Instead of a judgment call, "no normative clause,
  stated as a bare declarative."

A one-word tier collapses both axes into a rank and then discards the citation that produced it.
Four failures in the applied scheme show the collapse happening, all of them visible in
`model-altitude.md` before it was cut.

1. **The tiers needed exceptions.** `ConnectionAccepted` is infrastructure by shape — a TCP accept
   survives no reimplementation *as a fact* — and was admitted anyway, on the strength of a single
   field with a single consumer. §3 had to coin *"infrastructure in shape, admitted by consumer"*
   to say so. A taxonomy whose entries need that phrase is conceding that G1 outranks it.

2. **Compound verdicts were two axes reported as one.** `RecipientRejected` is entered as
   **"Domain + product field + protocol field"** — one cell carrying three tiers, because the event
   is business-lane while two of its fields differ on provenance: `reason` is ours and changeable,
   and `reply_code` encodes a presentation choice and a retryability fact in the same three digits.
   The scheme had nowhere to put a second axis, so it put it in the tier cell.

3. **It went stale unnoticed.** `DataPhaseEntered`'s G4 verdict argued that a phase transition is
   an artifact of SMTP's line-oriented command/response encoding rather than of the conversation.
   The event was renamed `DataRequested` on 2026-08-09 (FnEmail-Model commit `f48ce92`) for exactly
   the reason that it does not name a phase transition — it names the client's request — and the
   G4 reasoning, whose whole argument was the phase, was left standing under the new name. The same
   file shows the failure twice: §3's `ConnectionAccepted` subheading still read *"Domain, on one
   field"* while the table above it had been corrected to **Product** three days earlier, on
   2026-08-06. A verdict nobody re-derives when the fact changes is a label, not a test.

4. **It is charter-relative all the way down, and the charter is unruled.** Every tier verdict is a
   relation to the charter, by the taxonomy's own headline claim. FnEmail's charter is itself now
   in question and its replacement is not ruled, which leaves the verdicts uncalibrated rather than
   wrong. The charter rule that justified the whole construction — *"altitude is not a property of a
   fact; it is a relation between a fact and a charter"* — stands as written and is untouched; it
   simply no longer has an apparatus to serve.

The document doubted itself from the day it was written, and the doubts were the right ones. Its
§6 recorded that G3 and G4 have **no corpus support** and that *"If they are wrong, most of §3 is
wrong. Unverified."* (Q1); that *"product"* may be no tier at all, only *"domain we chose rather
than inherited"* (Q2); and that a different charter *"would reclassify most of §3 in one stroke"*
(Q3). Three open questions against a scheme in daily use is the shape of something being applied
faster than it was justified.

### What would bring it back

The condition was always a second modeled system: `model-altitude.md`'s 2026-08-08 banner called
the gates and tiers *"flow candidates once a second modeled system tests them."* That still holds,
sharpened by what went wrong — **a second system with a different charter and a different normative
source, where the two-axes reading is tried first and found insufficient.** Specifically: a case
where assigning a lane and keeping the citation leaves a real modeling question unanswered that a
ranked tier would answer. Then G3 and G4 return as the thing that answers it, and the sequence
returns if the order turns out to matter. Absent that case, two axes are cheaper and lose nothing —
a lane is the authors' own device, and a citation carries strictly more information than the tier
word that summarized it.

A narrower revival is available for **G3 alone**, and it is the likelier one. Its content — MUST
means forced, MAY or SHOULD means chosen — *is* the provenance axis stated in one line. What is
parked about G3 is naming its two outputs *Domain* and *Product* and ranking them; the distinction
it draws survives as the axis itself.

## 10. (open)

Space for what emerges next.

---

## Method notes that are *not* extensions

Recorded here because they were initially mistaken for extensions and turned out to be orthodox —
they belong in `event-modeling-research:research/METHOD-REFERENCE.md`, not on this list:

- **Admin/operator actor swimlane** carrying verbose or technical output. Canonical: Dymitruk's
  step 3.1 permits swimlanes for *"different people (or sometimes systems)."*
- **A view need not sit next to the events it projects.** Canonical: read models are placed in the
  slice of the screen or processor they serve; sources may be arbitrarily far back.
- **Protocol verbs as triggers without wireframes.** Canonical: eventmodeling.org's cheat sheet
  admits *"the route of an http endpoint"* as a trigger.
