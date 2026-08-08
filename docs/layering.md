# The layering — step, slice, path, workflow, and where each concern lives

> ⚠️ **Status: working hypothesis, not settled.** This document originally opened by calling the
> layering "Ruled 2026-08-08". That overstated it: the relations below — what a step, a slice, a
> path, and a workflow are, and how they contain one another — are the author's current thinking,
> drafted during the FnEmail walk-throughs and still being tested by walks. They are not the
> method authors' canon, and they are not final even here; further walks are expected to revise
> them, and revisions are recorded, not silently applied. The drafting date stands.

Drafted 2026-08-08 during the FnEmail walk-throughs. The hypothesis inverts the obvious reading
of a model repository: the polished model is not the source that walks instantiate — **the walks
are the source, and the model is derived from them.**

The method's own vocabulary — the canon slice, the timeline, the four patterns, Given-When-Then —
is summarized in [the primer](primer.md); this document assumes it. The slice and workflow defined
below are this document's layered senses of those terms, built on the canon ones.

## The elements

**A step is an instance of a slice, carrying example data.** One concrete traversal of one slice,
with the actual bytes and the actual field values of one occasion. The example a document displays
is one *selected* for display — the true slice composes **many**: every walk that traverses a
slice contributes its scenarios, and a walk that revisits a slice extends the composed slice's
Given-When-Then set rather than repeating it.

**A slice is the composition of every step that traversed it**, across every walked path, for
which real data exists. A slice's specification set is therefore a *result*, not an input — it
cannot be complete until enough paths have been walked, and an untouched slice has no evidence
behind it at all.

**A path is an instance of a composed timeline part, with specific data: a specific timeline.**
It is one conversation, one story, walked in time order with real values.

**A workflow is the composed, generic part of the model** — the same timeline part a path
instantiates, with the data left general. A path of a workflow is that workflow with the data
filled in.

## Three layers, three concerns

| Layer | Speaks | Owns |
|:--|:--|:--|
| **path** | the domain with real values | positional order — the timeline itself is the scoping |
| **slice** | the composed scenarios across walks | selection among many instances; query keys as contracts |
| **store** | physical organization | streams, envelopes, correlation apparatus |

The load-bearing consequence: **a path carries only values the domain itself moves.** Identity
and correlation machinery — session keys, correlation ids, stream names — cannot appear in a
path, because a key exists to select among interleaved instances, interleaving cannot occur on a
single timeline, and the page is already the selection. Scoping on a path is **positional**:
whatever lies between the opening fact and the closing fact of a container is inside it.

The worked demonstration is FnEmail's direct path, where a fully instantiated session ULID and
nine `Where` rows were removed once instantiation proved them slice-level apparatus — the removal
recorded as the finding
([the walk](https://github.com/IvanTheGeek/FnEmail-Model/blob/main/docs/paths/WORKING-helo-direct-single-recipient-v2.md),
*The layering* block). Every claim in this document has its long-form reasoning in that
repository's exploration records.

## The completeness check runs at path level, in three checks

Backward: every varying value an output renders traces to an origin on the page. Payload: every
value an emitted event carries traces to a command field, a `Given` fold, a derivation from
either, a mint at the step, or a named boundary fact. Forward: every event has at least one
consumer, and every unconsumed field is either deleted or flagged as an open question — never
left silent. A value that is constant in every walk there will ever be is a
**rule, not a fact**, and carries no field. A name no protocol or domain document uses is
**manufactured**, and survives only if the walk shows a real origin and a real destination for
its value.

## Departures registered

Two positions above depart from or go beyond the method's authors, deliberately. Like the rest of
this document they are working positions — held because walking demanded them, revisable the
moment a walk breaks them:

1. **Many examples compose the slice** — the authors' models typically display one example
   data set; here the displayed set is a selection and the union is the truth.
2. **Keys are not path concerns** — the authors do not address per-document key scoping;
   the positional-scoping rule is this practice's own, forced by walking a protocol whose
   specification names no identifiers at all.
