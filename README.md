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

| Document | What it holds, and how firmly |
|:--|:--|
| [docs/primer.md](docs/primer.md) | the method authors' canon in brief — timeline, the three moving pieces, the four patterns, slices, Given-When-Then. **Verified quotations; the summary is the author's reading** |
| [docs/layering.md](docs/layering.md) | step, slice, path, workflow, timeline — the layers and which concern lives at which. **Working hypothesis** |
| [docs/path-and-step-form.md](docs/path-and-step-form.md) | the concrete form of a walked path: the preamble, the step table, `Given` degrees, what a view carries. **Current practice, resting on the layering hypotheses** |
| [docs/state-view-todo-list-decision-model.md](docs/state-view-todo-list-decision-model.md) | one fold, three consumers — and why command-validation state has no box. **Verified quotations; the synthesis is the author's reading** |
| [docs/rendering.md](docs/rendering.md) | what a model document may emit and still render everywhere. **Measured**, not assumed |
| [docs/altitude.md](docs/altitude.md) | role collapse, when a model splits in two, and one wire instant appearing correctly in two models. **Working hypothesis**; the quotations are verified |
| [docs/extensions.md](docs/extensions.md) | proposed extensions to the method — **parked, deliberately applied to no model**, so the orthodox form stays measurable against them |

## How firmly things are held

Three kinds of claim live in these documents, and they carry different weight:

- **Measured** — the rendering rules were run against real renderers, with controls, and hold
  until a renderer changes.
- **Verified quotation** — every corpus quote was checked verbatim against a primary source at
  citation time.
- **Working hypothesis** — everything else, above all the layering: how steps, slices, paths,
  workflows, and timelines relate. These are the author's current thinking, developed by walking
  real systems and still being tested by the walks. Hypothesis documents say so at the top;
  nothing in them is settled, here or in the method authors' canon.

## Provenance and sourcing

The method originates with Adam Dymitruk (eventmodeling.org) and is taught at length in Martin
Dilger's *Understanding Eventsourcing*. Where these documents state the authors' positions, the
claims were verified against primary sources; quotations are brief and attributed, machine
transcripts are labeled as such, and nothing third-party is reproduced here. Where these documents
**depart** from or extend the authors' method, they say so explicitly — a departure is a decision
to record, never a silent drift.

## Status

Seeded 2026-08-08 from the FnEmail walk-throughs. The walks continue; what they teach feeds
updates here — extending the hypotheses where they hold, revising them where they break.

## License

**Code is AGPL-3.0, documents are CC BY-SA 4.0** — the family ruling, shared with FnEmail-Model,
which registers it in its
[`docs/DECISIONS.md`](https://github.com/IvanTheGeek/FnEmail-Model/blob/main/docs/DECISIONS.md)
and whose ruling commit of the same title carries the fuller reasoning. This is a repository of
documents — the modeling tool it works toward will live in repositories of its own — so:

- **The documents — everything this repository currently holds** — are licensed
  [CC BY-SA 4.0](LICENSE).
- **Code, if this repository ever comes to hold any**, is licensed [AGPL-3.0](LICENSE-code) —
  the license the modeling tool itself will take.
- **Not relicensed:** third-party material included here retains its original license and is not
  covered by either grant; brief quotations from the method corpus remain their authors' —
  [AGENTS.md](AGENTS.md) rules 2 and 7. Beyond brief quotation, third-party material enters only
  when its own terms permit redistribution, always attributed, its terms named where it sits.
