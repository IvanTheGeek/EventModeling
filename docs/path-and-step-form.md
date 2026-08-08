# The path and step form

The concrete shape of a walked path document, as it currently stands after the FnEmail
explorations. Each rule here was tried, broken, or corrected at least once before it held — but
that is evidence, not settlement: the form rests on the layering hypotheses
([layering.md](layering.md)), which are the author's working position, and if the layering moves,
the form moves with it. The reasoning records live in the
[FnEmail-Model paths directory](https://github.com/IvanTheGeek/FnEmail-Model/tree/main/docs/paths),
and the current worked instance is the
[direct-path walk](https://github.com/IvanTheGeek/FnEmail-Model/blob/main/docs/paths/WORKING-helo-direct-single-recipient-v2.md).

## A path opens with the events it requires

The **Required first** block is the path-level `Given`: every event the walk needs but does not
walk, each with the fields some step consumes. It is the only place 🟨 appears in a path, and 🟨
makes exactly one claim there — *needed, not walked here*. The path is deliberately indifferent
to provenance: whose event it is, what kind, where the value came from all carry no weight;
placeholder names are fine and expected. Once declared, an event is in the path's event store,
and steps cite it 🟧 exactly like a walked event.

The rule that governs the split:

> **Walk what is inside the path's timeline; declare, with fields, what is before it.**

A whole-session walk therefore walks its own opening fact; a narrower walk declares it. Facts
from a longer timescale than the path's own — configuration, provisioning — are always declared,
because no path-scoped walk can ever contain them. (The corpus supports the runtime half of this:
production systems ship with *"assumed events that are there at the beginning"* — Dymitruk,
podcast, machine transcript.)

## A step is one table, no heading

| Row | Carries |
|:--|:--|
| header | chip, `C`/`V`, step number in this walk, slice name |
| top row | the actor and what crossed to it — **absent** when nothing does |
| *(blank label)* | the command or view itself — the slice, already named in the header |
| Event | the event emitted (command steps only) |
| Given | the label alone, standing as an in-table sub-header — see below |
| *(blank label)* | the minimal dependency — see degrees below |

Two absences are meaningful. A **consulted** view — read internally, rendered to nobody — has no
top row; its readers declare it in their own `Given` rows. A step whose actor is **outside the
model** (infrastructure, a transport) has no top row either: the fields it lifts across the
boundary are admitted because something inside consumes them, and nothing else about the outside
is modeled.

**The command row carries the fields the command takes from its actor.** A bare command name
breaks the completeness check: every field needs an origin, and for everything the actor supplies
the command *is* that origin. Where the command row and the Event row look nearly identical, that
is the check **passing** — a value arriving and being stored unchanged. Where they diverge,
something was derived, generated or dropped, and the divergence is visible at a glance.

## The Given

**The `Given` is a labeled block, not a row.** The label stands alone with an empty right cell,
and the events follow in the row beneath it under an empty left label:

| 🟦 C · Step 3 | `Helo` |
|:--|:--|
| MTA Client | ⬛ `HELO` bar.com |
| | 🟦 **Helo**&#10;<br>&nbsp;&nbsp;`claimed_domain`: bar.com |
| Event | 🟧 **ClientIdentified**&#10;<br>&nbsp;&nbsp;`claimed_domain`: bar.com |
| Given | |
| | 🟧 **ConnectionAccepted** |

The label row reads as a sub-header and a separator, which is what the `Given` is: the rows above
it are *observations* of what the walk did, and the block below it is the step's one *claim*. It
is also the shape the **Required first** block already uses, so the path-level `Given` and the
step-level one are visibly one device at two scales rather than two conventions that happen to
share a word. However many events a `Given` cites, they still stack inside the single cell below
the label, in the Event row's own layout — the block is two rows, never one row per event.

Dependencies point backward, only, and are declared by the step that needs them — there is no
*Consumed by*, at step or at path level, because a forward list is derived data written by hand
and goes stale silently. Three degrees:

| Degree | Means | Written as |
|:--|:--|:--|
| 🟤 | depends on no previous event | the chip alone, and only in a `Given` |
| existence | the event must have happened | 🟧 name only |
| data | specific values are used | 🟧 name, then `field`: value lines |

The data degree appears **exactly where a value is used** — folded into state or rendered —
never as decoration. An unsatisfiable `Given` is a finding: it means the model is missing a data
collection point somewhere in the past, and working backward from the need is how the hole gets
located.

**It is the minimal dependency, not the accumulated history — a deliberate departure.** Dymitruk's
convention is that a given is every previous row, so a test runner *"can always just use an
accumulator to add events"*. That convention exists for a runner, which has no walk to read. Here
the walk is on the page, so restating it would be redundant: a step with three events above it
declares the one it actually needs. Recorded rather than left implicit, because a reader who knows
the method will notice the difference and should find it accounted for.

## A view is the dataset provided to the actor

A view is the data an actor needs to complete its task — the way a GUI takes a dataset and
produces the finished page. The rendered output row is what the **renderer** produces from the
view's fields, and the split is strict:

> **The renderer owns the constants; the view owns the facts.**

Every varying value on the output must be a field of the view. A value that never varies —
fixed reply text, a code chosen per scenario — belongs to the renderer and carries no field.
Existence-restating booleans that nothing renders are not fields; the choice among renderings
(success code versus refusal code) is scenario selection, which is a slice-level concern
contributed by whichever walk actually takes the other branch.

## Completeness closes in three checks

The backward check stops at rendered outputs; the forward check counts consumers. Neither asks
where an **emitted** event's payload values come from, and under the first two alone a walk
carried values materializing from nowhere — the gap was found twice in one day, by a document
review and by a refactor that orphaned a field whose real consumer turned out to be an
undeclared fold.

| Check | Every… | …traces to |
|:--|:--|:--|
| backward | varying output value | the view field carrying it, and that field's event of origin |
| payload | emitted event payload value | a command field, a `Given` fold, a derivation from either, a mint at the step, or a named boundary fact |
| forward | event | at least one consumer |

A payload value with none of those origins is a finding — usually a missing `Given`, sometimes a
missing data collection point further back. Seeded events are declared rather than emitted; the
Required first block already rules their provenance out of scope.

## Naming discipline

Refer to slices by **name**, never by number — numbers renumber silently. Step numbers within one
walk are positional facts and are fine. Field names are audited for provenance: a name the
domain's own documents use is kept; a manufactured name survives only while the walk shows its
value a real origin and destination; a value constant in every possible walk is a rule, not a
fact, and its field is deleted with the reasoning recorded.

## What a path document contains besides steps

Scene · the dialogue or story being walked · Required first · the walk · accounting ·
completeness instantiated in **three checks** (every output value to its origin; every emitted
payload to its origin; every event to its consumers) · what this walk tested · what it did not test · hotspots, listing only what is
open or new. Resolved doubts are absent, not struck through; corrections are recorded ⚠️-style
where they happened.
