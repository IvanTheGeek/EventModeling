# Working rules for agents in this repository

These rules produce bad work when broken. They are inherited from the FnEmail project, where each
was learned the hard way; the reasoning behind several lives in that repo's
[AGENTS.md](https://github.com/IvanTheGeek/FnEmail-Model/blob/main/AGENTS.md) and is not repeated
here.

## 1. US spelling. Always.

**color, modeling, behavior, organize, analyze, center, labeled, sanitize, honor, recognize.**
It applies to every word written — files, chat replies, and **commit messages**. Check both,
as a gate whose output is read before committing, not alongside it:

```bash
# files
grep -rniE '\b(colour|behaviour|honour|centre|modelling|labell|maths|whilst)\w*|\b(organis|sanitis|recognis|generalis|prioritis|summaris|apologis)(e|es|ed|ing|ation|er)\b|\b(analyse|analysed|analysing)\b' --include='*.md' .

# the commit message, before it is used
git log -n 20 --format='%B' | grep -niE '\b(colour|behaviour|honour|centre|modelling|labell|maths|whilst)\w*|\b(organis|sanitis|recognis|generalis|prioritis|summaris|apologis)(e|es|ed|ing|ation|er)\b|\b(analyse|analysed|analysing)\b'
```

The pattern is tested by running it against known positives and known negatives, never by reading
it. It must not fire on *analysis, advertise, surprise, exercise, compromise*.

## 2. Quotations keep their own words

Never restyle a quote — spelling, punctuation, capitalization, or inner quote marks. Machine
transcripts are lowercase, unpunctuated, and contain transcription errors; **copy them exactly and
label them as machine transcripts** wherever quoted. The citation apparatus depends on quotes
being checkable by exact string match.

## 3. Verify citations. Do not trust a remembered one

Every method claim traces to a primary source, read at citation time. Every RFC claim traces to
the specification text, opened and read — remembered MUSTs have turned out to be SHOULDs, and the
difference has reclassified whole conclusions.

## 4. Record corrections. Do not quietly fix them

When something turns out wrong, leave the original and add a ⚠️ block: what was claimed, why it
was wrong, what replaced it. The reasoning is worth more than a clean surface. This applies to
your own work in the same session.

## 5. Markdown renders three places, and they disagree

The full measured findings are in [docs/rendering.md](docs/rendering.md). The rules that bite:
line breaks inside table cells are `&#10;<br>` — both halves, always; color is carried by emoji
chips, never markup; no Mermaid; ASCII-only alignment in blocks; monospace marks what a protocol
fixes, standard font what varies; bold does not apply to code spans in the Claude app; angle
brackets outside code spans are eaten unless escaped. **Any change that moves text out of a code
span, and any table surgery, is re-rendered on GitHub before it is called done — not re-read.**

## 6. Commit messages carry the reasoning

Long on purpose: what changed, why, what was rejected. A correction's commit says what the old
claim was. End with a `Co-Authored-By` trailer naming **the agent and model that actually wrote
the commit** — the point is disclosure, not any fixed name:

```
Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>
```

## 7. Nothing third-party enters this repository

The method research corpus (two commercial books, a mostly unlicensed mirrored corpus, machine
transcripts of copyrighted speech) lives in a **separate private repository**. This repository
cites, quotes briefly with attribution, and links. It never reproduces. This is what keeps it
releasable under its own license, and it is not negotiable.

## 8. Walk with real data

Placeholders find nothing. `<address>` teaches nothing; 198.51.100.40 teaches. Instantiating a
value with a real instance is how orphan fields, manufactured names, and vacuous machinery get
found — several of this method's rules exist because a real value died on the page and the death
was the finding.

## 9. Departures are labeled

Where this repository extends or contradicts the method's authors, the document says so and says
why. A departure is a recorded decision; an unlabeled one is an error waiting to be cited as
canon.
