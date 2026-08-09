# The two kinds of events — capture and decision

Status: **working position, corpus-tested 2026-08-09.** Like the layering, this is held because
walking demanded it and stands ready to be revised by the walk that breaks it. Originated by Ivan
during FnEmail-Model's declaration-vs-status exploration
([`EXPLORE-declaration-vs-status.md`](https://github.com/IvanTheGeek/FnEmail-Model/blob/main/docs/paths/EXPLORE-declaration-vs-status.md),
*The two kinds of events*), where the full trail and the corpus review live.

## The taxonomy

The corpus defines an event only as a fact — *"something that happened."* True, and incomplete:
the facts divide.

| Kind | Records | Vocabulary | Examples |
|:--|:--|:--|:--|
| **capture** | an actor's act, taken faithfully, no judgment passed | *Declared, Identified, Requested, Submitted, Added* | `ReversePathDeclared`, `CartSubmitted`, money transfer *requested* |
| **decision** | the system's adjudication of a contended or validated question | *Accepted, Rejected, Assigned, Reserved, Resolved* | `RecipientAccepted`, seat *assigned*, `InventoryReservationFailed` |

A third, degenerate register — the **status** name, recording a state transition rather than an
act (*TransactionStarted, PaymentPending*) — is what good models correct away. It is not a
disease everywhere: the corpus deliberately sanctions status-shaped events at the **integration**
altitude (full-state *updated* events for cross-service publication) and at **stream management**
(*summary events* that "close the books"). The status kind is an altitude marker: wrong on a
domain timeline, legitimate where the model's subject is the machine or the stream itself.

## The pipeline, and compression

Every command-shaped interaction latently contains

```
capture --> process --> decide --> confirm
```

and a model need not draw all of it. **A step keeps exactly the pipeline-halves that have
consumers**; the rest compresses losslessly — the decision into the reply's rendering, the
capture into the decision's payload — and decompresses when a consumer arrives, which is usually
a processor. The corpus's reservation pattern is the fully decompressed case: a capture staged,
a todo-list processor, a decision event closing the todo.

The test is the consumer test, one level deeper than "does the event have a consumer":
**a decision-named event earns its place exactly where the decision-fact is consumed.** Try the
swap — replace the decision event with the capture of the same moment. Where something breaks
(a fold that must distinguish accepted from declared), the decision earns its event. Where
nothing breaks, the timeline keeps the capture and the decision lives in the view.

## What the corpus says, checked rather than assumed

Reviewed 2026-08-09 across the book, the talks, the channel transcripts, and the project's prior
distilled findings (research repo; cite-never-reproduce). The axis is **real, enacted
everywhere, named nowhere**:

- Both authors define events only as facts; Dymitruk avoids taxonomy by stated design (*"use
  regular English … not really add any specific terms"*, machine transcript). The corpus's
  explicit classifications run on three *other* axes — internal vs external, delta vs domain,
  summary events — all orthogonal to this one.
- Dilger's bank-account video demands the split without naming it: a lone *money transferred*
  event is *"too simplistic"* because it merges the request with the settlement.
- The kit's own naming corrections rewrite the status name *PaymentPending* into exactly one
  capture (*PaymentInitiated*) plus one decision (*PaymentAuthorized*) — the taxonomy operating
  in the tooling, unremarked.
- The taxonomy dissolves the corpus's recorded store-first-vs-validate-first contradiction:
  store-first is capture discipline, validate-first is decision discipline.
- Candidate third kind, noted and not yet taxonomized: **execution** events (*NotificationSent*,
  the todo-closers) — the system's own performed side effects, captures where the actor is the
  system.

## Worked example

FnEmail's walk enacts the whole of it: `ReversePathDeclared` (capture kept, decision compressed
into the `ReversePathAllowed` view), `RecipientAccepted` (decision kept — the recipients fold
must distinguish accepted from declared, so the swap breaks), and `DataRequested` (the lens's
first live ruling: the `354` is the protocol's only intermediate reply, so no decision name can
stand, and the status-register incumbent fell).

Descriptive, not doctrine — the naming tell (name and payload agree) and the consumer test
remain the enforcing rules; this page names the shape they keep producing.
