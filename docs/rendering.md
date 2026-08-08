# Rendering — what a model document may emit and still render everywhere

A model document is read in at least three renderers — GitHub, the Claude Android app, the Claude
desktop app — and they disagree. Every rule below was **measured**, not assumed: the experiments
live in
[FnEmail-Model/docs/diagrams](https://github.com/IvanTheGeek/FnEmail-Model/tree/main/docs/diagrams),
run against all three renderers with controls. For the future tool these are output requirements:
a generator must emit only what renders everywhere, and where no token works everywhere, the
cheapest failure is chosen deliberately and documented.

## Line breaks inside table cells: `&#10;<br>` — both halves, always

GitHub uses the `<br>` and collapses the entity; the Android app strips the `<br>` and uses the
entity. A lone `<br>` fails **silently and only on a phone**, fusing two words. The known,
accepted cost: the desktop app honors both halves and renders an extra gap. Do not "fix" the gap
by dropping a half — each half is load-bearing in a different renderer. One-row-per-line is the
only technique that cannot fail and is rejected on row-count cost, recorded with the evidence so
it can be adopted if the cost ever stops mattering.

## Color is emoji chips, never markup

Every text-coloring mechanism tested fails or diverges across renderers. The working legend:

🟧 Event · 🟦 Command · 🟩 Read Model · ⬜ rendered UI · ⬛ wire · 🟨 external / required-first ·
🟥 hotspot · 🟤 nothing (and only ever in a `Given`)

## No Mermaid; diagrams are markdown tables

The Android app has no diagram engine. Tables render everywhere.

## Alignment blocks are printable ASCII only

`| - + / \ ^ _` only. Box-drawing characters (U+2500–U+257F) are missing from most phone
monospace fonts, fall back at a different advance width, and collapse the alignment — a font
problem no markup can fix. Ellipsis, em dash and middot are fine in prose, not in aligned blocks.

## One axis of emphasis: font family

Monospace marks what the protocol or domain **fixes**; standard font marks what **varies**.
`250` OK — code monospace, operator text standard. `field_name`: value — name monospace, value
standard, **no quotation marks on values**. Never bold or italic as a second axis. And **bold
does not apply to code spans in the Claude app** — ``**`code`**`` silently loses its weight
there, so monospace text is never bolded; italic on a code span does work.

## Tables: structure is what markdown punishes

Every table needs its header row and `|:--|` separators; a table continues until the first
non-table line, so **every table header must be preceded by a blank line** — removing a row
between tables can fuse them, rendering the next header as data and its separator as literal
`:--` cells. No raw HTML tables: one Claude renderer strips the tags and fuses the contents, the
other prints the tags as text. Cells already top-align in both Claude renderers — do not pad, and
do not solve alignment problems that were never observed (that one was tested only after a fix
for it had produced two false verdicts).

## Angle brackets outside a code span are eaten

`<CRLF>` in running text vanishes; `<Smith@bar.com>` becomes a mailto link with the brackets
stripped — and in some domains the brackets are syntax that changes meaning. Escape the opening
bracket (`\<`) or use a code span; only a code span also suppresses the autolink. To show a
literal backtick inside a code span, use double backticks as delimiters — backslash escapes do
not work inside code spans.

## The two process rules that make the rest hold

**Test both directions.** A check that catches nothing looks exactly like a check that is
working; every pattern is validated against known positives and known negatives before it is
trusted.

**Re-render, never just re-read.** Any change that moves text out of a code span, and any table
surgery — adding, removing, or reordering rows — is verified in the actual renderers before it is
called done. Both rules exist because their absence produced real, silent breakage.
