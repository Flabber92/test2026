# Prototype spec: The Waiting Room

Single self-contained file, `prototype/index.html` (inline CSS + JS,
zero dependencies, zero build step, opens directly in a browser).
This is the "one room" test object: small enough to read in one
sitting, deep enough to prove every core mechanic works.

## Scene content (authored, fixed)

A room: a table, a window, a locked door, a wall inscription, a dead
moth on the sill. One recurring figure referred to only as "the
physician." One central unresolved question: who was last in the room
before the player.

Write 3-4 short authored paragraphs total (room description +
physician description + inscription text + one memory fragment).
Tone: sparse, calm, *Kentucky Route Zero* / *Stygian* register. Not
horror-movie purple prose.

## State variables (RECORD, resets on New Session)

- `depth` : int, 0-3
- `coherence` : int, 0-100 (starts 100, drops as contradictions pile up)
- `trust_physician` : bool | null (starts null/unresolved)
- `window_examined` : bool
- `moth_examined` : bool
- `inscription_read` : bool
- `last_in_room` : "physician" | "visitor" | "unresolved" (starts
  "unresolved" — this is the delayed-collapse variable)

## IMPRESSION (persists across New Session via localStorage, never resets)

- `sessions_count` : int
- `first_seen_at` : timestamp
- `has_seen_death_reading` : bool — set true if in ANY session
  `trust_physician` ever resolved false. Once true, forever colors
  how the physician is described, even in a fresh RECORD where trust
  is unresolved again.
- `last_active_at` : timestamp — used for Epoch Drift text (see below)

## Epistemic status indicator

Each of the 3-4 authored paragraphs has zero or more sentences tagged
with a status: OBSERVED / REPORTED / INFERRED / UNRESOLVED /
RETROACTIVE. Render a small glyph before RETROACTIVE sentences only
once `last_in_room` has collapsed away from "unresolved" — this is
the core "same words, new meaning" proof. Do not rewrite the sentence
text, ever. Only the glyph and its tooltip (from the legend) change.

## The glyph header (the spread)

Render a fixed-width header line with 3 positions:
`ORIGIN · OBSTACLE · OUTCOME`, each rendering a 1-2 char glyph derived
deterministically from state (e.g. Origin reflects `depth`, Obstacle
reflects `coherence` bucketed into 4 bands, Outcome reflects
`last_in_room`). Provide the mapping table in a code comment so it's
auditable, but never explain it in the UI itself.

## Input: the drum constraint

A single text input, hard `maxlength=80`, two visual rows (wrap at
40). Placeholder text should look like a fill-in-the-blank exhibit
line, not a chat box. On submit, run a tiny deterministic parser (see
below) and print exactly one verdict from the fixed vocabulary:
STIPULATED / DISPUTED / SUSTAINED / OVERRULED / REDACTED /
"CANNOT BE ESTABLISHED" (this last one is the parser's own
out-of-vocabulary response, distinct from the epistemic verdicts —
label it clearly as such). Never use SEALED in this prototype; it is
reserved and must not appear in shippable content yet.

## Deterministic parser (no LLM)

Hand-write a small synonym dictionary (~15-20 entries covering
physician/doctor/surgeon, door/entrance/gate, saw/observed/witnessed,
lied/concealed/deceived, moth/insect, window/glass) and a handful of
canonical proposition templates the room actually supports, e.g.:

- "the physician entered/left through the door" -> checks against
  `last_in_room`
- "the physician concealed/lied about X" -> checks `trust_physician`
- anything mentioning an entity/relation not in the dictionary ->
  "CANNOT BE ESTABLISHED"

Keep this genuinely small and readable; it is a demonstration of the
mechanism, not a production NLU system. Comment the mapping table
inline so a future session can extend it.

## Theory mechanic

A separate, clearly labeled action ("state a theory," not a chat
message) that checks a longer proposition against 2-3 authored
"theory templates" for `last_in_room`. Correct-enough theory ->
SUSTAINED, plus it *collapses* `last_in_room` from "unresolved" to a
concrete value (delayed choice collapse) and immediately re-renders
the earlier paragraphs so the player sees the RETROACTIVE glyphs
light up on text they already read. This is the single most important
moment in the prototype; get this right before polishing anything else.

## Anti-datamining tiebreaker

When the theory mechanic collapses `last_in_room` and authored logic
alone doesn't fully determine which of 2 remaining candidates wins,
break the tie with a hash of (session start timestamp, count and
timing deltas of the player's prior submit actions) — not
`Math.random()`. Implement as a small pure function so it's testable
and auditable; comment that this is intentional (see AGENTS.md rule 6).

## Epoch Drift (minimal version)

On load, compare `IMPRESSION.last_active_at` to now. If more than a
few minutes have passed (use a short threshold for demo purposes, a
constant clearly marked as demo-only), swap one specific sentence in
the room description for a gist version (e.g. exact detail about the
moth becomes vaguer). Deterministic, not random; the substitution
table is authored, not generated.

## Maintenance ritual (minimal version)

One button, "recalibrate," available once per session. Using it
resets `coherence` toward 100 but locks out one specific anomaly
(the RETROACTIVE glyph flicker) for the rest of the session. Not
using it leaves coherence to drift down from contradictions but keeps
anomalies visible. Make the trade-off legible in the legend, not in
the UI copy.

## Legend (ships now, ADA-relevant)

A persistent "?" control opens a plain-text panel: what each glyph
position means, what each verdict means (except SEALED, which is not
mentioned at all since it must not appear in this prototype), and a
non-color-dependent description of any color-coded element (use
pattern/border-style differences, not hue alone, for any state that is
currently only conveyed by color).

## Visual style

Minimal, dark background, monospace font, one visible mechanical
"gear" element (CSS animation, rotates faster/longer proportional to
how many candidate states the parser eliminated on that submission —
comment this mapping, do not explain it in UI). No images required;
this is a text/CSS prototype.

## Explicit non-goals for this prototype

No combat, no inventory, no multiple rooms, no dice mechanic, no
save-file export/import, no sound. Those are later milestones.

## Addendum: progressive reveal + broadened parser (post-playtest fix)

A first headless playtest (naive phrasing, not reverse-engineered from
source) showed two real problems: the initial render dumped all 4
sections (~163 words) before any player action, and several entirely
on-topic natural attempts — including "who was in the room last?" and
a *correct* theory ("I think it was the visitor") — returned CANNOT
BE ESTABLISHED purely for missing an unstated required keyword (the
theory parser required a redundant position word; the proposition
parser required the literal word "door").

Fix applied: the room's teaser sentence now names the physician and
the inscription so the player has words to ask about, but "The
physician" / "The inscription" / "A fragment" sections only render
once the corresponding flag is set (`physician_asked`,
`inscription_read`, two-of-four asked for the memory fragment) —
first render is the room alone (~73 words). The "door" requirement was
dropped from the position-claim template (a player says "the
physician was here" far more often than "...entered through the
door"); a bare position word with no named suspect now returns
REDACTED instead of CANNOT (a question isn't a proposition, but the
machine still engages); and the theory parser no longer requires a
position word, since the theory box already frames the question. A
re-run of the same naive playtest afterward hit zero CANNOT responses
across 7 attempts, with sections unlocking visibly as each one landed.

## Addendum 2: single-channel, oracular rebuild (scope correction)

The two-form, paragraph-and-badge version above was itself wrong: it
put a full text-adventure's worth of prose permanently on screen and
split input into "exhibit" vs "theory" boxes, which contradicts the
actual brief (one input mechanism, output as short as input, "no big
screen, just knobs, gears, rotating cylinders for text"). Rebuilt to:

- One input, hard `maxlength=48`, one output line that replaces
  itself each turn. No separate theory affordance: naming a suspect
  with a position word (a claim) is what triggers the delayed
  collapse of `last_in_room`, in the same channel as everything else.
- The glyph header lost its captions; it is now an unlabeled
  concatenated code, matching "a code at the top written in special
  characters," not a labeled stats readout.
- Scene content is delivered one short authored line at a time
  (physician and inscription reveal across a small fixed sequence as
  asked; window/moth are one-shot), never a standing paragraph.
- The mystery line ("SOMEONE WAS HERE BEFORE YOU ARRIVED.") is now
  explicitly re-askable ("who was here last?" with no suspect named
  recalls it) so the retroactive glyph flip is a player-triggered,
  repeatable moment, not a passive re-render: verified the same exact
  text returns with its glyph changed from unresolved to retroactive
  once a claim has collapsed the record.
- Known tradeoff: the unprompted "machine volunteers something" beat
  (the memory fragment, once two things have been asked about) can
  supersede that turn's actual answer. The superseded content isn't
  lost, asking again returns it, but it's a deliberate one-turn cost
  for a stronger beat, not an oversight.
- `DISPUTED` is unreachable in this one room by design, not omission:
  it needs a genuine second, conflicting source of evidence, which a
  one-witness room doesn't have. Left for a later room rather than
  forced in artificially.
- Physical components (dice, mechanical counters, printed artifacts)
  and the Lone Wolf-style RPG framing surfaced in project history are
  explicitly out of scope; see AGENTS.md rules 11-12. Web/phone
  browser only.
