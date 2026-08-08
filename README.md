# EventModeling

Generic Event Modeling material as practiced and extended here: what a slice is, how steps and
paths and timelines relate, where state lives, and the conventions a model document must follow to
survive real renderers. The long-run intent is a **modeling tool** — everything in this repository
is the tool's requirements being discovered by hand, one modeled system at a time.

**This is method, not any one model.** Modeled systems live in their own repositories and appear
here only as examples. The first and currently richest example is
[FnEmail-Model](https://github.com/IvanTheGeek/FnEmail-Model) — an SMTP receiver modeled before
any code — whose walk-throughs produced most of what is written here. As more systems are modeled,
their examples extend these documents.

## Reading order

| Document | What it settles |
|:--|:--|
| [docs/layering.md](docs/layering.md) | step, slice, path, workflow, timeline — the layers and which concern lives at which |
| [docs/path-and-step-form.md](docs/path-and-step-form.md) | the concrete form of a walked path: the preamble, the step table, `Given` degrees, what a view carries |
| [docs/state-view-todo-list-decision-model.md](docs/state-view-todo-list-decision-model.md) | one fold, three consumers — and why command-validation state has no box |
| [docs/rendering.md](docs/rendering.md) | what a model document may emit and still render everywhere — measured, not assumed |

## Provenance and sourcing

The method originates with Adam Dymitruk (eventmodeling.org) and is taught at length in Martin
Dilger's *Understanding Eventsourcing*. Where these documents state the authors' positions, the
claims were verified against primary sources; quotations are brief and attributed, machine
transcripts are labeled as such, and nothing third-party is reproduced here. Where these documents
**depart** from or extend the authors' method, they say so explicitly — a departure is a decision
to record, never a silent drift.

## Status

Seeded 2026-08-08 from the FnEmail walk-throughs. The walks continue; rulings made there feed
updates here. Licensed AGPL-3.0 — see [LICENSE](LICENSE).
