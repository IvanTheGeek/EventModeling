# An Event Modeling primer — the canon the rest of this repository assumes

> **Status: the authors' method, summarized.** Everything below states the method as its authors
> published it, verified against primary sources at citation time; quotations are exact and
> attributed. The selection and phrasing are this repository's. Nothing here is a position of this
> repository — its own layer vocabulary (step, path, and its layered senses of slice and workflow)
> is defined in [layering.md](layering.md), and where this practice departs from the canon below,
> the departure is registered there, never here.

The other documents use the method's vocabulary — slice, timeline, the four patterns,
Given-When-Then — without defining it. This document is the floor under them: enough canon, with
sources, that the rest can be read without the corpus at hand.

## The method and where it comes from

Event Modeling describes an information system as a timeline of concrete examples. Its author is
Adam Dymitruk; eventmodeling.org credits the method's development to him at Adaptech Group, and
the defining article — *Event Modeling: What is it?* — describes it as a way to *"design a
blueprint for an Information System of any size or scale"*. The article dates to 2019 on the site
and began as a 2018 Medium article (Dymitruk's account on Software Engineering Radio episode 539;
machine transcript). It is marked as periodically updated; claims here were verified 2026-08-08
against the site's public source repository.

Martin Dilger's book *Understanding Eventsourcing* teaches the method at length and is the other
primary source these documents cite. Despite that title, the method does not presuppose an
event-sourced system: the book's planning chapter is explicit that an event is a fact however it
is persisted — a row saved in a table counts.

## The timeline

*"Event Modeling works on a single timeline"* (the book's planning chapter): events are laid left
to right in chronological order and read as a story. Conditions and loops are not modeled — *"The
short answer: we don’t. Instead, pick one flow and model it."* (the structuring chapter) — the
usual practice being the good case first, error cases as separate linked flows or as scenarios.
Swim-lanes divide the board into lanes for the people and systems involved (the article); the
book's stream-design chapter later gives them a second job: *"Swimlanes define stream
boundaries."* Wireframes or mockups run across the top, and their stated goal is sketching how
data is captured and kept flowing consistently — explicitly not UX or screen design.

Modeling starts by brainstorming events, and the brainstorming chapter's one rule is *"The sticky
notes have to be formulated in the past tense."* — an event is a fact, something that has already
happened.

## The three moving pieces

*"Event Modeling only uses 3 moving pieces and 4 patterns based on 2 ideas"* (the article). The
pieces:

- 🟧 **Event** — a fact of the system, in the past tense, sitting on the timeline. Orange.
- 🟦 **Command** — an intention to change the system. Blue.
- 🟩 **View** (or **Read Model**) — what the system shows: it *"represents a query against the
  already stored events in the system"* (the planning chapter). Green.

Wireframes sit outside the count, and the palette is the book's; the article's prose itself names
only the blue command box and a green to-do list. This repository's documents carry the same
colors as emoji chips ([rendering.md](rendering.md)).

## The four patterns

The pattern names below are the book's — *"the four main patterns in Event Modeling"*. The
article names only Translation and Automation, leaving the others as *"the first 2 patterns of
the 4 that are needed"*:

| Pattern | Shape on the board | What it does |
|:--|:--|:--|
| **State Change** | 🟦 command → 🟧 event | changes the system — the book calls it *"the only way to trigger change in a system"* |
| **State View** | 🟧 events → 🟩 view | shows the system — events fold into a read model that a screen or process queries |
| **Automation** | 🟩 view → gear → 🟦 command | a background process — a State View plus a State Change joined by a gear symbol |
| **Translation** | external data → 🟧 event or 🟩 view | brings the outside in — *"The external event represents data that comes from an external system."* |

An automation's trigger, per the book, is an event, a timer, or a user interaction; its
todo-list-and-processor reading — the form this repository leans on — is Dymitruk's, joined by
the book's later pattern chapter, and is treated in full in
[state-view-todo-list-decision-model.md](state-view-todo-list-decision-model.md). Translation is
the one pattern the book allows modeling two ways: as a read model translating under the cover,
or as a state change storing a new internal event (Dilger prefers the latter).

The patterns close a loop, and the loop is the law of the board: command to event, event to view,
view to UI or processor, UI or processor to command — *"Connecting an event directly to a command
is not allowed."* (The quoted rule is in the article's source repository; the deployed page was
mid-revision when checked, 2026-08-08.)

## Slices

The model divides into vertical slices: *"Every write operation, every read operation will be a
dedicated slice."* (the vertical-slicing chapter). One slice is one State Change, one State View,
or one automation — not a command-and-view bundle — and it carries all of its technical layers,
persistence through UI. The organizing principle is one the book quotes from Jimmy Bogard:
*"Minimize coupling between slices, and maximize coupling within a slice."* Slices can be
implemented mostly in isolation and in any order, because the events between them are the
contracts; keeping a slice to about a day's work is Dilger's stated personal goal, not a method
rule.

## Given / When / Then

A slice's behavior is specified by scenarios placed directly below it — the model's business
rules live there, not in tickets. For a State Change, the Given is *"A set of events that brings
the system into a specific state."* and may be omitted; *"“When” always defines a command."*; the
Then is the resulting event or events, or an expected error. For read models — and automation
tests — *"you typically do not use GWTs but GTs (Given - Then)"* (the planning chapter), since a
view relies only on already-stored events. At implementation time the scenarios translate into
unit tests.

The book attributes the Given/When/Then form to behavior-driven development; the phrase
*specification by example* appears in Dymitruk's foreword to it. Example data is framed as an
extension of a scenario, not its baseline — *"The key is to provide clear, concrete examples"*.
Where this repository stands on example data — the walks, the many-examples position — is
[layering.md](layering.md), and the concrete form a walked path takes is
[path-and-step-form.md](path-and-step-form.md).

## The seven steps, and the check that follows

The article's workshop format: *"Event Modeling is done in 7 steps."* Its step titles, verbatim:
Brain Storming; The Plot; The Story Board; Identify Inputs; Identify Outputs; Apply Conway's Law;
Elaborate Scenarios. After the steps comes the Completeness Check: *"All information has to have
an origin and a destination."* — every field on the model traceable from where it entered to
where it is used, with skipping the check named as a variation that absorbs rework cost instead.
That check is the seed of the completeness discipline these documents run at path level
([layering.md](layering.md)).

## Sources

Adam Dymitruk, [Event Modeling: What is it?](https://eventmodeling.org/posts/what-is-event-modeling/)
(eventmodeling.org, 2019; marked periodically updated) and the
[eventmodeling.org](https://eventmodeling.org/) homepage. Quotations were verified 2026-08-08
against the site's public source repository; one quoted rule (event directly to command) was in
the source but not on the deployed page that day, noted where quoted.

Martin Dilger, *Understanding Eventsourcing* — cited above by chapter: "Planning Systems using
Event Modeling", "Brainstorming", "Modeling Use Cases with Wireframes", the "Given / When / Then"
Scenarios chapter, "Vertical Slicing", "Structuring an Event Model", "Event Streaming, Event
Sourcing and Stream Design", and Adam Dymitruk's foreword.

Software Engineering Radio, episode 539, *Adam Dymitruk on Event Modeling* — machine transcript,
used only for the article's 2018 origin; nothing from it is quoted here.

Quotations are brief and remain their authors' — the README's License section and
[AGENTS.md](../AGENTS.md) rules 2 and 7 govern.
