# Working rules for agents in this repository

These rules produce bad work when broken. Rules 1–9 are inherited from the FnEmail project, where
each was learned the hard way; the reasoning behind several lives in that repo's
[AGENTS.md](https://github.com/IvanTheGeek/FnEmail-Model/blob/main/AGENTS.md) and is not repeated
here. Rules 10–11 originated here — first as a labeled departure, since adopted by FnEmail-Model
as its rules 14–15 — so the convention now governs the family: normative documents carry the
present, git history carries the why, and exploration material keeps its in-document trail.

## 1. US spelling. Always.

**color, modeling, behavior, organize, analyze, center, labeled, sanitize, honor, recognize.**
It applies to every word written — files, chat replies, and **commit messages**. Check both,
as a gate whose output is read before committing, not alongside it:

```bash
# files
grep -rniE '\b(re)?(colour|behaviour|honour|centre|modelling|labell|maths|whilst|artefact)\w*|\b(organis|sanitis|recognis|generalis|prioritis|summaris|apologis|authoris|standardis)(e|es|ed|ing|ation|er)\b|\b(analyse|analysed|analysing)\b' --include='*.md' .

# the commit message, before it is used
git log -n 20 --format='%B' | grep -niE '\b(re)?(colour|behaviour|honour|centre|modelling|labell|maths|whilst|artefact)\w*|\b(organis|sanitis|recognis|generalis|prioritis|summaris|apologis|authoris|standardis)(e|es|ed|ing|ation|er)\b|\b(analyse|analysed|analysing)\b'
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

Where the record lives depends on the document class (rule 10 names the classes). Exploration
and parked material ([docs/extensions.md](docs/extensions.md) carries several ⚠️ blocks) keeps
the block in place — there the trail *is* the content, and that holds for superseded rulings
too. A normative document records the correction in the commit that fixes it (rule 6) and keeps
a block in-document only while it changes how the current text must be read — a status that
reframes the whole document qualifies; a superseded position does not. And in a normative
document, a **ruling that changes is not a correction**: the old position was the accepted one
until it was superseded, and it goes to history (rule 10), not to a ⚠️ block.

## 5. Markdown renders three places, and they disagree

The full measured findings are in [docs/rendering.md](docs/rendering.md). The rules that bite:
line breaks inside table cells are `&#10;<br>` — both halves, always; color is carried by emoji
chips, never markup; no Mermaid; ASCII-only alignment in blocks; monospace marks what a protocol
fixes, standard font what varies; bold does not apply to code spans in the Claude app; angle
brackets outside code spans are eaten unless escaped. **Any change that moves text out of a code
span, and any table surgery, is re-rendered on GitHub before it is called done — not re-read.**

## 6. Commit messages carry the reasoning

Long on purpose: what changed, why, what was rejected. A correction's commit says what the old
claim was, and a commit that establishes or supersedes a ruling follows the ruling-record form in
rule 10. End with a `Co-Authored-By` trailer naming **the agent and model that actually wrote
the commit** — the point is disclosure, not any fixed name:

```
Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>
```

## 7. Third-party material enters only on terms that allow it

Three constraints, not a blanket ban: never republish material that copyright or licensing does
not permit; never use material without attribution; and for code, dependencies must be
license-compatible with AGPL-3.0. The method research corpus (two commercial books, a mostly
unlicensed mirrored corpus, machine transcripts of copyrighted speech) lives in a **separate
private repository** because almost none of it may be republished — for corpus material the
practice is unchanged and not negotiable: cite, quote briefly with attribution, link, never
reproduce. That is what keeps this repository releasable. Third-party material whose own terms
allow redistribution may enter; it keeps its original license, always attributed, its terms
named where it sits, carved out of the repository's own grant (README → *License*).

## 8. Walk with real data

Placeholders find nothing. `<address>` teaches nothing; 198.51.100.40 teaches. Instantiating a
value with a real instance is how orphan fields, manufactured names, and vacuous machinery get
found — several of this method's rules exist because a real value died on the page and the death
was the finding.

## 9. Departures are labeled

Where this repository extends or contradicts the method's authors, the document says so and says
why. A departure is a recorded decision; an unlabeled one is an error waiting to be cited as
canon.

## 10. Documents carry the present. Git history carries the why

A normative document says only what is currently accepted. **Normative means the reader applies
it, whatever its confidence label**: the method documents in docs/ — the working-hypothesis ones
included — plus this file and the README. Exploration material is what is read for its trail:
[docs/extensions.md](docs/extensions.md) (parked) and the walk records in the model
repositories; rule 4 governs those, not this rule. In a normative document, superseded
positions, rejected alternatives, and the road here live in the commits that changed it, never
as a narrative accumulating inside it — the one exception is rule 4's: a ⚠️ block stays while it
changes how the current text must be read. The division of labor: the current file answers *what
is accepted now*; the commit that changed it answers *why this became accepted*; earlier commits
answer *what was accepted before, and why*; a superseding commit answers *why the old ruling
fell*. Keep in the document only the rationale a reader needs to apply the rule correctly —
history is never an excuse for an ambiguous or incomplete current state. Existing documents come
under this rule as they are next materially changed, not by a sweep.

**Read the history before changing a ruling.** Before materially changing a normative document,
inspect what stood and why:

```bash
git log --follow -p -- <path>          # the sequence of changes and their reasoning
git blame <path>                       # which commit last touched the lines in question
git show <commit>                      # that commit's full message and diff
git log -S'<exact text>' -p -- <path>  # commits that added or removed specific wording
git log -G'<pattern>' -p -- <path>     # the same, by regex
git log -F --grep='<title or path>'    # rulings from commits that never touched the file
```

`git blame` names only the **latest** commit behind each surviving line — a starting point, not
the decision history; `git log --follow -p` gives the sequence. Wording that was deleted or
replaced is invisible to blame and to the current file; `-S`/`-G` find it. A ruling whose commit
never touched the file is invisible to every file-scoped command — the unscoped `--grep` and the
`docs/DECISIONS.md` register (when it exists) are how those are found. A rule stated without
rationale does **not** mean no ruling exists — under this convention that is the normal case.
Before reversing or materially altering a ruling, find and read the commit that established it:
rule 3's discipline, applied to our own history. The trail can cross repositories — the seeded
documents' pre-move drafting history and several establishing rulings live in FnEmail-Model's
git and its `docs/DECISIONS.md`, and `--follow` here stops at the seed commit.

**A ruling-bearing commit is focused and self-describing.** One ruling per commit, unmixed with
cleanup, mass formatting, or unrelated work — a tangled diff makes both the change and the
reasoning unretrievable. The message opens by naming the ruling with a short title used
**verbatim** wherever the ruling is cited, so `git log -F --grep='<title>'` retrieves it (`-F`
matches the title as text — bare `--grep` is regex, and a title's punctuation would break it).
A cited title never wraps across lines: `--grep` matches within a line, and a wrapped citation
is unfindable — found the hard way on this form's first use. This repository family
identifies rulings by descriptive title, never by serial number
(FnEmail-Model's `docs/DECISIONS.md` register set the precedent). Beyond rule 6's baseline,
cover whichever of these actually carry information: the context that forced the decision; the
rationale; the alternatives materially considered and why each was rejected; the costs accepted;
an `Applies-To:` trailer listing affected paths when the ruling governs more than the commit
touches; a `Supersedes:` trailer naming the replaced ruling's title (and commit, when known).
Typos, formatting, and mechanical changes need none of this — rule 6 alone covers them.

**Supersede; never rewrite.** A changed ruling is a new commit that names what it replaces. The
old commit is the archive: never amended, rebased away, or reworded to make history look
inevitable. And `git log --follow` on one file cannot surface a ruling from a commit that never
touched that file — when a ruling's scope exceeds its diff, the `Applies-To:` trailer records
the scope, and a cross-cutting, long-lived, or externally cited ruling additionally earns a row
in `docs/DECISIONS.md`, created on first need in the named-table form of FnEmail-Model's
register, so it is discoverable without knowing which commit to read. The register is a
discovery index, not a document under this rule's discipline: its rows — the *Superseded*
table's included — are pointers to commits and documents, and retrieval is their content.

## 11. History is preserved by how branches land

Work here commits directly to `main`, one focused commit at a time — that already preserves the
trail. When work does go through a branch, it lands with an explicit merge commit:

```bash
git merge --no-ff <branch>
```

`--no-ff` keeps the branch boundary — the merge commit records that those commits were one
reviewed unit of work, where a fast-forward loses that boundary while keeping the commits. But
`--no-ff` alone is not preservation: a **squash merge replaces the branch's commits with one
combined commit** on the target, destroying ruling-bearing messages outright, and a rebase-merge
or force-push rewrites them. So: never squash-merge a branch whose commits carry rulings; never
rebase accepted work; never force-push rewritten history to `main`. A wrong accepted ruling is
replaced by a superseding commit (rule 10), not by rewriting the commit that made it. If a
platform ever forces a squash, the squash commit's message must carry, complete, every ruling
record that would otherwise be lost — but the standing preference is `--no-ff` and the
individual commits, kept.
